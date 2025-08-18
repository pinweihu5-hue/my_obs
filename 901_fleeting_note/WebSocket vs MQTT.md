
|                     | MQTT                                                                   | WebSocket                                                                                                         |
| ------------------- | ---------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| 架構                  | pubsub                                                                 | 雙向，socket like API . Note: many WebSocket frameworks and libraries add pub/sub capabilities on top of WebSockets. |
| Message Queuing     | 訂閱者斷線的話， broker 可以 queue 住 msg                                         | 沒有 queue, 除非另外使用其他軟體                                                                                              |
| 連接類型                | long lived connect via broker                                          | persistent direct connection                                                                                      |
| 溝通方式                | oneToMany (broadcast), async, event based                              | one to onem async, event based                                                                                    |
| 資料 Format           | Binary                                                                 | Binary (Frame-based)                                                                                              |
| Underlying protocol | TCP                                                                    | TCP                                                                                                               |
| Secure Connections  | TLS                                                                    | TLS                                                                                                               |
| Max size            | up to 256 MB                                                           | up p to 2^63 bytes per frame                                                                                      |
| QoS mgmt            | 支援 0(至多一次), 1(至少一次), 2 (剛好一次), **Last will and testament (LWT)**, 消息保留 | 沒有 QoS, relies on TCP                                                                                             |
| 擴展性                 | 主要靠 broker 進行來擴展                                                       | 受到 direct  connections 數量限制，擴展需要額外的層去處理                                                                           |
| 消息 overhead         | Minimal, starting from 2 bytes                                         | Minimum 2 bytes, 6 bytes for masked frames                                                                        |
| 傳輸效率                | 高，因為 overhead 低                                                        | 低，因為 framing 的 overhead 高                                                                                         |
| 整合複雜度               | 中等，取決於 broker 設定                                                       | 低，和 HTTP/S 環境配適高                                                                                                  |
| 及時性                 | 高，但透過broker 會帶入一些  lantency                                            | 非常高，適合即時資料                                                                                                        |
| 網路侷限環境下的表現          | 適合網路侷限環境                                                               | 比較適合穩路穩定的環境                                                                                                       |
| Protocol Maturity   | Widely used in IoT                                                     | Popular in web development                                                                                        |
| use case            | IoT 應用，網路環境不好應用，telemetry                                              | 適合即時 web app, 遊戲和互動平台                                                                                             |

ref:
- [MQTT vs WebSocket - Which protocol to use when in 2024](https://ably.com/topic/mqtt-vs-websocket)
- [MQTT vs WebSocket: Key Differences & Applications | EMQ](https://www.emqx.com/en/blog/mqtt-vs-websocket)