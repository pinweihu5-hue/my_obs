
以下是高級 Spring Boot 開發者應該掌握的 10 個進階模式：

## 1. 事件驅動架構模式 (Event-Driven Architecture)

```java fold

// 定義領域事件
@Component
public class OrderEvent {
    private final String orderId;
    private final OrderStatus status;
    private final LocalDateTime timestamp;

    public OrderEvent(String orderId, OrderStatus status) {
        this.orderId = orderId;
        this.status = status;
        this.timestamp = LocalDateTime.now();
    }
	public String getOrderId() { return orderId; }
	public OrderStatus getStatus() { return status; }
	public LocalDateTime getTimestamp() { return timestamp; }
}

// 事件發布者
@Service
public class OrderService {
    // ApplicationEventPublisher 是 Spring 內建的介面，Spring Boot 會自動把它注入到你的 Service
    private final ApplicationEventPublisher eventPublisher;

    public OrderService(ApplicationEventPublisher eventPublisher) {
        this.eventPublisher = eventPublisher;
    }

    @Transactional
    public void createOrder(Order order) {
        
        // 保存訂單
        orderRepository.save(order);

        // 發布事件
        OrderEvent orderEvent = new OrderEvent(order.getId(), OrderStatus.CREATED)
        eventPublisher.publishEvent(orderEvent);
    }
}

// 異步事件監聽器
@Component
public class OrderEventListener {

    @Async("eventTaskExecutor")
    @EventListener
    @TransactionalEventListener(phase = TransactionPhase.AFTER_COMMIT)
    public void handleOrderCreated(OrderEvent event) {
        
        // 發送確認郵件
        emailService.sendOrderConfirmation(event.getOrderId());

        // 更新庫存
        inventoryService.reserveItems(event.getOrderId());

        // 通知物流系統
        logisticsService.scheduleDelivery(event.getOrderId());
    }

    @EventListener
    @Order(1) // 控制執行順序, 當多個事件監聽器都監聽同一種事件時，這個註解控制它們的執行順序。數字越小，越先執行
    public void handleOrderStatusChange(OrderEvent event) {
        auditService.logOrderEvent(event);
    }
}

// 配置異步執行
@Configuration
@EnableAsync
public class AsyncConfig {

    @Bean(name = "eventTaskExecutor")
    public TaskExecutor eventTaskExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(5);
        executor.setMaxPoolSize(10);
        executor.setQueueCapacity(100);
        executor.setThreadNamePrefix("event-");
        executor.initialize();
        return executor;
    }
}


```

## 2. CQRS (Command Query Responsibility Segregation) 模式

```java fold

// Command 模型
@Component
public class OrderCommand {

    // @Transactional 預設會用 主資料庫 / write data source（因為它是 @Primary）
    @Transactional
    public void createOrder(CreateOrderRequest request) {
        Order order = Order.builder()
            .customerId(request.getCustomerId())
            .items(request.getItems())
            .build();

        // 這邊要確保 orderWriteRepository 的 EntityManager 是用主庫（write）。
        orderWriteRepository.save(order);

        // 同步呼叫 updateOrderReadModel(order) → 這在讀寫分離架構裡可能會有資料延遲同步的問題（讀庫可能還沒更新）
        // 如果這個方法直接操作讀庫，可能會在主庫 commit 之前查不到最新資料。
        // 建議 改為異步事件處理
        // 避免直接在 Command 裡操作讀庫，防止資料延遲造成讀寫衝突
        updateOrderReadModel(order);
    }

    @TransactionalEventListener(phase = AFTER_COMMIT)
    public void updateOrderStatus(String orderId, OrderStatus status) {
        Order order = orderWriteRepository.findById(orderId)
            .orElseThrow(() -> new OrderNotFoundException(orderId));

        order.updateStatus(status);
        orderWriteRepository.save(order);

        // 異步更新讀取模型, 避免阻塞
        eventPublisher.publishEvent(new OrderStatusChangedEvent(orderId, status));
    }
}

// Query 模型
// 如果使用資料庫複製（如 MySQL 主從複製），資料延遲會讓新建 / 更新的資料短時間內查不到，這要在系統設計上允許或透過特定查詢強制走主庫
@Component
@ReadOnlyTransaction
public class OrderQuery {

    public OrderSummaryDto getOrderSummary(String orderId) {
        return orderReadRepository.findOrderSummaryById(orderId);
    }

    public Page<OrderListDto> getOrdersByCustomer(String customerId, Pageable pageable) {
        return orderReadRepository.findByCustomerId(customerId, pageable);
    }

    public List<OrderStatisticsDto> getOrderStatistics(LocalDate from, LocalDate to) {
        return orderReadRepository.getOrderStatistics(from, to);
    }
}

// 讀寫分離的數據源配置
@Configuration
public class DatabaseConfig {

    @Bean
    @Primary
    @ConfigurationProperties("spring.datasource.write")
    public DataSource writeDataSource() {
        return DataSourceBuilder.create().build();
    }

    @Bean
    @ConfigurationProperties("spring.datasource.read")
    public DataSource readDataSource() {
        return DataSourceBuilder.create().build();
    }

    @Bean
    @Primary
    public JpaTransactionManager writeTransactionManager(
            @Qualifier("writeDataSource") DataSource dataSource) {
        return new JpaTransactionManager(writeEntityManagerFactory(dataSource).getObject());
    }
}

@Bean
public JpaTransactionManager readTransactionManager(
        @Qualifier("readEntityManagerFactory") EntityManagerFactory emf) {
    return new JpaTransactionManager(emf);
}

@Bean
public LocalContainerEntityManagerFactoryBean writeEntityManagerFactory(
        @Qualifier("writeDataSource") DataSource dataSource) {
    // configure packagesToScan, jpaVendorAdapter...
}

@Bean
public LocalContainerEntityManagerFactoryBean readEntityManagerFactory(
        @Qualifier("readDataSource") DataSource dataSource) {
    // configure packagesToScan, jpaVendorAdapter...
}


// 自定義讀取事務註解
@Target({ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Transactional(transactionManager = "readTransactionManager", readOnly = true)
public @interface ReadOnlyTransaction {
}



```

