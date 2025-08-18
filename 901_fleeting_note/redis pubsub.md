
# 例子

在這個架構中，Redis Pub/Sub 扮演著至關重要的角色，它負責處理非同步通訊並擴展回應機制，尤其是在叢集環境中。以下是它為何如此重要的詳細說明：

非同步通訊和解耦： 物聯網設備透過 MQTT 進行通訊，這是一種非同步的通訊方式。 Redis Pub/Sub 充當了同步 HTTP 請求到設備與非同步 MQTT 回應之間的橋樑。 BoxService 不需要直接等待設備的回應。相反，它發佈一條訊息並監聽 Redis Pub/Sub 通道。這種解耦讓 BoxService 可以處理其他請求而不會阻塞。

在叢集環境中處理回應（"Forward Data in Cluster"）： 「Forward Data in Cluster」部分突顯了 Redis Pub/Sub 的主要優勢。當設備最終透過 MQTT 回應時，一個 EXHook（可能是外部鉤子或服務）會接收該訊息。然後，這個 EXHook 將回應發佈到 Redis Pub/Sub 主題。這允許叢集中 任何 BoxService 實例（而不僅僅是發起請求的實例）接收和處理回應。這對於可擴展性和容錯能力至關重要。如果原始的 BoxService 實例發生故障，另一個實例可以無縫地接管回應處理。

定向通訊（基於主題的路由）： Redis Pub/Sub 使用主題（在此例中為 wemo/bb/boxId/response）來有效地路由訊息。只有訂閱此特定主題的訂閱者才會收到設備的回應，確保正確的 BoxService 實例獲得數據。

逾時機制： BoxService 使用 setTimeout 和 emitter.once 設定逾時。如果在逾時期限內沒有透過 Redis Pub/Sub 收到回應，BoxService 可以適當地處理逾時（例如，返回錯誤或重試請求）。如果沒有像 Redis Pub/Sub 這樣的機制來監聽非同步回應，這將不容易管理。

總而言之，Redis Pub/Sub 實現了非同步通訊，促進了叢集環境中的回應處理，提供了定向訊息路由，並支援逾時機制，使其成為此架構中的關鍵組件。如果沒有它，系統將會更加複雜、可擴展性更低且更不穩健。

![[IMG-redis pubsub-20250810210639379.png]]
ref: [docs.google.com/document/d/1tdlQDIWnxjq\_w6rQvCI3Ma6EruBmMTqDjJImBwOLELI/edit?tab=t.0](https://docs.google.com/document/d/1tdlQDIWnxjq_w6rQvCI3Ma6EruBmMTqDjJImBwOLELI/edit?tab=t.0)



![[IMG-redis pubsub-20250810210640304.png]]ref [Figma](https://www.figma.com/board/4MD4T4IegKTKPRN3RdJGE5/my?node-id=208-346&node-type=section&t=AMCBbCfpilaMYFF7-0)




code
src/emqx/exhook.service.ts:77
```ts fold



// src/emqx/exhook.service.ts:77
  private async publishBBResponse(
    payload: any,
    funTag: string,
    exTopicType: ExTopicType,
    attribute: { deviceId: string }
  ) {
    try {
      const decodedString = payload.toString('utf8')
      const requestId = JSON.parse(decodedString).id

      if (!requestId) {
        this.logger.debug({ funTag, exTopicType, attribute, msg: 'requestId is undefined' })
      }

      await this.redisPubSubService.publish(`wemo/bb/${attribute.deviceId}/response/${requestId}`, decodedString)
    } catch (error) {
      this.logger.debug({ funTag, payload, attribute, error: JSON.stringify(error) })
    }
  }







// src/emqx/exhook.service.ts:38
  async exHookPublishEventProcessor(exTopicType: ExTopicType, payload: any, attribute: { deviceId: string }) {
    const funTag = `${this.TAG}/exHookPublishEventProcessor`
    this.logger.debug({
      funTag,
      exTopicType,
      attribute,
      payload
    })

    try {
      switch (exTopicType) {
        case ExTopicType.PBBResponse:
          await this.pbbPubSubService.publishPBBResponse(payload, attribute)
          break
        case ExTopicType.PBBEvent:
          await this.pbbPubSubService.publishPBBEvent(payload, attribute)
          break
        case ExTopicType.CBBEvent:
          await this.pbbPubSubService.publishCBBEvent(payload, attribute)
          break
        case ExTopicType.CBBState:
          await this.pbbPubSubService.publishCBBState(payload, attribute)
          break
        case ExTopicType.BBResponse:
          await this.publishBBResponse(payload, funTag, exTopicType, attribute)
          break
        default:
          this.logger.debug({
            funTag,
            message: 'not match any exTopicType',
            exTopicType,
            attribute
          })
      }
    } catch (error) {
      this.logger.error(JSON.stringify({ funTag, error: JSON.stringify(error) }))
    }
  }
```