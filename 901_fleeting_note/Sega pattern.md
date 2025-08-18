
link:
- [notion 筆記](https://www.notion.so/nture4388/db-88ed30cc1382487cbf71afc54797d987?pvs=4)
- [Saga Orchestration: NestJS & Kafka Guide | Medium | Medium](https://kawther-asma.medium.com/implementing-saga-orchestration-with-nestjs-and-kafka-129dac1fca98)



# what is Sega pattern? 

- 情境
    - 每個 micro svc 都有自己的 db
    - 需要跨 micro svc 來進行 tx, how?
    - 例子？ 類似我要進行一筆銀行交易，而我也要確定這個客戶的額度有到, 如果沒到，我需要 rollback
        - 銀行交易 和客戶，分別在不同的 mircrosvc
- how to use sega to solve?
    - 機制：
        - sega 是一系列的 local tx, 彼此透過 event 觸發
        - 所以流程就會是 會透過event 來觸發 一系列的 local tx
        - 如果失敗，又會呼叫一系列的 local 的 補償 tx 來抵消



# 兩種形式

## 編排 Choreography → event 自己發
![[IMG-Sega pattern-20241120184905556.png]]
via a bull queue or SQS...etc 



## 指揮 Orchestration  → 有一個中央的地方來發 event
![[IMG-Sega pattern-20241120184905707.png]]
via gcp pubsub, kafak,... message broker...



- 優點和潛在問題
    - 優點
        - 使應用程式能夠在多個服務之間維護資料一致性，而無需使用分散式事務(like XA、二階段提交 (2PC)、三階段提交 (3PC)、TCC (Try-Confirm-Cancel)
    - 潛在問題
        - 開發人員必須自己設計補償事務，而不是依賴 ACID 事務的自動回滾功能 rollback
        - 缺乏隔離（ACID 中的「I」）－缺乏隔離意味著多個 sagas 和事務的"並發執行"存在使用資料異常的風險。 
	        - dev 需要自行去處理這些風險。
        - 同步呼叫後的非同步決策 
	        - response back after 整個 sega 非同步 complete
	        - response back after sega init, and then client need to polling the result
	        - response back after sega init, and then server send the result via webhook or websocket or a topic in message broker
	        


ref:
[Pattern: Saga](https://microservices.io/patterns/data/saga.html)