## 3. Saga 分散式交易模式

```java
// Saga 編排器
@Component
public class OrderProcessingSaga {
    
    public void processOrder(OrderCreatedEvent event) {
        SagaTransaction saga = SagaTransaction.builder()
            .sagaId(UUID.randomUUID().toString())
            .orderId(event.getOrderId())
            .build();
            
        try {
            // 步驟 1: 驗證付款
            PaymentResult paymentResult = paymentService.processPayment(
                event.getOrderId(), event.getAmount());
            saga.addCompensation(() -> paymentService.refund(paymentResult.getTransactionId()));
            
            // 步驟 2: 預留庫存
            ReservationResult reservation = inventoryService.reserveItems(
                event.getOrderId(), event.getItems());
            saga.addCompensation(() -> inventoryService.releaseReservation(reservation.getId()));
            
            // 步驟 3: 安排配送
            DeliveryResult delivery = deliveryService.scheduleDelivery(
                event.getOrderId(), event.getDeliveryAddress());
            saga.addCompensation(() -> deliveryService.cancelDelivery(delivery.getId()));
            
            // 所有步驟成功，確認訂單
            orderService.confirmOrder(event.getOrderId());
            
        } catch (Exception e) {
            // 執行補償操作
            saga.compensate();
            orderService.rejectOrder(event.getOrderId(), e.getMessage());
        }
    }
}

// Saga 事務管理器
@Component
public class SagaTransaction {
    private final List<Runnable> compensations = new ArrayList<>();
    
    public void addCompensation(Runnable compensation) {
        compensations.add(compensation);
    }
    
    public void compensate() {
        // 反向執行補償操作
        Collections.reverse(compensations);
        compensations.forEach(compensation -> {
            try {
                compensation.run();
            } catch (Exception e) {
                log.error("補償操作失敗", e);
                // 可能需要人工介入
            }
        });
    }
}

// 使用狀態機管理 Saga
@Configuration
@EnableStateMachine
public class SagaStateMachineConfig {
    
    @Bean
    public StateMachine<SagaState, SagaEvent> buildMachine() throws Exception {
        StateMachineBuilder.Builder<SagaState, SagaEvent> builder = 
            StateMachineBuilder.builder();
            
        builder.configureStates()
            .withStates()
            .initial(SagaState.STARTED)
            .state(SagaState.PAYMENT_PROCESSED)
            .state(SagaState.INVENTORY_RESERVED)
            .state(SagaState.DELIVERY_SCHEDULED)
            .end(SagaState.COMPLETED)
            .end(SagaState.COMPENSATED);
            
        builder.configureTransitions()
            .withExternal()
                .source(SagaState.STARTED)
                .target(SagaState.PAYMENT_PROCESSED)
                .event(SagaEvent.PAYMENT_SUCCESS)
                .action(paymentAction())
            .and()
            .withExternal()
                .source(SagaState.PAYMENT_PROCESSED)
                .target(SagaState.COMPENSATED)
                .event(SagaEvent.PAYMENT_FAILED)
                .action(compensateAction());
                
        return builder.build();
    }
}
```

## 4. 多租戶架構模式 (Multi-Tenancy)

