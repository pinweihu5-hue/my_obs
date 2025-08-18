
# 說明

- 如果直接關掉會有一些問題
	- 處理到一半的 http req
- 因此需要一個機制：
	- 送一個 Signal - **SIGTERM** 給 Server 
	- server 會等待進行中的請求結束，當當請求都結束後，就可以關閉 Server 以及資料庫的連線
	- 以 [Kubernetes 的流程](https://kubernetes.io/docs/concepts/workloads/pods/pod/#termination-of-pods)來說
		- Pod 要被關閉時會進入 Terminating 的狀態並開始 30 秒的 **Grace Period**
		- Server 收到 **SIGTERM** 後，就會馬上開始跑 terminus 的關閉流程。
		- 如果 30 秒內都沒辦法優雅地關閉，就會吃一個 **SIGKILL** 被強制殺掉。


# 完整流程
1. 手動下指令或系統 **Delete Pod**
2. **30 秒 Grace Period** 開始
3. 在 Client 顯示 Pod 為 **Terminating**，同時送 **SIGTERM** 給 Pod，也同時從服務清單移除，接下來 **Readiness Probes 都會當作 Failed**
4. terminus 收到 SIGTERM 開始跑[清理流程](https://github.com/godaddy/terminus/blob/e368b21758ec911af5a7cc8cbd8f02017f6b725f/lib/terminus.js#L111-L114)：  
    — `beforeShutdown`：在這邊可以等特定需要跑完的東西跑完  
    — `asyncServerStop`：連線超過設定的 `timeout` HTTP 連線還沒自動斷掉就強制關掉 Server  
    — `onSignal`：處理關掉資料庫的連線  
    — `onShutdown`：就是已經關了
5. 如果以上執行超過 30 秒還沒關掉，直接送 SIGKILL，強制關掉 Pod
6. Pod 的資源從 API 以及 Client 的顯示上消失

了解並處理好了以上的細節後，重啟、升級系統時就能夠更踏實囉。





# bull queue 的例子


app 關掉前需要優雅的把 queue 處理好

/Users/re4388/project/work/angelia/src/app.module.ts
```js

  async beforeApplicationShutdown(_signal: string) {
    await gracefulShutdownHelper.shutdown()
    await this.bullQueueGracefulShutdownService.closingAllQueue()
    this.logger.log('Service ready for shutdown')
  }

```


包括，讓 consumer 不要在去拿job 來處理
已經在處理，我們就繼續等他們處理完，也要 log 記錄有哪些還在處理等等
最後就是正常的把 queue 關掉

```js fold

  public async closingAllQueue() {
    this.logger.log({ msg: '!>>> Graceful shutting down all Queues' })
    await Promise.all([
      await this.gracefulShutdown<Queue<SendSmsInfo>, SmsSendQueueService>(this.smsSendQueue, this.smsSendQueueService),
      await this.gracefulShutdown<Queue<SmsStatusUpdateRequest>, SmsCallbackQueueService>(
        this.smsCallbackQueue,
        this.smsCallbackQueueService
      ),
      await this.gracefulShutdown<Queue<ISendMailRequest>, MailSendQueueService>(
        this.emailSendQueue,
        this.mailSendQueueService
      ),
      await this.gracefulShutdown<Queue<IMailEventCallbackRequest>, MailCallbackQueueService>(
        this.emailCallbackQueue,
        this.mailCallbackQueueService
      )
    ])
    this.logger.log({ msg: '!>>> All Queues Shut Down !' })
  }

  private async gracefulShutdown<T extends Queue, R extends { consumeJobCount: number }>(queue: T, service: R) {
    try {
      // 1. Pause the Queue so no new jobs will be consumed
      this.logger.log({ msg: `Consumer STOP receiving from Queue: ${queue.name}...` })
      await queue.pause(true)

      // 2. Although we already pause the Queue, there may still be jobs in the process.
      // Wait until all of them are finished
      while (service.consumeJobCount > 0) {
        this.logger.log({ msg: `Jobs Remaining for Queue: ${queue.name}.| Consumer[${service.consumeJobCount}]` })
        await new Promise(resolve => setTimeout(resolve, 2000))
      }
      this.logger.log({ msg: `Queue: ${queue.name} is Empty on Pod, ready to Exit` })

      // 3. Close the Queue
      await queue.close()
      this.logger.log({ msg: `Shutting down Queue: ${queue}` })
    } catch (err) {
      this.logger.error({ msg: `!>>> gracefulShutdown error: ${err} !` })
    }
  }


```