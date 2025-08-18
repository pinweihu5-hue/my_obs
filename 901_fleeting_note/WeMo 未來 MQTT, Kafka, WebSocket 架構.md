ref: [Bi-directional Communication - App/Box/Service](https://hackmd.io/cPXHm-8dTmK-ju94WSWy4A)

![[IMG-WeMo 未來 MQTT, Kafka, WebSocket 架構-20250811203629576.png]]





- BB <-> mqtt <-> 微服務(s)
- useApp, serviceApp, Apollo, 透過 mqtt over websocket 來跟 微服務(s) 溝通 (一樣，中間有 broker)
- 微服務(s) 透過 kafka 來進行 持久性 msg pub-sub 的作業
![[IMG-WeMo 未來 MQTT, Kafka, WebSocket 架構-20250811203630761.png]]


![[IMG-WeMo 未來 MQTT, Kafka, WebSocket 架構-20250811203632893.png]]