```java
// 租戶上下文
public class TenantContext {
    private static final ThreadLocal<String> currentTenant = new ThreadLocal<>();
    
    public static void setTenant(String tenant) {
        currentTenant.set(tenant);
    }
    
    public static String getTenant() {
        return currentTenant.get();
    }
    
    public static void clear() {
        currentTenant.remove();
    }
}

// 多租戶攔截器
@Component
public class TenantInterceptor implements HandlerInterceptor {
    
    @Override
    public boolean preHandle(HttpServletRequest request, 
                           HttpServletResponse response, 
                           Object handler) throws Exception {
        
        String tenantId = extractTenantId(request);
        if (tenantId == null) {
            response.setStatus(HttpStatus.BAD_REQUEST.value());
            return false;
        }
        
        TenantContext.setTenant(tenantId);
        return true;
    }
    
    @Override
    public void afterCompletion(HttpServletRequest request, 
                              HttpServletResponse response, 
                              Object handler, Exception ex) {
        TenantContext.clear();
    }
    
    private String extractTenantId(HttpServletRequest request) {
        // 從 Header 提取
        String tenantId = request.getHeader("X-Tenant-ID");
        if (tenantId != null) return tenantId;
        
        // 從子域名提取
        String serverName = request.getServerName();
        if (serverName.contains(".")) {
            return serverName.substring(0, serverName.indexOf('.'));
        }
        
        return null;
    }
}

// 多租戶數據源路由
@Component
public class TenantRoutingDataSource extends AbstractRoutingDataSource {
    
    @Override
    protected Object determineCurrentLookupKey() {
        return TenantContext.getTenant();
    }
}

// JPA 多租戶配置
@Configuration
@EnableJpaRepositories
public class MultiTenantJpaConfig {
    
    @Bean
    @Primary
    public DataSource dataSource() {
        TenantRoutingDataSource routingDataSource = new TenantRoutingDataSource();
        
        Map<Object, Object> dataSourceMap = new HashMap<>();
        dataSourceMap.put("tenant1", createDataSource("tenant1"));
        dataSourceMap.put("tenant2", createDataSource("tenant2"));
        
        routingDataSource.setTargetDataSources(dataSourceMap);
        routingDataSource.setDefaultTargetDataSource(createDataSource("default"));
        
        return routingDataSource;
    }
    
    @Bean
    public MultiTenantConnectionProvider multiTenantConnectionProvider() {
        return new DataSourceBasedMultiTenantConnectionProvider();
    }
    
    @Bean
    public CurrentTenantIdentifierResolver tenantIdentifierResolver() {
        return new TenantIdentifierResolver();
    }
}

// 租戶級別的緩存
@Service
public class TenantAwareCacheService {
    
    @Cacheable(value = "userCache", key = "#tenantId + '_' + #userId")
    public User getUser(String tenantId, String userId) {
        TenantContext.setTenant(tenantId);
        return userRepository.findById(userId).orElse(null);
    }
    
    @CacheEvict(value = "userCache", key = "#tenantId + '_' + #userId")
    public void evictUser(String tenantId, String userId) {
        // 清除特定租戶的緩存
    }
}
```

## 5. 斷路器模式 (Circuit Breaker Pattern)

```java fold

// 自定義斷路器實現
@Component
public class CircuitBreaker {
    private final int failureThreshold;   // 失敗次數上限
    private final long timeoutDuration;   // 超時（目前程式沒用到，可擴充）
    private final long retryTimePeriod;   // 從 OPEN 狀態到 HALF_OPEN 的等待時間
    
    private int failureCount = 0;         // 當前失敗次數
    private long lastFailureTime;         // 最近一次失敗時間

    private int failureCount = 0;
    private long lastFailureTime;
    private CircuitBreakerState state = CircuitBreakerState.CLOSED;

    public enum CircuitBreakerState {
        CLOSED, OPEN, HALF_OPEN
    }

    public <T> T execute(Supplier<T> operation, Supplier<T> fallback) {
        if (state == CircuitBreakerState.OPEN) {
            /**
            OPEN（打開）
            表示服務目前處於失敗狀態
            所有請求直接走 fallback（不再嘗試真正的操作）
            當 lastFailureTime + retryTimePeriod 到了，會進入 HALF_OPEN
            */
            if (System.currentTimeMillis() - lastFailureTime < retryTimePeriod) {
                return fallback.get();
            } else {
                state = CircuitBreakerState.HALF_OPEN;
            }
        }


        try {
            /**
            CLOSED（閉合）
            正常狀態
            請求會直接執行 operation
            如果請求成功，failureCount 會被重置
            如果失敗，失敗計數 failureCount 增加
            
            HALF_OPEN（半開）
            系統允許一次請求去試探外部服務是否恢復
            成功的話回到 CLOSED
            失敗的話回到 OPEN 並重新計時
            */
            T result = operation.get();
            reset();
            return result;
        } catch (Exception e) {
            recordFailure();
            return fallback.get();
        }
    }

    private void recordFailure() {
        failureCount++;
        lastFailureTime = System.currentTimeMillis();

        if (failureCount >= failureThreshold) {
            state = CircuitBreakerState.OPEN;
        }
    }

    private void reset() {
        failureCount = 0;
        state = CircuitBreakerState.CLOSED;
    }
}

@Service
public class ExternalApiService {
    private final CircuitBreaker circuitBreaker;

    public ExternalApiService(CircuitBreaker circuitBreaker) {
        this.circuitBreaker = circuitBreaker;
    }

    public String callExternalService() {
        return circuitBreaker.execute(
            // 正常執行邏輯
            () -> {
                // 模擬外部 API 呼叫
                if (Math.random() < 0.7) {
                    throw new RuntimeException("API 失敗");
                }
                return "API 成功回應";
            },
            // fallback 預設值
            () -> "使用預設回應"
        );
    }
}

public class Demo {
    public static void main(String[] args) {
        CircuitBreaker breaker = new CircuitBreaker(3, 5000, 3000);
        ExternalApiService service = new ExternalApiService(breaker);

        for (int i = 0; i < 10; i++) {
            System.out.println(service.callExternalService());
            try { Thread.sleep(1000); } catch (InterruptedException ignored) {}
        }
    }
}


// 使用 Resilience4j
@Configuration
public class Resilience4jConfig {

    @Bean
    public CircuitBreakerConfig circuitBreakerConfig() {
        return CircuitBreakerConfig.custom()
            .failureRateThreshold(50)
            .waitDurationInOpenState(Duration.ofSeconds(30))
            .slidingWindowSize(10)
            .minimumNumberOfCalls(5)
            .permittedNumberOfCallsInHalfOpenState(3)
            .build();
    }

    @Bean
    public RetryConfig retryConfig() {
        return RetryConfig.custom()
            .maxAttempts(3)
            .waitDuration(Duration.ofSeconds(2))
            .retryOnException(throwable -> throwable instanceof IOException)
            .build();
    }

    @Bean
    public BulkheadConfig bulkheadConfig() {
        return BulkheadConfig.custom()
            .maxConcurrentCalls(10)
            .maxWaitDuration(Duration.ofSeconds(1))
            .build();
    }
}

// 服務層實現
@Service
public class ExternalApiService {

    @CircuitBreaker(name = "external-api", fallbackMethod = "fallbackResponse")
    @Retry(name = "external-api")
    @Bulkhead(name = "external-api")
    @TimeLimiter(name = "external-api")
    public CompletableFuture<ApiResponse> callExternalApi(String request) {
        return CompletableFuture.supplyAsync(() -> {
            // 調用外部 API
            return externalApiClient.call(request);
        });
    }

    public CompletableFuture<ApiResponse> fallbackResponse(String request, Exception ex) {
        log.warn("External API call failed, using fallback", ex);
        return CompletableFuture.completedFuture(
            ApiResponse.builder()
                .status("fallback")
                .message("Service temporarily unavailable")
                .build()
        );
    }
}



```

