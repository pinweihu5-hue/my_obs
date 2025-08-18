a good article [https://encore.dev/blog/queueing](https://encore.dev/blog/queueing)

- when you want to prevent only time-out req got server → think ab out LIFO queue(or stack) on task queue

## when use sqs

- handle a situatino that you have a lot of incoming request and you want to process them in async way
    - you can push as much as msg you want, no msg limited, the down stream lambda can gradully process them
    - so it’s like a buffer before a compute unit
        - like you lamnda only can cocurrent up 2k, but you got 20k incoming request coming in
            - u use a queue to take 20k msg in it, and 2k lambda will process them (and some of the task/msg need to wait to be processe, just sit in the queue)
- a pub-sub system
    - push to queue (publish), listen to queue (sub)
- decouple, ez to switch up stram and down stream
- auto-scale down stream
    - auto scale lambda instance
    - run process in parrellel
- at least one delivery, no msg loss
    - say if you directly call lambda, if fail to process, you just lose it

## when to use event?

- 它是異步的
    - 你不知道它何時會發生
    - 你發送了一個請求，但不知道何時會收到回應
        - 就像你發送一個任務來執行某些工作，但無法預先確定何時完成
    - 比如用戶點擊某個按鈕，你需要綁定一個事件回調來處理這個事件
    - 邏輯本質是基於事件的
- 可以解耦組件
- 可擴展性和容錯
    - 本來一分鐘只需要 handle 約一個，直接 fn call 就可以
    - 未來需要一秒鐘 handle 10000個，fn call無法 handle 有了 queue, 你需要快速丟到一個 queue 來慢慢處理
- 當多個組件/代理/部分需要對一個組件做出反應時
    - 所有這些組件都可以 subscribe 該組件的事件
- 你還可以將一個複雜的任務拆分為多個步驟的任務或事件
    - 整個工作流程就是一系列的事件步驟，每個事件和處理都可以獨立處理
- 壞處?
	- 性能會低於 直連 p2p
    - 複雜度提昇
        - 更多組件交互
        - 管理更多東西
	- debug, tracing and monitor 都會更複雜和更多元件要管理
- 有哪些可用?
    - GCP pubsub
    - amazon sqs
    - bull queue via redis
    - redis pubsub
    - kafka
    - amqp/ rabbitMQ



## How sr. 工程師 to evaluate the MQ

資深工程師評估MQ（Message Queue）時，通常會從以下幾個面向進行：

- **效能 (Performance)**
  - 吞吐量（Throughput）：每秒可處理的訊息數量
  - 延遲（Latency）：訊息從發送到接收的時間
  - 資源使用率：CPU、記憶體及網路帶寬消耗

- **可靠性 (Reliability)**
  - 訊息持久化機制
  - 訊息不丟失保障（At-least-once、Exactly-once）
  - 容錯與重試機制

- **擴展性 (Scalability)**
  - 水平擴展能力（增加節點）
  - 負載均衡策略
  - 支援多消費者與多生產者架構

- **易用性與維護性**
  - 管理介面及監控工具
  - 配置與部署的簡易度
  - 社群支持與文件完整度

- **安全性 (Security)**
  - 訊息加密傳輸
  - 存取控制與權限管理

資深工程師會根據專案需求，綜合以上指標進行評估，選擇最適合的MQ方案。

[[Message Queue 評估非功能性面向]]


- [sqs-vs-rabbitmq](https://stackoverflow.com/questions/28687295/sqs-vs-rabbitmq)
    - [https://stackoverflow.com/questions/28687295/sqs-vs-rabbitmq](https://stackoverflow.com/questions/28687295/sqs-vs-rabbitmq)
        看具體需求，值得一看
        
- [消息队列助你成为高薪的 Node.js 工程师 - 掘金](https://juejin.cn/post/6844904003151593479#heading-29)