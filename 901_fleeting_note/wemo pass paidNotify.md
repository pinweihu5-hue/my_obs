[[wemo_pass_note]]

---


- 購買 vip 後 hermes call trans API
	- ceres `WeMoOrderAgent`:`trans` 
	- `_trans` 
	- `checkAndProcessIfOrderPaid` 
	- `onPaid`
	- `this.hermesNotifier.vip`
	- `await this.jobQueue.addJob<NotifyVip>`   add job
	- `await this.hermes.vipPaidNotify(userVipDetailId, orderId)`   job run
	- ceres call hermes `POST /vip/details/:userVipDetailId/paidNotify`


---


WeMoOrderAgent 的 `trans` 會呼叫 `_trans` 會呼叫 `checkAndProcessIfOrderPaid`



WeMoOrderAgent 有一個 method `checkAndProcessIfOrderPaid` 
this method is to check if order paid and invoke `onPaid`
```ts

  private async checkAndProcessIfOrderPaid(orderId: string) {
    const isOrderPaid = (orderAmount: number, details: PaywayItem[]) => {
      const amount = getUnpaidAmount(orderAmount, details)
      return amount <= 0
    }

    const order = await this.db.orders.getOrderById(orderId)
    if (isOrderPaid(order.amount, order.details)) {
      await this.db.orders.updateOrderStatus({ orderId, status: OrderStatus.paid })
      await this.paymentChecker.deleteJob(orderId)
      const paidAmount = getPaidAmount(order.details)
      const invoiceAmount = this?.invoiceAmount ?? paidAmount
      await this.db.orders.updateOrderInvoiceAmount(orderId, invoiceAmount)
      if (this.onPaid) await this.onPaid({ orderId, amount: paidAmount }) // <--- HERE!
      await this.hermesNotifier.paymentPaid(orderId, this.orderType, paidAmount, new Date())
    }
  }


```




this vip fn is at `onPaid` method
所以當 VipAgent 被 init 就會把 邏輯 assign 給 onPaid了
```ts

export class VipAgent extends WeMoOrderAgent {
  invoiceAmount: number
  constructor(dep: Dependency) {
    super(dep, OrderType.vip)
    
    this.onPaid = async (data: OnPaidArg) => {
      const tag = 'v2/orders/vipAgent/onPaid'
      const { orderId } = data
      const order = await this.db.orders.getOrderById(orderId)
      if (!order.userVipDetailId) {
        logger.error({ tag, msg: 'userVipDetailId is null', orderId })
        return
      }
      this.invoiceAmount = order.amount
      await this.hermesNotifier.vip(orderId, order.userVipDetailId)
    }
  }


```



this code push job into queue
```ts

  async vip(orderId: string, userVipDetailId: string) {
    const jobId = 'VIP' + orderId
    await this.jobQueue.addJob<NotifyVip>(
	    HermesNotifyJob, 
		jobId, 
		{ 
			type: JobType.vipPaid, 
			userVipDetailId, 
			orderId 
		})
  }

```



Ceres::HermesNotifier(class)::hermesNotifyJob(method)
```ts

export class HermesNotifier {

constructor(private jobQueue: JobQueue, private hermes: Hermes) {
	this.jobQueue.initQueue<NotifyJobData>(HermesNotifyJob, this.hermesNotifyJob.bind(this)).catch((error) => logger.error({ tag: Tag, error }))
	
	}



// ... skip

        case JobType.vipPaid: {
          const { userVipDetailId, orderId } = <NotifyVip>data
          logger.debug({ tag, msg: 'notify hermes vip paid', userVipDetailId })
          await this.hermes.vipPaidNotify(userVipDetailId, orderId)
          break
        }

```




this code will call Hermes `POST /vip/details/:userVipDetailId/paidNotify`
```ts

  async vipPaidNotify(userVipDetailId: string, orderId: string) {
    const tag = 'api/hermes/vipPaidNotify'
    const url = `${this.config.host}/v20/vip/details/${userVipDetailId}/paidNotify`
    const opt = {
      headers: { Authorization: this.apiKey }
    }
    try {
      await retryNetworkError(5, async () => {
        const res = await axios.post(url, { orderId }, opt)
        return validCommonResult(res)
      })
    } catch (error) {
      logger.error({ tag, error })
      throw error
    }
  }

```


---



POST /vip/details/:userVipDetailId/paidNotify
paidNotify 的邏輯如下

其實就是呼叫
await VipService.setUserVipDetailPaid

## Paid Notify API 檢查點

## ![[IMG-wemo pass paidNotify-20241003104934083.png]]

  
  