## 6. 策略模式與工廠模式結合

```java fold
// 策略介面
public interface PaymentStrategy {
    PaymentResult processPayment(PaymentRequest request);
    boolean supports(PaymentType type);
}

// 具體策略實現
@Component
public class CreditCardPaymentStrategy implements PaymentStrategy {
    
    @Override
    public PaymentResult processPayment(PaymentRequest request) {
        // 信用卡支付邏輯
        CreditCardPayment payment = (CreditCardPayment) request;
        
        // 驗證信用卡
        if (!validateCreditCard(payment.getCardNumber())) {
            return PaymentResult.failure("Invalid credit card");
        }
        
        // 處理支付
        String transactionId = creditCardProcessor.charge(
            payment.getAmount(), 
            payment.getCardNumber()
        );
        
        return PaymentResult.success(transactionId);
    }
    
    @Override
    public boolean supports(PaymentType type) {
        return PaymentType.CREDIT_CARD == type;
    }
}

@Component
public class WalletPaymentStrategy implements PaymentStrategy {
    
    @Override
    public PaymentResult processPayment(PaymentRequest request) {
        WalletPayment payment = (WalletPayment) request;
        
        // 檢查錢包餘額
        if (!walletService.hasSufficientBalance(payment.getWalletId(), payment.getAmount())) {
            return PaymentResult.failure("Insufficient wallet balance");
        }
        
        // 扣款
        String transactionId = walletService.debit(payment.getWalletId(), payment.getAmount());
        
        return PaymentResult.success(transactionId);
    }
    
    @Override
    public boolean supports(PaymentType type) {
        return PaymentType.WALLET == type;
    }
}

// 策略工廠
@Component
public class PaymentStrategyFactory {
    private final List<PaymentStrategy> strategies;
    
    public PaymentStrategyFactory(List<PaymentStrategy> strategies) {
        this.strategies = strategies;
    }
    
    public PaymentStrategy getStrategy(PaymentType type) {
        return strategies.stream()
            .filter(strategy -> strategy.supports(type))
            .findFirst()
            .orElseThrow(() -> new UnsupportedPaymentTypeException(type));
    }
}

// 支付服務
@Service
@Transactional
public class PaymentService {
    private final PaymentStrategyFactory strategyFactory;
    private final PaymentAuditService auditService;
    
    public PaymentResult processPayment(PaymentRequest request) {
        PaymentStrategy strategy = strategyFactory.getStrategy(request.getType());
        
        // 記錄支付嘗試
        auditService.logPaymentAttempt(request);
        
        try {
            PaymentResult result = strategy.processPayment(request);
            
            // 記錄支付結果
            auditService.logPaymentResult(request, result);
            
            if (result.isSuccess()) {
                // 發布支付成功事件
                eventPublisher.publishEvent(new PaymentSuccessEvent(result.getTransactionId()));
            }
            
            return result;
        } catch (Exception e) {
            auditService.logPaymentError(request, e);
            throw new PaymentProcessingException("Payment processing failed", e);
        }
    }
}
```

## 7. 異步處理和消息佇列模式

