
### 1. **gRPC showed us why observability isn’t optional in distributed systems**

* **意思**：在分散式系統中，「可觀測性」（observability）不是可有可無的，gRPC 的經驗證明了這點。
* **背景**：分散式系統的元件分布在不同機器或地理位置，問題可能發生在任意節點之間。如果沒有足夠的監控、日誌、追蹤，問題幾乎無法定位。
* **為什麼重要**：

  * 網路延遲、封包丟失、節點故障… 這些都可能讓系統行為不穩定。
  * 沒有可觀測性，開發者只能猜哪裡壞了。



### 2. **Built-in distributed tracing with metadata propagation enables debugging**

* **意思**：gRPC 內建的分散式追蹤（distributed tracing）功能，透過**中繼資料（metadata）傳遞**，讓除錯變得可行。
* **背景**：
  * 分散式追蹤會在請求（request）跨越多個服務時，附帶一個唯一 ID（trace ID）。
  * gRPC 可以把這些 ID 放在 metadata 中，自動在服務之間傳遞。
* **好處**：
  * 你可以在追蹤系統（如 Jaeger、Zipkin、OpenTelemetry）中看到整條請求路徑，以及每一段的耗時與錯誤點。
  * 這讓找出瓶頸、失敗來源更容易。



### 3. **Bidirectional streaming enabled responsive UIs**
* **意思**：gRPC 支援雙向串流（bidirectional streaming），讓使用者介面（UI）可以即時更新、更有回應性。
* **背景**：
  * 傳統 HTTP 1.1 是「請求–回應」模式，一次請求後要等回應才有下一步。
  * gRPC（基於 HTTP/2）支援**雙向串流**：客戶端與伺服器可以同時持續傳送資料。
* **好處**：
  * 即時聊天、遊戲、即時儀表板等場景，UI 不需要等到整個結果才更新，而是邊收到資料邊顯示。



### 4. **Deadline propagation prevented cascade failures**
* **意思**：gRPC 的「期限傳遞」（deadline propagation）機制，可以防止**連鎖故障**（cascade failures）。
* **背景**：
  * **Deadline** 是客戶端對請求設定的最大可等待時間。
  * gRPC 會自動把這個剩餘時間傳給下游服務。
  * 如果下游服務知道剩餘時間不足，就不會繼續處理（直接取消），避免浪費資源。
* **好處**：
  * 防止一個請求超時後，仍有一大堆下游服務忙著處理，導致系統資源被拖垮。
  * 這在微服務架構中尤其重要。
  * [[java grpc deadline propagation example]]



### 5. **Structured status codes distinguish retriable from permanent failures**
* **意思**：gRPC 的結構化狀態碼（structured status codes）可以區分**可重試的錯誤**與**永久失敗的錯誤**。
* **背景**：
  * gRPC 不只是回傳「200 / 500」這種 HTTP 狀態碼，而是有更細的錯誤分類（如 `UNAVAILABLE`, `INVALID_ARGUMENT`, `DEADLINE_EXCEEDED`）。
  * 開發者可以根據錯誤類型決定：
    * **可重試**（例如網路暫時中斷）
    * **不可重試**（例如請求參數錯誤）
* **好處**：
  * 客戶端可以自動化錯誤處理策略，減少人工判斷。
  * 避免對不可重試的錯誤一直重試，浪費資源。

---

✅ **總結**

* **可觀測性**：分散式系統必備，否則找問題像摸黑。
* **分散式追蹤 + metadata 傳遞**：能看到請求全路徑。
* **雙向串流**：讓 UI 即時更新，提升使用體驗。
* **期限傳遞**：防止下游無謂工作造成系統崩潰。
* **結構化狀態碼**：讓系統能自動化決定是否重試。

---
