[[db_idx]]


---


### 複合索引（Composite Index）

當查詢涉及多個列時，使用複合索引可以顯著提高查詢性能。

#### 要點
- 索引中列的順序很重要，應該將選擇性較高的列放在前面，這樣可以更有效地篩選數據。
- "選擇性較高的列" 是指在查詢中能夠顯著減少結果集大小的列。換句話說，這些列的值分佈較為分散，能夠有效地篩選出較少的記錄。選擇性較高的列通常具有較高的唯一值數量（cardinality），這意味著這些列中的值是高度多樣化的。例如，主鍵列通常具有最高的選擇性，因為每個值都是唯一的。

#### 示例

假設有一個表 `orders`，包含 `customer_id`、`order_date` 和 `status` 列。你可以創建一個複合索引來覆蓋這些列：

```sql
CREATE INDEX idx_orders_on_customer_date_status
ON orders (customer_id, order_date, status);
```

這樣的索引可以提高以下查詢的性能：

```sql
SELECT *
FROM orders
WHERE customer_id = 123
  AND order_date >= '2023-01-01'
  AND status = 'shipped';
```


在 PostgreSQL 中創建多列索引（Multicolumn Indexes）時，選擇主要（major）和次要（minor）列的順序是很重要的。以下是選擇的一些指導原則：

1. 選擇性原則 OR 基數（Cardinality）：
    - 通常將選擇性更高的列放在索引的前面（作為 major 列）。
    - 選擇性指的是列中唯一值的比例。選擇性越高，該列在過濾數據時越有效，因為一次可以過濾掉很多值。
    - 假設均勻分布，那 city 會比 gender 的選擇性更好
    - 要考慮數據的實際分佈。譬如如果你的 city 雖然有1x個縣市，但是表中幾乎9x %都是台北市..那選擇性很低。
1. 查詢模式：
    - 考慮最常見的查詢模式。將經常在 WHERE 子句中單獨使用或首先使用的列放在前面。
2. 範圍查詢考慮：
    - 如果經常對某一列進行範圍查詢，考慮將其放在索引的最後（作為 minor 列）。
    - 這是因為範圍條件後的列在索引中就不那麼有用了。
3. 相等性條件：
    - 將更常用於相等性條件（例如 `column = value`）的列放在前面。
4. 前綴原則：
    - PostgreSQL 可以使用索引的左前綴，但不能跳過索引中的列。
    - 例如，如果索引是 (A, B, C)，那麼可以使用 A，或 A 和 B，但不能只使用 B 和 C。
6. 排序考慮：
    - 如果查詢經常需要按特定順序排序，可以將這些列放在索引中，並按所需的排序順序排列。
7. 測試和優化：
    - 創建索引後，使用 EXPLAIN ANALYZE 來測試實際查詢性能。
    - 根據實際性能結果調整索引結構。

舉例說明： 假設有一個用戶表，包含 city、age 和 name 列：
- 如果大多數查詢都是基於 city 和 age 的組合，可以創建索引： `CREATE INDEX user_city_age_idx ON users (city, age);`
- 這樣，即使只查詢 city，索引也能發揮作用。而如果查詢同時涉及 city 和 age，索引將更加高效。

記住，索引的選擇應該基於實際的查詢模式和數據特性。在大型數據庫中，可能需要進行多次測試和調整才能找到最佳的索引結構。



### 條件索引（Partial Index）

當查詢只涉及表中的一部分數據時，使用條件索引可以顯著提高查詢性能，並且減少索引的存儲空間。

#### 特點

1. **部分數據**：條件索引只包含滿足特定條件的數據，這樣可以減少索引的大小。

#### 示例

假設有一個表 `orders`，包含 `order_date` 和 `status` 列。你可以創建一個條件索引來只覆蓋狀態為 `shipped` 的訂單：

```sql
CREATE INDEX idx_orders_on_shipped
ON orders (order_date)
WHERE status = 'shipped';
```

這樣的索引可以提高以下查詢的性能：

```sql
SELECT *
FROM orders
WHERE status = 'shipped'
  AND order_date >= '2023-01-01';
```

### 綜合應用

你可以結合使用複合索引和條件索引來滿足特定的查詢需求。例如，假設你有一個表 `blocks`，包含 `blockTarget`、`beginAt`、`endAt`、`flow` 和 `condition` 列。你可以創建一個條件複合索引來覆蓋特定條件下的多個列：

```sql
CREATE INDEX idx_blocks_on_conditions
ON blocks (blockTarget, beginAt, endAt)
WHERE flow = 'Register' AND condition = 'PhoneChanged4TimesInSentryToken';
```

這樣的索引可以提高以下查詢的性能：

```sql
SELECT *
FROM blocks
WHERE blockTarget = '09376765112'
  AND now() BETWEEN beginAt AND COALESCE(endAt, 'infinity')
  AND flow = 'Register'
  AND condition = 'PhoneChanged4TimesInSentryToken';
```

### 總結

- **複合索引**：基於多個列創建的索引，適用於查詢涉及多個列的情況。
- **條件索引**：基於部分數據創建的索引，適用於查詢只涉及表中一部分數據的情況。