```java fold

// RabbitMQ 的訂單處理機制


// 消息處理器
@Component
public class OrderMessageHandler {

    @RabbitListener(queues = "order.processing.queue") // 從 order.processing.queue 消費消息
    @RetryableTopic(  // 自動重試（3 次），1 秒延遲，每次乘 2 倍，遇到 OrderProcessingException 才會重試
        attempts = "3",
        backoff = @Backoff(delay = 1000, multiplier = 2.0),
        dltStrategy = DltStrategy.FAIL_ON_ERROR,
        include = {OrderProcessingException.class}
    )
    public void handleOrderProcessing(OrderMessage orderMessage) { // PS: 可在 OrderMessage 加上 correlationId 或 traceId 方便追蹤。
        try {
            orderProcessingService.processOrder(orderMessage.getOrderId());
        } catch (OrderProcessingException e) {
            // 可重試錯誤 → 記錄並讓 Spring AMQP 重試
            log.error("訂單處理失敗，將重試: {}", orderMessage.getOrderId(), e);
            throw e;
        } catch (Exception e) {
            // 不可重試的錯誤，發送到死信佇列
            log.error("訂單處理發生不可重試錯誤: {}", orderMessage.getOrderId(), e);
            
            // 最好用 Spring AMQP 提供的 RabbitTemplate 實際推送
            sendToDeadLetterQueue(orderMessage, e);
        }
    }

    // 處理死信佇列（Dead Letter Topic）的最終失敗消息
    @DltHandler
    public void handleDlt(OrderMessage orderMessage, Exception exception) {
        log.error("訂單消息處理最終失敗，需要人工處理: {}", orderMessage.getOrderId(), exception);

        // 記錄到數據庫供人工處理
        failedMessageService.recordFailedMessage(orderMessage, exception);

        // 發送告警
        alertService.sendAlert("Order processing failed", orderMessage, exception);
    }
}

// 異步任務配置
// 可加上 監控 metrics（例如 Micrometer）以追蹤執行緒池使用率。
// 根據服務需求調整 queueCapacity 與 corePoolSize，避免 OOM。
@Configuration
@EnableAsync
public class AsyncConfig implements AsyncConfigurer {

    @Override
    @Bean(name = "taskExecutor") // 適合輕量高併發的任務
    public Executor getAsyncExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(10);
        executor.setMaxPoolSize(50);
        executor.setQueueCapacity(200);
        executor.setThreadNamePrefix("async-task-");
        executor.setRejectedExecutionHandler(new ThreadPoolExecutor.CallerRunsPolicy()); // 使用 CallerRunsPolicy 避免丟棄任務（超載時呼叫者執行）
        executor.initialize();
        return executor;
    }

    @Override
    public AsyncUncaughtExceptionHandler getAsyncUncaughtExceptionHandler() {
        return new SimpleAsyncUncaughtExceptionHandler();
    }

    @Bean(name = "heavyTaskExecutor") // 適合耗時或 CPU 密集任務
    public Executor heavyTaskExecutor() { 
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(2);
        executor.setMaxPoolSize(5);
        executor.setQueueCapacity(50);
        executor.setThreadNamePrefix("heavy-task-");
        executor.initialize();
        return executor;
    }
}

// 批量處理服務
@Service
public class BatchProcessingService {

    // 讓批量處理不阻塞主線程
    @Async("heavyTaskExecutor")
    public CompletableFuture<BatchResult> processBatch(List<OrderId> orderIds) {
        log.info("開始批量處理 {} 個訂單", orderIds.size());

        List<OrderProcessingResult> results = orderIds.parallelStream() // 使用 parallelStream() 提升 CPU 密集型批量處理性能
            .map(this::processOrderSafely) // 包裝錯誤，避免單筆失敗導致整批失敗
            .collect(Collectors.toList());

        BatchResult batchResult = BatchResult.builder()
            .totalProcessed(results.size())
            .successCount(results.stream().mapToInt(r -> r.isSuccess() ? 1 : 0).sum())
            .failureCount(results.stream().mapToInt(r -> r.isSuccess() ? 0 : 1).sum())
            .results(results)
            .build();

        log.info("批量處理完成: {}", batchResult);
        // CompletableFuture 方便後續鏈式處理（e.g., .thenAccept()）
        return CompletableFuture.completedFuture(batchResult);
    }

    /*
    改進建議:
    - parallelStream 與 @Async 可能雙重並行，要小心 CPU 資源耗盡
    - 如果批量數據量大，可改用 stream() 搭配自訂的 ExecutorService。
    - 失敗記錄可集中寫入 DB，而不是分散多次 I / O。
    - 可以加入批量重試機制（例如針對暫時性錯誤的訂單再重試一次）。
    */
    private OrderProcessingResult processOrderSafely(OrderId orderId) {
        try {
            orderProcessingService.processOrder(orderId);
            return OrderProcessingResult.success(orderId);
        } catch (Exception e) {
            log.error("處理訂單失敗: {}", orderId, e);
            return OrderProcessingResult.failure(orderId, e.getMessage());
        }
    }
}



```

## 8. 緩存策略模式

