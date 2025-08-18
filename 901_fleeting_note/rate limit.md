


# 設計 rate limit 的注意事項
[1]  [Notes on Gloogle Cloud Rate-limiting strategies and techniques · GitHub](https://gist.github.com/rponte/c610d391820a7fd28aee13ce879a87c8)


## find limiting key
要實施速率限制，首先要了解在這種情況下應用它的原因，然後確定請求的哪些屬性最適合用作限制金鑰（例如，來源 IP 位址、使用者、API 金鑰）。選擇 limit key 後，rate limit 實作可以使用它來追蹤使用情況。當達到限制時，服務會傳回限制訊號（通常是 429 HTTP 回應）。



## 搭配其他 pattern 使用
建立具有強大錯誤處理能力的系統，以防速率限制策略的某些部分失敗，並了解您的服務的用戶在這些情況下將收到什麼。 [...] 使用timeouts, deadlines, and circuit-breaking patterns 可以幫助您的服務在沒有速率限制的情況下更加穩健。



## 替代方案： queue
如果計算回應成本高昂或耗時，系統可能無法對請求提供及時回應，這使得服務更難處理高速率的請求。在這些情況下，速率限制的替代方法是將請求分流到佇列中並傳回某種形式的作業 ID。 [...] 當對請求的立即回應不包含真實訊息時，延遲回應模式最容易應用。**如果過度使用此模式，則會增加系統的複雜性和故障模式**。




# 實作方法

一般都會放在 middle ware

![[IMG-rate limit-20250810200240183.png]]

## token bucket

pro and con
- 用在你想要應付瞬間的大流量, spike
	- 如果桶裡面有2000個 token, 瞬間來 2000個都可以吃

流程
- 主要控制設定 refill rate 要多少，類似一秒 10 個
- 設定你要放多少 token 在 bucket內
- 不同的 API, limiting key, 可以套用不同的上面這兩個 param

![[IMG-rate limit-20250810200241281.png]]


## leaky bucket
pro and con
- 想要控制吃 req 的速度，平滑化


流程
- 用 queue 管理, FIFO
- 用一定的速度來消耗 queue

![[IMG-rate limit-20250810200242618.png]]


## fixed window
- pro
	- 易於理解。
	- 便於計算和存儲請求數量。
	- 一個時間點一定要有多少，某些 use case 會要這個
- con
	- **窗口邊界會出現突發流量（burst），導致短時間內的高負載**。




切點會有超過預期的 req
![[IMG-rate limit-20250810200244174.png]]

## sliding window log
- pro
	- 解決 fixed window 問題
	- 用 redis ttl 很好實作
- con

- 設定 1 分鐘內 2 個 最多
- 1分鐘內，進來 兩個 -> okay
- req 打進來
	- 超過時間的 log 移除 (use redis TTL implement )
	- check log 有多少，是否超過, 超過 throw 429 error (user hash, 記錄有幾個了，每此 count+1, 超過設定的 count -> 429)
![[IMG-rate limit-20250810200245470.png]]




redis use INCR to implement rate-limit
```c

FUNCTION LIMIT_API_CALL(ip)

ts = CURRENT_UNIX_TIME()

keyname = ip+":"+ts

MULTI
    INCR(keyname)
    EXPIRE(keyname,10)
EXEC
current = RESPONSE_OF_INCR_WITHIN_MULTI
IF current > 10 THEN
    ERROR "too many requests per second"
ELSE
    PERFORM_API_CALL()
END

```
https://redis.io/commands/incr/











# link

[2] Twitter rate limits: https://developer.twitter.com/en/docs/basics/rate-limits
[3] Google docs usage limits: https://developers.google.com/docs/api/limits
[4] IBM microservices: https://www.ibm.com/cloud/learn/microservices
[5] Throttle API requests for better throughput:
https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-request-throttling.html
[6] Stripe rate limiters: https://stripe.com/blog/rate-limiters
[7] Shopify REST Admin API rate limits: https://help.shopify.com/en/api/reference/rest-admin-api-rate-limits
[8] Better Rate Limiting With Redis Sorted Sets:
https://engineering.classdojo.com/blog/2015/02/06/rolling-rate-limiter/
[9] System Design — Rate limiter and Data modelling:
https://medium.com/@saisandeepmopuri/system-design-rate-limiter-and-data-modelling-9304b0d18250
[10] How we built rate limiting capable of scaling to millions of domains:
https://blog.cloudflare.com/counting-things-a-lot-of-different-things/
[11] Redis website: https://redis.io/
[12] Lyft rate limiting: https://github.com/lyft/ratelimit
[13] Scaling your API with rate limiters:
https://gist.github.com/ptarjan/e38f45f2dfe601419ca3af937fff574d#request-rate-limiter
[14] What is edge computing: https://www.cloudflare.com/learning/serverless/glossary/what-is-edge-computing/
[15] Rate Limit Requests with Iptables: https://blog.programster.org/rate-limit-requests-with-iptables
[16] OSI model: https://en.wikipedia.org/wiki/OSI_model#Layer_architecture