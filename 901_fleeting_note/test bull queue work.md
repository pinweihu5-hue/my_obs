
script for test

```tsx

for i in {1..2000}; do curl -X POST localhost:3011/api/v1/audio/transcode || (echo "Failed after $i attempts" && break); done

for i in {1..10}; do curl -X POST localhost:3011/api/v1/stress-runner/default || (echo "Failed after $i attempts" && break); done
```

- 初步結論
    - 不用 queue. you still can use below syntax to update 2k lines
        - 因此，本身nest.js就有某種每一個 req 需要排隊處理的機制，不會馬上打架?
        - 還可以做的?
            - 用專業軟體去打更多，同步打: [https://ithelp.ithome.com.tw/articles/10203900?sc=hot](https://ithelp.ithome.com.tw/articles/10203900?sc=hot)
            - 做更繁重的事情
                - 變成我要去研究這個api對我的server(mac 本機）的影響事什麼？io bound?
        - 需要 deploy到一個 container裡面，這樣把那個 container 的 cpu and memory 開低一點去測試
            - open container with these param
                ```markdown fold
                ## Using the --memory flag: 
                Docker allows you to set memory limits for containers using the --memory flag when starting a container. 
                You can specify the memory limit as an absolute value like 
                `--memory=1g` for 1 gigabyte 
                or use suffixes like 
                `--memory=512m` for 512 megabytes.
                
                ## Using the --cpu-quota flag:
                
                For example, if you set `--cpu-quota=50000` for a container, 
                it means the container is allowed to use up to 50 milliseconds 
                (or 5% of the CPU time) within each one-second period.
                ```
                
    - why use queue?
        - 用 queue 可以做 rate limiter “at JOB level”
            - 當然不需要用 queue也可以設定 rate limit “at api level”
        - 用 queue 可以可以給不同類型的工作排定優先順序
        - 用 queue 可以做 delay queue. 可以不用打來馬上做
        - 確保順序，選擇 fifo or lifo
        - buffer層。雖然 api level will have some mechansim of controlling incoming request, but those are low-level or framwork level stuff, which we are not fully control its behavior. with queue. we control the it
        - decouple sender and recevier
        - loadbalancing capbalitity, u can have multiple worker, even processcer to do work
        - error handling and recovery
            - dead letter queue, how to handle this
        - more…
            In software development, a queue mechanism is a data structure that follows the First-In-First-Out (FIFO) principle. It operates on the concept of enqueueing (adding elements to the back) and dequeueing (removing elements from the front). Queues are widely used in various software systems for several reasons:
            
            1. Order preservation: Queues ensure that the order of elements is preserved. The first item enqueued is the first item dequeued. This property is particularly useful in scenarios where the chronological order or sequence of operations is essential.
            2. Buffering and decoupling: Queues act as a buffer or intermediary between components or subsystems. They enable loose coupling by allowing the sender and receiver to operate independently and asynchronously. The sender can enqueue messages or tasks into the queue, and the receiver can process them at its own pace.
            3. Load balancing: Queues facilitate load balancing across multiple workers or processes. Instead of overwhelming a single component with an influx of requests or tasks, a queue can distribute the workload evenly. Workers can pull tasks from the queue, ensuring efficient utilization of resources.
            4. Synchronization and concurrency: Queues provide a synchronized and thread-safe mechanism for inter-thread or inter-process communication. Multiple threads or processes can safely enqueue and dequeue items from a shared queue without conflicts or race conditions.
            5. Error handling and recovery: Queues can be used to handle errors or failures gracefully. Failed or unprocessed items can be moved to an error queue for analysis or later processing. This enables fault tolerance and the ability to retry failed operations.
            6. Scalability and elasticity: Queues are fundamental in building scalable and elastic systems. By decoupling components through queues, you can add or remove processing resources dynamically without impacting the overall system's functionality.
            7. Asynchronous processing: Queues enable asynchronous processing, allowing applications to respond quickly to requests and perform background tasks efficiently. Requests can be added to a queue, and workers can process them independently, freeing up the main application thread to handle new incoming requests.
            
            Common implementations of queues in software development include message queues (e.g., RabbitMQ, Apache Kafka), task queues (e.g., Celery), and in-memory queues (e.g., Java's `java.util.Queue`, Python's `queue.Queue`).
            
            Overall, queues provide an essential mechanism for managing workloads, coordinating asynchronous operations, and facilitating reliable and scalable communication between software components.