```java
// 多層緩存管理
@Service
public class MultiLevelCacheService {
    
    private final RedisTemplate<String, Object> redisTemplate;
    private final Cache<String, Object> localCache;
    
    public MultiLevelCacheService(RedisTemplate<String, Object> redisTemplate) {
        this.redisTemplate = redisTemplate;
        this.localCache = Caffeine.newBuilder()
            .maximumSize(1000)
            .expireAfterWrite(Duration.ofMinutes(5))
            .removalListener(this::onCacheRemoval)
            .build();
    }
    
    public <T> Optional<T> get(String key, Class<T> type) {
        // 首先嘗試本地緩存
        T value = (T) localCache.getIfPresent(key);
        if (value != null) {
            return Optional.of(value);
        }
        
        // 然後嘗試 Redis
        value = (T) redisTemplate.opsForValue().get(key);
        if (value != null) {
            // 回填本地緩存
            localCache.put(key, value);
            return Optional.of(value);
        }
        
        return Optional.empty();
    }
    
    public void put(String key, Object value, Duration ttl) {
        // 同時寫入兩層緩存
        localCache.put(key, value);
        redisTemplate.opsForValue().set(key, value, ttl);
    }
    
    public void evict(String key) {
        localCache.invalidate(key);
        redisTemplate.delete(key);
        
        // 通知其他節點清除本地緩存
        redisTemplate.convertAndSend("cache.eviction", key);
    }
    
    @EventListener
    public void handleCacheEviction(String key) {
        localCache.invalidate(key);
    }
}

// 智能緩存更新策略
@Component
public class SmartCacheManager {
    
    @CachePut(value = "users", key = "#user.id", condition = "#user.lastModified != null")
    public User updateUser(User user) {
        // 只有在數據真正變更時才更新緩存
        User savedUser = userRepository.save(user);
        
        // 異步預熱相關緩存
        CompletableFuture.runAsync(() -> preheatRelatedCaches(savedUser));
        
        return savedUser;
    }
    
    @Cacheable(value = "users", key = "#id", unless = "#result == null")
    public User getUser(String id) {
        return userRepository.findById(id).orElse(null);
    }
    
    // 預熱策略
    @Async
    public void preheatRelatedCaches(User user) {
        // 預熱用戶的訂單緩存
        orderService.getUserOrders(user.getId());
        
        // 預熱用戶偏好設定
        preferenceService.getUserPreferences(user.getId());
    }
    
    // 基於標籤的批量清除
    @CacheEvict(value = "users", allEntries = true, condition = "#tag == 'user-data'")
    public void evictByTag(String tag) {
        log.info("清除標籤為 {} 的所有緩存", tag);
    }
}

// 分散式緩存同步
@Configuration
public class CacheSyncConfig {
    
    @Bean
    public MessageListenerAdapter cacheEvictionListener(CacheEvictionHandler handler) {
        return new MessageListenerAdapter(handler, "handleEviction");
    }
    
    @Bean
    public RedisMessageListenerContainer redisContainer(
            RedisConnectionFactory connectionFactory,
            MessageListenerAdapter listenerAdapter) {
        
        RedisMessageListenerContainer container = new RedisMessageListenerContainer();
        container.setConnectionFactory(connectionFactory);
        container.addMessageListener(listenerAdapter, new PatternTopic("cache.*"));
        return container;
    }
}
```

## 9. 限流和熔斷模式

