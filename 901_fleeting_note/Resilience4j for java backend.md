Resilience4j 是一個功能強大的 Java 容錯庫，可以幫助你滿足多種非功能需求：

## 可用性 (Availability)

**Circuit Breaker**：防止級聯故障，當下游服務不可用時快速失敗 **Retry**：自動重試暫時性錯誤，提高成功率 **Fallback**：提供降級機制，即使依賴服務失敗也能提供基本功能

```java
@CircuitBreaker(name = "userService", fallbackMethod = "fallbackUser")
@Retry(name = "userService")
public User getUserById(String id) {
    return userServiceClient.getUser(id);
}

public User fallbackUser(String id, Exception ex) {
    return User.builder().id(id).name("Unknown").build();
}

```

## 性能 (Performance)

**TimeLimiter**：防止慢查詢拖垮系統性能 **Bulkhead**：隔離不同的資源池，防止資源競爭 **Cache**：減少重複調用，提升響應速度

```java
@TimeLimiter(name = "slowService")
@Bulkhead(name = "slowService", type = Bulkhead.Type.THREADPOOL)
public CompletableFuture<String> getSlowData() {
    return CompletableFuture.supplyAsync(() ->
        slowServiceClient.getData());
}

```

## 可靠性 (Reliability)

**Rate Limiter**：控制請求速率，保護系統不被過載 **Retry with Exponential Backoff**：智能重試機制，避免雪崩效應

```java
@RateLimiter(name = "apiService")
@Retry(name = "apiService")
public ApiResponse callExternalApi(String request) {
    return externalApiClient.call(request);
}

```

## 可觀察性 (Observability)

**Metrics 集成**：與 Micrometer/Prometheus 整合，提供詳細的監控指標 **Event 發布**：發布狀態變更事件，便於監控和告警

```java
// 自動暴露 metrics
CircuitBreakerStats stats = circuitBreaker.getMetrics();
- getNumberOfSuccessfulCalls()
- getNumberOfFailedCalls()
- getFailureRate()

```

## 可伸縮性 (Scalability)

**Thread Pool Bulkhead**：限制並發執行的線程數 **Semaphore Bulkhead**：控制並發訪問的資源數量

```java
@Bulkhead(name = "database", type = Bulkhead.Type.SEMAPHORE)
public List<User> searchUsers(String query) {
    return userRepository.search(query);
}

```

## 實際配置範例

```yaml
resilience4j:
  circuitbreaker:
    instances:
      userService:
        failure-rate-threshold: 50
        wait-duration-in-open-state: 30s
        sliding-window-size: 10

  retry:
    instances:
      userService:
        max-attempts: 3
        wait-duration: 1s
        exponential-backoff-multiplier: 2

  ratelimiter:
    instances:
      apiService:
        limit-for-period: 100
        limit-refresh-period: 1s
        timeout-duration: 0s

  timelimiter:
    instances:
      slowService:
        timeout-duration: 5s
        cancel-running-future: true

```

## 與 Spring Boot 整合優勢

**自動配置**：透過 Starter 自動配置所有組件 **AOP 支援**：使用註解即可啟用功能 **Actuator 整合**：健康檢查和監控端點 **配置外部化**：透過 application.yml 管理所有設定

Resilience4j 的模組化設計讓你可以根據實際需求選擇性使用，不會引入不必要的複雜性。同時，它與現代 Java 生態系統整合良好，特別適合微服務架構中的容錯處理需求。