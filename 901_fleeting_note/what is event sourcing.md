- code: /Users/re4388/project/personal/tsmc_code/java/event_sourcing/es0
- [GitHub - re4388/event\_sourcing0](https://github.com/re4388/event_sourcing0)




[Event Sourcing Explained - YouTube](https://www.youtube.com/watch?v=yFjzGRb8NOk)
- 什麼是事件溯源 (Event Sourcing)？
    - 應用程式狀態的所有變更都儲存為一系列不可變的事件。
    - 儲存導致該狀態的事件歷史。
    - 可通過重播事件序列來重建當前狀態。
- 與傳統基於狀態的系統比較：
    - 傳統系統直接更新當前狀態（例如：更新資料庫中的行）。



**傳統方式 vs 事件溯源**

```javascript


// 傳統方式：直接修改狀態
class BankAccount {
    constructor() {
        this.balance = 0;
    }
    
    deposit(amount) {
        this.balance += amount; // 直接修改狀態，遺失歷史
    }
}

// 事件溯源方式：記錄事件，重建狀態
class BankAccountES {
    constructor() {
        this.events = [];
        this.version = 0;
    }
    
    deposit(amount) {
        const event = {
            type: 'MoneyDeposited',
            amount: amount,
            timestamp: new Date(),
            version: this.version + 1
        };
        this.events.push(event);
        this.version++;
    }
    
    getBalance() {
        return this.events
            .filter(e => e.type === 'MoneyDeposited')
            .reduce((sum, e) => sum + e.amount, 0);
    }
}
```


# Read Model 讀取模型
- 讀取模型是為了優化查詢而存在的資料表示。
- 由於直接從事件儲存中查詢當前狀態或進行複雜的聚合查詢效率較低（需要重播事件），因此會維護一個單獨的、針對查詢進行優化的資料庫或視圖。
- 讀取模型通常是從事件流中**投影**（Project）出來的。
- AccountReadModel 實體儲存了帳戶的當前狀態（帳戶 ID、餘額、已處理的事件版本），它是一個傳統的關係型表格，方便快速查詢帳戶的最新餘額。



在事件溯源的上下文中，**Projection（投影）** 是指從事件流中衍生出來的**讀取模型**或**查詢模型**。它是 CQRS (Command Query Responsibility Segregation) 模式的重要組成部分。

## 投影的基本概念
```javascript
// 事件溯源中，我們只存儲事件
const events = [
    { type: 'UserRegistered', userId: '123', email: 'user@example.com' },
    { type: 'UserProfileUpdated', userId: '123', name: 'John Doe' },
    { type: 'OrderPlaced', userId: '123', orderId: 'order-456', amount: 100 },
    { type: 'OrderShipped', orderId: 'order-456', trackingId: 'track-789' }
];

// 但是前端需要查詢：「顯示用戶的基本資訊和訂單列表」
// 如果每次都要重播事件來計算，性能會很差
```

**投影的解決方案**
```javascript
// 用戶資訊投影
const userProjection = {
    userId: '123',
    email: 'user@example.com',
    name: 'John Doe',
    registeredAt: '2024-01-01T10:00:00Z',
    lastUpdated: '2024-01-02T15:30:00Z'
};

// 訂單摘要投影
const orderSummaryProjection = {
    userId: '123',
    totalOrders: 1,
    totalAmount: 100,
    lastOrderDate: '2024-01-03T09:15:00Z',
    orders: [
        {
            orderId: 'order-456',
            amount: 100,
            status: 'shipped',
            trackingId: 'track-789'
        }
    ]
};
```

## 投影的建立過程
**事件處理器 (Event Handler)**
```javascript
class UserProjectionHandler {
    constructor(database) {
        this.db = database;
    }
    
    async handle(event) {
        switch (event.type) {
            case 'UserRegistered':
                await this.createUserProjection(event);
                break;
                
            case 'UserProfileUpdated':
                await this.updateUserProjection(event);
                break;
                
            case 'OrderPlaced':
                await this.updateOrderSummary(event);
                break;
        }
    }
    
    async createUserProjection(event) {
        await this.db.query(`
            INSERT INTO user_projections (
                user_id, email, registered_at, last_updated
            ) VALUES ($1, $2, $3, $4)
        `, [
            event.userId, 
            event.data.email, 
            event.timestamp, 
            event.timestamp
        ]);
    }
    
    async updateUserProjection(event) {
        await this.db.query(`
            UPDATE user_projections 
            SET name = $1, last_updated = $2
            WHERE user_id = $3
        `, [event.data.name, event.timestamp, event.userId]);
    }
    
    async updateOrderSummary(event) {
        await this.db.query(`
            UPDATE user_projections 
            SET total_orders = total_orders + 1,
                total_amount = total_amount + $1,
                last_order_date = $2
            WHERE user_id = $3
        `, [event.data.amount, event.timestamp, event.userId]);
    }
}
```

## 多種投影同時存在
**不同的投影服務不同的查詢需求**
```javascript
// 1. 用戶管理投影（後台管理用）
class AdminUserProjection {
    async handle(event) {
        switch (event.type) {
            case 'UserRegistered':
                await this.db.query(`
                    INSERT INTO admin_user_view (
                        user_id, email, status, registered_at, 
                        login_count, last_login
                    ) VALUES ($1, $2, 'active', $3, 0, NULL)
                `, [event.userId, event.data.email, event.timestamp]);
                break;
                
            case 'UserLoggedIn':
                await this.db.query(`
                    UPDATE admin_user_view 
                    SET login_count = login_count + 1,
                        last_login = $1
                    WHERE user_id = $2
                `, [event.timestamp, event.userId]);
                break;
        }
    }
}

// 2. 分析報表投影（商業智能用）
class AnalyticsProjection {
    async handle(event) {
        switch (event.type) {
            case 'OrderPlaced':
                await this.updateDailySales(event);
                await this.updateUserSegment(event);
                break;
        }
    }
    
    async updateDailySales(event) {
        const date = event.timestamp.toISOString().split('T')[0];
        await this.db.query(`
            INSERT INTO daily_sales (date, total_amount, order_count)
            VALUES ($1, $2, 1)
            ON CONFLICT (date) DO UPDATE SET
                total_amount = daily_sales.total_amount + $2,
                order_count = daily_sales.order_count + 1
        `, [date, event.data.amount]);
    }
}

// 3. 搜索投影（Elasticsearch 用於全文搜索）
class SearchProjection {
    async handle(event) {
        switch (event.type) {
            case 'ProductAdded':
                await this.elasticClient.index({
                    index: 'products',
                    id: event.productId,
                    body: {
                        name: event.data.name,
                        description: event.data.description,
                        category: event.data.category,
                        price: event.data.price,
                        tags: event.data.tags
                    }
                });
                break;
        }
    }
}
```

## 投影的重建與更新

**投影重建（當業務邏輯變更時）**
```javascript
class ProjectionRebuilder {
    async rebuildUserProjections() {
        // 1. 清空現有投影
        await this.db.query('TRUNCATE TABLE user_projections');
        
        // 2. 重播所有相關事件
        const allEvents = await this.eventStore.getAllEvents([
            'UserRegistered', 
            'UserProfileUpdated', 
            'OrderPlaced'
        ]);
        
        const handler = new UserProjectionHandler(this.db);
        
        // 3. 依序處理每個事件
        for (const event of allEvents) {
            await handler.handle(event);
        }
        
        console.log('投影重建完成');
    }
    
    // 增量更新（從特定時間點開始）
    async updateProjectionsFrom(timestamp) {
        const newEvents = await this.eventStore.getEventsAfter(timestamp);
        const handler = new UserProjectionHandler(this.db);
        
        for (const event of newEvents) {
            await handler.handle(event);
        }
    }
}
```

## 投影的一致性保證
**最終一致性**
```javascript
class ProjectionManager {
    constructor() {
        this.eventHandlers = new Map();
        this.retryQueue = new Map();
    }
    
    async processEvent(event) {
        try {
            // 處理所有相關投影
            const handlers = this.eventHandlers.get(event.type) || [];
            
            await Promise.all(handlers.map(async handler => {
                try {
                    await handler.handle(event);
                } catch (error) {
                    // 單個投影失敗不影響其他投影
                    await this.addToRetryQueue(event, handler, error);
                }
            }));
            
        } catch (error) {
            console.error('處理事件失敗:', error);
        }
    }
    
    async addToRetryQueue(event, handler, error) {
        const retryKey = `${event.id}-${handler.name}`;
        const retryData = {
            event,
            handler,
            error,
            retryCount: (this.retryQueue.get(retryKey)?.retryCount || 0) + 1,
            nextRetryTime: Date.now() + (Math.pow(2, retryCount) * 1000)
        };
        
        this.retryQueue.set(retryKey, retryData);
    }
}
```

## 投影的查詢使用

**API 層直接查詢投影**

```javascript
// API 控制器
class UserController {
    async getUserProfile(userId) {
        // 直接查詢投影，不需要重播事件
        const userProfile = await this.db.query(`
            SELECT user_id, email, name, total_orders, total_amount
            FROM user_projections 
            WHERE user_id = $1
        `, [userId]);
        
        return userProfile[0];
    }
    
    async getUserOrders(userId, page = 1, limit = 10) {
        // 從專門的訂單投影查詢
        const orders = await this.db.query(`
            SELECT order_id, amount, status, created_at
            FROM user_order_projections 
            WHERE user_id = $1
            ORDER BY created_at DESC
            LIMIT $2 OFFSET $3
        `, [userId, limit, (page - 1) * limit]);
        
        return orders;
    }
}
```

## 投影的優勢

1. **查詢性能**：預先計算好的資料，查詢速度快
2. **靈活性**：可以針對不同需求建立不同的投影
3. **技術異構性**：不同投影可以使用不同的存儲技術
4. **可重建性**：業務邏輯變更時可以重新建立投影
5. **獨立演進**：每個投影可以獨立優化和維護

**總結：** 投影是事件溯源架構中將「事件日誌」轉換為「可查詢資料」的橋樑，它讓我們能夠享受事件溯源的好處（如完整的審計追蹤、時間旅行等），同時保持良好的查詢性能。




# 事件溯源確實會大幅增加實作複雜度...
這種複雜度只有在特定場景下才值得付出代價。讓我分享一些真正需要事件溯源的實際使用案例

## 真正需要事件溯源的場景

**1. 金融交易系統**
```javascript
// 銀行轉帳：需要完整審計追蹤
const events = [
    { type: 'TransferInitiated', from: 'acc-123', to: 'acc-456', amount: 1000 },
    { type: 'DebitProcessed', account: 'acc-123', amount: 1000 },
    { type: 'CreditProcessed', account: 'acc-456', amount: 1000 },
    { type: 'TransferCompleted', transferId: 'txn-789' }
];

// 監管要求：
// - 每筆交易的完整歷史記錄
// - 不能修改或刪除歷史記錄
// - 能重現任何時間點的帳戶狀態
// - 支援複雜的對帳流程
```

**2. 醫療記錄系統**
```javascript
// 病患治療歷程
const medicalEvents = [
    { type: 'PatientAdmitted', patientId: 'p-123', symptoms: ['fever', 'cough'] },
    { type: 'DiagnosisAdded', diagnosis: 'pneumonia', doctorId: 'dr-456' },
    { type: 'MedicationPrescribed', medication: 'amoxicillin', dosage: '500mg' },
    { type: 'TreatmentModified', newMedication: 'azithromycin', reason: 'allergy' }
];

// 醫療需求：
// - 永久保存治療歷史（法律要求）
// - 追蹤醫療決策的完整脈絡
// - 支援醫療研究和統計分析
// - 醫療責任追蹤
```

**3. 保險理賠系統**
```javascript
// 複雜的理賠流程
const claimEvents = [
    { type: 'ClaimSubmitted', claimId: 'claim-789', amount: 50000 },
    { type: 'DocumentsReceived', documents: ['police_report', 'medical_records'] },
    { type: 'InitialAssessmentCompleted', assessor: 'assessor-123', recommendation: 'investigate' },
    { type: 'FraudCheckCompleted', result: 'clear', investigator: 'inv-456' },
    { type: 'ClaimApproved', approvedAmount: 45000, adjustments: ['deductible'] },
    { type: 'PaymentIssued', paymentId: 'pay-999', amount: 45000 }
];

// 保險需求：
// - 理賠決策的完整證據鏈
// - 欺詐偵測需要歷史行為模式
// - 複雜的業務規則變更需要重新計算
// - 法律糾紛時需要完整的決策記錄
```

## 複雜度與價值的平衡

**適合事件溯源的特徵：**

- 業務領域本身就很複雜
- 需要完整的審計追蹤
- 業務規則經常變更
- 需要時間旅行功能
- 監管合規要求嚴格
- 系統的讀寫比例適中

**不適合的場景：**
```javascript
// 這種簡單 CRUD 不需要事件溯源
class BlogPost {
    constructor(title, content) {
        this.title = title;
        this.content = content;
        this.createdAt = new Date();
    }
    
    update(title, content) {
        this.title = title;
        this.content = content;
        this.updatedAt = new Date();
    }
}

// 用傳統方式就很好，不需要增加複雜度
```

## 實際採用事件溯源的公司案例

**Netflix - 用戶行為追蹤**
```javascript
// Netflix 使用事件溯源追蹤用戶行為來優化推薦算法
const userEvents = [
    { type: 'VideoStarted', userId: 'user-123', videoId: 'movie-456', timestamp: '...' },
    { type: 'VideoPaused', position: 1800 },
    { type: 'VideoSkipped', position: 2100, reason: 'user_action' },
    { type: 'VideoRated', rating: 4 }
];

// 需要事件溯源的原因：
// - 推薦算法需要完整的用戶行為歷史
// - A/B 測試需要重新計算不同的推薦結果
// - 需要分析用戶行為模式的長期趨勢
```

**Uber - 行程管理**
```javascript
// Uber 的行程狀態追蹤
const tripEvents = [
    { type: 'TripRequested', passengerId: 'p-123', location: {lat: 37.7, lng: -122.4} },
    { type: 'DriverAssigned', driverId: 'd-456', estimatedArrival: '5min' },
    { type: 'DriverArrived', actualLocation: {lat: 37.7001, lng: -122.4001} },
    { type: 'TripStarted', startLocation: {lat: 37.7, lng: -122.4} },
    { type: 'TripCompleted', endLocation: {lat: 37.8, lng: -122.3}, fare: 15.50 }
];

// 需要的原因：
// - 定價算法需要歷史數據
// - 司機和乘客的爭議處理
// - 監管報告和合規要求
// - 即時定位和狀態同步
```

## 漸進式採用策略

**不是全有全無的選擇**
```javascript
// 混合架構：只對關鍵業務使用事件溯源
class ECommerceSystem {
    // 核心業務：訂單處理用事件溯源
    async processOrder(command) {
        const events = this.orderAggregate.handle(command);
        await this.eventStore.append(events);
    }
    
    // 簡單業務：產品目錄用傳統 CRUD
    async updateProduct(productId, data) {
        await this.productRepository.update(productId, data);
    }
    
    // 配置數據：用傳統方式
    async updateSettings(settings) {
        await this.settingsRepository.save(settings);
    }
}
```

## 何時不應該使用事件溯源

**明確的反指標：**
- 團隊對領域驅動設計不熟悉
- 簡單的 CRUD 應用
- 主要是讀取密集的系統
- 即時查詢需求高於審計需求
- 團隊規模小，維護成本高
- 業務邏輯相對簡單穩定

**替代方案：**
```javascript
// 簡單審計日誌就足夠
class AuditLogger {
    async logChange(entity, oldValue, newValue, userId) {
        await this.auditLog.insert({
            table: entity.constructor.name,
            entityId: entity.id,
            changes: this.diffFields(oldValue, newValue),
            userId,
            timestamp: new Date()
        });
    }
}

// 或使用資料庫內建的變更追蹤功能
// PostgreSQL 的 temporal tables
// SQL Server 的 Change Data Capture
```

**總結觀點：** 事件溯源適合那些業務本身就很複雜、需要嚴格審計追蹤、且團隊有足夠技術能力的場景。對於大多數應用來說，傳統的 CRUD + 簡單審計日誌就已經足夠。關鍵是要評估複雜度增加的成本是否能被業務價值所證明。

如果你現在面臨的問題用更簡單的方法就能解決，那就不需要事件溯源。技術選型的黃金法則是：選擇能解決問題的最簡單方案。



# 事件溯源如何解決一致性問題

**1. 樂觀併發控制**
```javascript
// 使用版本號防止併發衝突
async function processCommand(accountId, command, expectedVersion) {
    const currentEvents = await getEvents(accountId);
    const currentVersion = currentEvents.length;
    
    if (currentVersion !== expectedVersion) {
        throw new ConcurrencyError('版本衝突，請重試');
    }
    
    const newEvent = processBusinessLogic(command, currentEvents);
    await appendEvent(accountId, newEvent, expectedVersion + 1);
}

// 客戶端重試機制
async function retryableCommand(accountId, command) {
    let retries = 0;
    while (retries < 3) {
        try {
            const currentVersion = await getCurrentVersion(accountId);
            await processCommand(accountId, command, currentVersion);
            break;
        } catch (error) {
            if (error instanceof ConcurrencyError) {
                retries++;
                await delay(Math.pow(2, retries) * 100); // 指數退避
            } else {
                throw error;
            }
        }
    }
}
```

**2. 事件的不可變性**
```sql
-- 事件表設計：只能插入，不能修改
CREATE TABLE events (
    id UUID PRIMARY KEY,
    aggregate_id UUID NOT NULL,
    event_type VARCHAR(100) NOT NULL,
    event_data JSONB NOT NULL,
    version INTEGER NOT NULL,
    timestamp TIMESTAMP DEFAULT NOW(),
    UNIQUE(aggregate_id, version)  -- 確保版本順序
);

-- 只有插入操作，沒有 UPDATE 或 DELETE
INSERT INTO events (id, aggregate_id, event_type, event_data, version)
VALUES (gen_random_uuid(), 'account-123', 'MoneyDeposited', 
        '{"amount": 1000}', 5);
```

**3. 最終一致性的實現**
```javascript
// 事件處理器確保最終一致性
class AccountProjectionHandler {
    async handle(event) {
        switch (event.type) {
            case 'MoneyDeposited':
                await this.updateAccountBalance(
                    event.aggregateId, 
                    event.data.amount
                );
                break;
            case 'MoneyWithdrawn':
                await this.updateAccountBalance(
                    event.aggregateId, 
                    -event.data.amount
                );
                break;
        }
    }
    
    async updateAccountBalance(accountId, amount) {
        // 更新讀取模型，可能會有短暫的不一致
        await db.query(`
            UPDATE account_projections 
            SET balance = balance + $1,
                last_updated = NOW()
            WHERE account_id = $2
        `, [amount, accountId]);
    }
}
```

## 處理分散式系統中的一致性

**Saga 模式與事件溯源結合**
```javascript
// 訂單處理 Saga
class OrderProcessingSaga {
    async handle(event) {
        switch (event.type) {
            case 'OrderPlaced':
                // 發送命令到庫存服務
                await this.sendCommand('inventory', {
                    type: 'ReserveItems',
                    orderId: event.data.orderId,
                    items: event.data.items
                });
                break;
                
            case 'ItemsReserved':
                // 發送命令到支付服務
                await this.sendCommand('payment', {
                    type: 'ProcessPayment',
                    orderId: event.data.orderId,
                    amount: event.data.amount
                });
                break;
                
            case 'PaymentFailed':
                // 補償操作：釋放庫存
                await this.sendCommand('inventory', {
                    type: 'ReleaseItems',
                    orderId: event.data.orderId
                });
                break;
        }
    }
}
```

**事件溯源的 ACID 替代方案**
```javascript
// 原子性：通過事件存儲的原子寫入
async function atomicEventAppend(events) {
    const transaction = await db.beginTransaction();
    try {
        for (const event of events) {
            await transaction.query(`
                INSERT INTO events (aggregate_id, event_type, event_data, version)
                VALUES ($1, $2, $3, $4)
            `, [event.aggregateId, event.type, event.data, event.version]);
        }
        await transaction.commit();
    } catch (error) {
        await transaction.rollback();
        throw error;
    }
}

// 一致性：通過事件處理器保證
class ConsistencyChecker {
    async validateBusinessRules(events) {
        const account = this.replayEvents(events);
        
        // 業務規則檢查
        if (account.balance < 0) {
            throw new BusinessRuleViolation('帳戶餘額不能為負');
        }
        
        return true;
    }
}
```

## 快照優化與性能

```javascript
// 定期建立快照避免重播太多事件
class SnapshotManager {
    async createSnapshot(aggregateId) {
        const events = await getEvents(aggregateId);
        const currentState = replayEvents(events);
        
        await saveSnapshot({
            aggregateId,
            state: currentState,
            version: events.length,
            timestamp: new Date()
        });
    }
    
    async loadAggregate(aggregateId) {
        const snapshot = await getLatestSnapshot(aggregateId);
        const eventsAfterSnapshot = await getEventsAfterVersion(
            aggregateId, 
            snapshot.version
        );
        
        return replayEventsFromSnapshot(snapshot.state, eventsAfterSnapshot);
    }
}
```

## 事件溯源的一致性優勢

**1. 審計追蹤**
- 每個狀態變更都有完整記錄
- 可以重現任何時間點的狀態
- 自然支援法規遵循需求
**2. 調試與錯誤恢復**
```javascript
// 可以重現問題發生時的確切狀態
async function debugAccount(accountId, problemTimestamp) {
    const eventsBeforeProblem = await getEventsBeforeTimestamp(
        accountId, 
        problemTimestamp
    );
    
    const stateAtProblem = replayEvents(eventsBeforeProblem);
    console.log('問題發生時的狀態:', stateAtProblem);
}
```

- 優點：
    - 審計 (Auditing)：提供完整的變更歷史。
    - 偵錯 (Debugging)：更容易理解狀態是如何達成的。
    - 時間點查詢 (Temporal Queries)：可以重建任何時間點的狀態。
    - 解耦 (Decoupling)：事件可以被多個服務消費。
    - 經常與命令查詢職責分離 CQRS (Command Query Responsibility Segregation) 一起使用。
- 缺點：
    - 複雜性 (Complexity)：比傳統的 CRUD 實現更複雜。
    - 查詢 (Querying)：直接查詢事件流可能很困難；需另外建立讀取模型 (read models)。
    - 事件模式演進 (Event Schema Evolution)：處理事件模式隨時間的變化。
    - 儲存 (Storage)：事件流可能需要大量的儲存空間。
- 相關概念：
    - CQRS (命令查詢職責分離)：分離讀取和寫入操作。事件溯源很適合用於寫入端。
    - DDD (領域驅動設計 Domain-Driven Design)：事件通常代表領域事件。
    - 最終一致性 (Eventual Consistency)：從事件衍生的讀取模型可能無法立即與最新事件同步。
    - 樂觀並發控制 (Optimistic Concurrency)：處理事件流並發更新的技術。