```java fold


// 自定義限流器
@Component
public class RateLimiter {
    private final Map<String, Bucket> buckets = new ConcurrentHashMap<>();
    
    public boolean tryConsume(String key, int tokens, Duration window, int capacity) {
        Bucket bucket = buckets.computeIfAbsent(key, k -> 
            Bucket4j.builder()
                .addLimit(Bandwidth.fixed(capacity, window))
                .build()
        );
        
        return bucket.tryConsume(tokens);
    }
    
    public ConsumptionProbe tryConsumeAndReturnRemaining(String key, int tokens, 
                                                        Duration window, int capacity) {
        Bucket bucket = buckets.computeIfAbsent(key, k -> 
            Bucket4j.builder()
                .addLimit(Bandwidth.fixed(capacity, window))
                .build()
        );
        
        return bucket.tryConsumeAndReturnRemaining(tokens);
    }
}

// 限流註解
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface RateLimit {
    String key() default "";
    int capacity() default 100;
    int refillTokens() default 100;
    Duration refillPeriod() default @Duration("PT1M"); // 1 分鐘
    String fallbackMethod() default "";
}

// 限流 AOP
@Aspect
@Component
public class RateLimitAspect {
    
    @Around("@annotation(rateLimit)")
    public Object around(ProceedingJoinPoint joinPoint, RateLimit rateLimit) throws Throwable {
        String key = generateKey(joinPoint, rateLimit.key());
        
        ConsumptionProbe probe = rateLimiter.tryConsumeAndReturnRemaining(
            key, 1, rateLimit.refillPeriod(), rateLimit.capacity()
        );
        
        if (probe.isConsumed()) {
            // 在響應頭中添加限流資訊
            addRateLimitHeaders(probe);
            return joinPoint.proceed();
        } else {
            // 觸發限流，執行降級邏輯
            if (!rateLimit.fallbackMethod().isEmpty()) {
                return executeFallback(joinPoint, rateLimit.fallbackMethod());
            } else {
                throw new RateLimitExceededException(
                    "Rate limit exceeded. Try again in " + probe.getNanosToWaitForRefill() / 1_000_000 + "ms"
                );
            }
        }
    }
    
    private void addRateLimitHeaders(ConsumptionProbe probe) {
        RequestContextHolder.currentRequestAttributes();
        HttpServletResponse response = ((ServletRequestAttributes) 
            RequestContextHolder.currentRequestAttributes()).getResponse();
            
        if (response != null) {
            response.setHeader("X-RateLimit-Remaining", 
                String.valueOf(probe.getRemainingTokens()));
            response.setHeader("X-RateLimit-Retry-After-Milliseconds", 
                String.valueOf(probe.getNanosToWaitForRefill() / 1_000_000));
        }
    }
}

// 使用示例
@RestController
@RequestMapping("/api/orders")
public class OrderController {
    
    @PostMapping
    @RateLimit(
        key = "'order_creation_' + authentication.name", 
        capacity = 10, 
        refillPeriod = @Duration("PT1M"),
        fallbackMethod = "createOrderFallback"
    )
    public ResponseEntity<OrderResponse> createOrder(@RequestBody CreateOrderRequest request) {
        OrderResponse response = orderService.createOrder(request);
        return ResponseEntity.ok(response);
    }
    
    public ResponseEntity<OrderResponse> createOrderFallback(CreateOrderRequest request) {
        // 降級邏輯：返回快取的響應或延遲處理
        return ResponseEntity.status(HttpStatus.TOO_MANY_REQUESTS)
            .body(OrderResponse.builder()
                .message("請求過於頻繁，請稍後再試")
                .retryAfter(60)
                .build());
    }
    
    @GetMapping("/{id}")
    @RateLimit(key = "'order_query_' + request.remoteAddr", capacity = 100)
    public ResponseEntity<OrderDto> getOrder(@PathVariable String id) {
        OrderDto order = orderService.getOrder(id);
        return ResponseEntity.ok(order);
    }
}

// 分散式限流（基於 Redis）
@Component
public class DistributedRateLimiter {
    private final RedisTemplate<String, String> redisTemplate;
    
    public boolean isAllowed(String key, int maxRequests, Duration window) {
        String script = """
            local key = KEYS[1]
            local window = tonumber(ARGV[1])
            local limit = tonumber(ARGV[2])
            local current_time = tonumber(ARGV[3])
            
            -- 清除過期的請求記錄
            redis.call('zremrangebyscore', key, 0, current_time - window * 1000)
            
            -- 獲取當前窗口內的請求數量
            local current_requests = redis.call('zcard', key)
            
            if current_requests < limit then
                -- 記錄當前請求
                redis.call('zadd', key, current_time, current_time)
                redis.call('expire', key, math.ceil(window))
                return 1
            else
                return 0
            end
            """;
        
        List<String> keys = Collections.singletonList(key);
        List<String> args = Arrays.asList(
            String.valueOf(window.getSeconds()),
            String.valueOf(maxRequests),
            String.valueOf(System.currentTimeMillis())
        );
        
        Long result = redisTemplate.execute(
            (RedisCallback<Long>) connection -> 
                connection.eval(script.getBytes(), ReturnType.INTEGER, 1, 
                    keys.toArray(new String[0]), args.toArray(new String[0]))
        );
        
        return result != null && result == 1L;
    }
}
```

## 10. 觀察者模式與領域事件

