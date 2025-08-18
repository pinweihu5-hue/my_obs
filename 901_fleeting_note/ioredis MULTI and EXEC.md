[[redis_idx]]

---


# when to use

hset 可以讓你 一次修改 hash 裡面的某個 property (如果你用 string, 你需要整 serlize/deserilize 後整包一起處理)
搭配 multi 可以讓你把很多修改一次做，好用!



# limitation

下一個指令的 input 無法吃上一個指令的output
- 因此，用 MULTI and EXEC 是無法 使用 rpop and lpush 達到 類似 Rpoplpush 這種效果 (當然你可以直接用Rpoplpush)


無法進行條件判定
- 如果你要進行邏輯判定，類似只有當某個欄位為null時，你才會去進行某個特定的操作 -> need to use lua script



no roll back
```sh

MULTI
SET key1 "value1"
INCR key2  // if fail
HSET key3 field "value" // still run
EXEC

```

EXEC 會返回一個包含每個命令結果的數組，其中失敗的命令會標記錯誤。


需要 app 處理 roll back
```js fold

const Redis = require('ioredis');
const redis = new Redis();

async function runTransaction() {
  try {
    const results = await redis.multi()
      .set('key1', 'value1')
      .incr('key2')
      .hset('key3', 'field', 'value')
      .exec();

    console.log('Transaction results:', results);
  } catch (err) {
    console.error('Transaction error:', err);
  } finally {
    redis.quit();
  }
}

runTransaction();

/**
[
  [null, 'OK'],          // SET 成功   不會回滾
  [Error: ERR...],       // INCR 失敗  會顯示
  [null, 1]              // HSET 成功  還是會跑
]
**/



// 如果您需要在某個命令失敗時回滾整個事務，可以手動執行回滾邏輯。以下是一個簡單的模擬回滾示例：

const Redis = require('ioredis');
const redis = new Redis();

async function runTransactionWithRollback() {
  const transaction = redis.multi();

  try {
    transaction.set('key1', 'value1');
    transaction.incr('key2');
    transaction.hset('key3', 'field', 'value');

    const results = await transaction.exec();

    // 檢查是否有命令失敗
    for (const [error, result] of results) {
      if (error) {
        throw new Error('Transaction failed, rolling back...');
      }
    }

    console.log('Transaction succeeded:', results);
  } catch (err) {
    console.error(err);
    // 執行回滾邏輯，比如刪除已成功的鍵
    await redis.del('key1');
    await redis.del('key2');
    await redis.del('key3');
  } finally {
    redis.quit();
  }
}

runTransactionWithRollback();

// 如果任何命令失敗，我們會檢測到並拋出錯誤，隨後在 catch 區塊中執行回滾邏輯。

```





---



# 結果會以嵌套數組的形式返回


每個內部數組對應於 multi 中執行的單個命令，包含兩個元素：
第一個元素是錯誤（如果存在的話），否則為 null。
第二個元素是命令的結果。

讓我們來看看你的代碼和返回結果的詳細解釋：
```js
javascript
Copy
redis
  .multi()
  .set("foo", "bar")
  .get("foo")
  .exec((err, results) => {
    // results === [[null, 'OK'], [null, 'bar']]
  });

```
解釋返回結果
results === [[null, 'OK'], [null, 'bar']] 這表示：

第一個內部數組 [null, 'OK']：
null 表示第一個命令（SET foo bar）沒有錯誤。
'OK' 是 SET 命令在成功執行後的返回值。

 \第二個內部數組 [null, 'bar']：
null 表示第二個命令（GET foo）沒有錯誤。
'bar' 是 GET 命令在成功執行後的返回值，這是我們之前設置的值。