```java fold

// 領域事件基類
@MappedSuperclass
public abstract class DomainEvent {
    private final String eventId;
    private final LocalDateTime occurredOn;
    private final String eventType;
    
    protected DomainEvent() {
        this.eventId = UUID.randomUUID().toString();
        this.occurredOn = LocalDateTime.now();
        this.eventType = this.getClass().getSimpleName();
    }
    
    // getters...
}

// 聚合根基類
@MappedSuperclass
public abstract class AggregateRoot<T> {
    
    @Transient
    private final List<DomainEvent> domainEvents = new ArrayList<>();
    
    protected void addDomainEvent(DomainEvent event) {
        this.domainEvents.add(event);
    }
    
    public void clearDomainEvents() {
        this.domainEvents.clear();
    }
    
    public List<DomainEvent> getDomainEvents() {
        return Collections.unmodifiableList(domainEvents);
    }
}

// 具體聚合實現
@Entity
public class Order extends AggregateRoot<OrderId> {
    
    @EmbeddedId
    private OrderId id;
    
    private OrderStatus status;
    private Money totalAmount;
    
    public void confirm() {
        if (this.status != OrderStatus.PENDING) {
            throw new IllegalStateException("Only pending orders can be confirmed");
        }
        
        this.status = OrderStatus.CONFIRMED;
        
        // 發布領域事件
        addDomainEvent(new OrderConfirmedEvent(this.id, this.totalAmount));
    }
    
    public void cancel(String reason) {
        if (this.status == OrderStatus.DELIVERED || this.status == OrderStatus.CANCELLED) {
            throw new IllegalStateException("Cannot cancel order in current status: " + this.status);
        }
        
        this.status = OrderStatus.CANCELLED;
        addDomainEvent(new OrderCancelledEvent(this.id, reason));
    }
}

// 領域事件發布器
@Component
@Transactional
public class DomainEventPublisher {
    
    private final ApplicationEventPublisher eventPublisher;
    private final DomainEventStore eventStore;
    
    public DomainEventPublisher(ApplicationEventPublisher eventPublisher, 
                              DomainEventStore eventStore) {
        this.eventPublisher = eventPublisher;
        this.eventStore = eventStore;
    }
    
    public void publishEvents(AggregateRoot<?> aggregate) {
        List<DomainEvent> events = aggregate.getDomainEvents();
        
        for (DomainEvent event : events) {
            // 持久化事件（事件溯源）
            eventStore.save(event);
            
            // 發布事件
            eventPublisher.publishEvent(event);
        }
        
        // 清除事件
        aggregate.clearDomainEvents();
    }
}

// JPA 生命週期監聽器
@Component
public class DomainEventListener {
    
    @Autowired
    private DomainEventPublisher eventPublisher;
    
    @PostPersist
    @PostUpdate
    @PostRemove
    public void handleEvent(Object entity) {
        if (entity instanceof AggregateRoot) {
            eventPublisher.publishEvents((AggregateRoot<?>) entity);
        }
    }
}

// 事件處理器
@Component
@Transactional
public class OrderEventHandler {
    
    private final NotificationService notificationService;
    private final InventoryService inventoryService;
    private final PaymentService paymentService;
    
    @EventListener
    @Async("domainEventExecutor")
    public void handleOrderConfirmed(OrderConfirmedEvent event) {
        log.info("處理訂單確認事件: {}", event.getOrderId());
        
        try {
            // 發送確認通知
            notificationService.sendOrderConfirmation(event.getOrderId());
            
            // 預留庫存
            inventoryService.reserveItems(event.getOrderId());
            
            // 處理付款
            paymentService.processPayment(event.getOrderId(), event.getAmount());
            
        } catch (Exception e) {
            log.error("處理訂單確認事件失敗", e);
            // 發布補償事件
            eventPublisher.publishEvent(new OrderConfirmationFailedEvent(event.getOrderId(), e.getMessage()));
        }
    }
    
    @EventListener
    @Order(1) // 確保優先執行
    public void auditOrderEvent(OrderConfirmedEvent event) {
        auditService.recordOrderEvent(event);
    }
    
    // 事件重放處理
    @EventListener
    public void handleEventReplay(EventReplayRequest request) {
        List<DomainEvent> events = eventStore.getEventsAfter(
            request.getAggregateId(), 
            request.getFromVersion()
        );
        
        for (DomainEvent event : events) {
            processEventForReplay(event);
        }
    }
}

// 事件存储
@Repository
public class DomainEventStore {
    
    @PersistenceContext
    private EntityManager entityManager;
    
    public void save(DomainEvent event) {
        StoredEvent storedEvent = new StoredEvent(
            event.getEventId(),
            event.getEventType(),
            event.getOccurredOn(),
            toJson(event)
        );
        
        entityManager.persist(storedEvent);
    }
    
    public List<DomainEvent> getEventsAfter(String aggregateId, Long version) {
        return entityManager.createQuery(
            "SELECT se FROM StoredEvent se WHERE se.aggregateId = :aggregateId AND se.version > :version ORDER BY se.version",
            StoredEvent.class
        )
        .setParameter("aggregateId", aggregateId)
        .setParameter("version", version)
        .getResultList()
        .stream()
        .map(this::fromJson)
        .collect(Collectors.toList());
    }
    
    public void replay(String aggregateId, Consumer<DomainEvent> eventHandler) {
        List<DomainEvent> events = getEventsAfter(aggregateId, 0L);
        events.forEach(eventHandler);
    }
}

// 事件驅動的讀模型更新
@Component
public class OrderProjectionHandler {
    
    @EventListener
    @Transactional("readTransactionManager")
    public void updateOrderSummary(OrderConfirmedEvent event) {
        OrderSummary summary = orderSummaryRepository
            .findById(event.getOrderId().getValue())
            .orElse(new OrderSummary(event.getOrderId()));
            
        summary.setStatus("CONFIRMED");
        summary.setConfirmedAt(event.getOccurredOn());
        summary.setAmount(event.getAmount());
        
        orderSummaryRepository.save(summary);
    }
    
    @EventListener
    @Transactional("readTransactionManager") 
    public void updateCustomerStatistics(OrderConfirmedEvent event) {
        CustomerStatistics stats = customerStatsRepository
            .findByCustomerId(event.getCustomerId())
            .orElse(new CustomerStatistics(event.getCustomerId()));
            
        stats.incrementOrderCount();
        stats.addToTotalSpent(event.getAmount());
        stats.setLastOrderDate(event.getOccurredOn());
        
        customerStatsRepository.save(stats);
    }
}
```

## 總結

這些進階模式涵蓋了企業級 Spring Boot 應用的核心架構問題：

1. **事件驅動架構** - 實現系統解耦和異步處理
2. **CQRS** - 讀寫分離，優化查詢性能
3. **Saga** - 管理分散式事務
4. **多租戶** - 支持 SaaS 架構
5. **斷路器** - 提高系統韌性
6. **策略+工廠** - 靈活的業務邏輯組織
7. **異步消息** - 高吞吐量處理
8. **多層緩存** - 優化性能和擴展性
9. **限流熔斷** - 保護系統穩定性
10. **領域事件** - 實現複雜業務邏輯的清晰表達

掌握這些模式將使您能夠設計和實現高性能、高可用性、易維護的企業級 Spring Boot 應用程式。每個模式都有其適用場景，關鍵是根據具體需求選擇合適的組合。