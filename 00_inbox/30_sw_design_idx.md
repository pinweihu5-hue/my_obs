---
aliases:
  - dist_sys_idx, system_design_idx
---

# distributed_system
- [[The Raft Consensus Algorithm  Raft 共識演算法]]
- [[ACID]]
- [[非功能需求]]
- [[Consistent Hash]]
- [[流量很大的策略（後端處理处理 C10k 问题）]]
- [[partition]]
- [[replication (ddia)]]
- [[Consistency and Consensus, DDIA CHAPTER 9]]
- [[serializability (可串行性 )vs linearizability (線性一致性)]]
- [[storage and retrieval DB engine]]
- [[假设你工作的系统不支持事务性 你会如何从头开始实现它]]
- [[分布式系統 CAP]]
- [[分布系統的權衡策略]]
- [[分布系統的謬誤]]
- [[分散式事務]]
- [[在分布式系统中，如何处理故障？]]
- [[Circuit-Breaking Pattern（熔斷器模式）]]
- [[when to use async operation]]
- [[背壓 (Backpressure)]]
- [[非功能需求]]
- [[mutex 跟 semaphore 有什麼差別]]




- [[what is event sourcing]]

- [[追求 simple not easy]]

- [[AbortController]]
- [[發一個 request , 有哪些可以思考討論的]]

- 思考哪些問題值得投入精力
	- 知道哪些問題值得投入精力。
	- 知道哪些專案值得維護。
	- 知道你是在為了幫助而建造，還是為了應對而建造。
	- 知道何時停止。
	- **「學會讓它們保持一點破損」**：這是作者正在努力學習的「最困難的事情」。因為他意識到，他不想「修復一切」，只想在一個「往往不盡如人意的世界中感到安好」。

- [[load balancing 算法]]
- [[認證機制]]
- [[developer to architect 系列]]
- [[Oauth]]
- [[microservice]]
- [[如何防止供应商依赖 (Vendor Lock-in)]]
- [[webhook]]
- [[Domain Driven Design]]
- [[rate limit]]
- [[RESTful API  設計]]
- [[大型物件 vs 小型互動物件]]
- [[Core Design Principles for Software Developers by Venkat Subramaniam]]
- [[Code Review, you said - Venkat Subramaniam]]
- [[Software Architecture The Hard Parts - Neal Ford]]
- [[Debugging Under Fire Keep your Head when Systems have Lost their Mind]]
- [[Continued Learning The Beauty of Maintenance - Kent Beck]] 
- [[Can Great Programmers Be Taught? - Prof. Dr. John Ousterhout]]
- [[How to Think Like an Architect - Mark Richards]]
- [[Thinking Like an Architect by Gregor Hohpe]]
- [[Build Abstractions Not Illusions by Gregor Hohpe]]
- [[agile]]
- [[how code rot]]
- [[abstract volatility away]]
- [[clean architecture]]
- [[OOP]]
- [[DI 依賴反轉 and IoC 控制反轉]]
- [[非功能需求]]
- [[robust design]]
- [[SOLID]]
- [[軟體設計 原子能]]
- [[unify vs 特化設計]]
- [[db vs app, 邏輯和loading要放哪裡]]
- [[发送时要保守，接收时要开放]]
- [[好莱坞原则（Hollywood Principles]]
- [[take more time to think about use case - this is everything]]
- [[Engineering Principles for Building Financial Systems]]
- [[why OOP? self-asking question]]
- [[性能生命周期（Performance Lifecycle）]]
- book
	- [nodeJS_design_pattern_book](https://www.notion.so/nture4388/nodeJS_design_pattern_book-f1a5791080ef48a5a736a13182bd04e1?pvs=4)
	- [refactoring martin fowler](https://www.notion.so/nture4388/refactoring-martin-fowler-f18019b736b44fc9b4f3f7e2fbb8da5d?pvs=4)
- wemo experience
	- [[core system 功能規劃記錄]]
	- [[分層架構 one repo per module]]
	- [[why use dto in nest.js response]]
	- other [notion note](https://www.notion.so/nture4388/better-design-1239df69750f8061836fc63324ce83dc?pvs=4)



---






[(778) What to do when there are no requirements - Uncle Bob - YouTube](https://www.youtube.com/watch?v=d2teQJzSh60)
- don't wait for the definition -> 開啟對話，先建立然後確認是否是這樣，你自己定義，讓他們確認是否可以


[(778) Why boolean arguments should be avoided - Robert C. Martin (Uncle Bob) - YouTube](https://www.youtube.com/watch?v=2Q9GRPxqCAk)
- 如果你 pass boolean to the function, you sould use 2 seperate fun



[Why you should never write bad code - Uncle Bob - YouTube](https://www.youtube.com/watch?v=HV1Kp-pj3fo)
- to move fast, you need to do it correctly -> 這代表很多時候你得慢下來，想清楚。


[(778) The problem with assignment statements - Uncle Bob - YouTube](https://www.youtube.com/watch?v=kdDDEHpS7ow)
- assert(f(x), f(x)) -> always true if f(x) is not side effect
- what is side effect?  if we have assignement statement in f(x) -> f(x)有可能有 side effect
- side effect function, will need another funciton to "undo" the side effect
	- like u have a funciton that open a file -> you need to have another fun to close the file
	- say you use malloc to allocate mem, u need to have another fun to de-allocate mem
- 另外 side effect 也跟 time coupling -> open need to before close, allocate mem need to first below de-allocate...etc



[The symptoms of bad code - Robert C. Martin (Uncle Bob) - YouTube](https://www.youtube.com/watch?v=vsQya8Ai1jw)
- 你改一個地方，你以為你只要改一個地方，沒想到很多地方都會連動到，因此你要去很多地方改
	- root cause: 沒有高內聚低耦合
	- solve: 
		- 假設你要調整價格系統的某個特定A功能的設定，應該就只有一個地方需要去調整。 
		- 沒關連的東西，不應該互相影響, 類似你改了 salaray 的計算，print report 的功能不應該不會被影響到
- common theme for bad code is "counping, 耦合", dependency





- [ ] [(604) 還在大費周章實作設計模式？一口氣了解函數型設計模式！與類別的設計有何區別？ - YouTube](https://www.youtube.com/watch?v=gFPVVKsDXdg)
	- [ ] try out use fn to implement factory and stgy



[(592) The code behind the Apollo moon landing ... was perfect - YouTube](https://www.youtube.com/watch?v=RnjTYBhAcfA)
key abt software design that transcand space and time
- oragnizing complex codebase
- **deisnging with user in mind**
- **optimizing resources**


[4 Software Design Principles I Learned the Hard Way](https://read.engineerscodex.com/p/4-software-design-principles-i-learned)
- reduce mutable state
- single source of truth
- don't blindly follow DRY => abstraction with clear intention
- don't over use mock -> not really understand how to actually do it...



# essential complexity
[A Note on Essential Complexity | olano.dev](https://olano.dev/blog/a-note-on-essential-complexity)
> In my experience most of the complexities which are encountered in systems work are symptoms of organizational malfunctions. Trying to model this reality with equally complex programs is actually to conserve the mess instead of solving the problems.

如果組織和spec本身就很亂，那"本質複雜性"(另一個意外複雜性，這個要盡可能降低)也會很亂
而這一點，工程師是可以去降低的，要降低的就是讓 spec 的架構更好

> 重新定義問題看起來像是作弊，但對資深工程師來說這只是平常事：我們為什麼要研究這個？我們真的需要它嗎？我們要解決什麼問題？我們解決這個問題對誰有利？如果我們最初發布的是 X1，而不是 X，這需要我們 20% 的努力並提供 80% 的功能，那會怎麼樣？



# 让编程再次伟大
[(540) 给代码起名字的三要三不要准则【让编程再次伟大 #11】 - YouTube](https://www.youtube.com/watch?v=Z02zGJcJ2EA&list=WL&index=2)
正確性 > 精準性 > 一致性





# 市场不会激励优秀的工程，它会激励速度和执行力
很多人觉得，代码质量是软件公司的生命。但是，大多数公司的生死存亡并不取决于它的代码库的质量。可怕的代码库也可能带来了数十亿美元的收入。市场不会激励优秀的工程，它会激励速度和执行力。
-- [《完美的代码库无法拯救你的公司》](https://www.catalystmonitor.com/blog/perfect-codebase-wont-save-your-company)



# to read
- [GitHub - zakirullin/cognitive-load: 🧠 Cognitive Load is what matters](https://github.com/zakirullin/cognitive-load)
- [99 Bottles — Sandi Metz](https://sandimetz.com/99bottles)












## sysrem_design_interview_prep
- [[a chat system]]
- [[a key-val store]]
- [[a notification system]]
- [[a unique ID generator is distributed system]]
- [[autocomplete, typeahead, search-as-you-type]]
- [[design google drive, dropbox]]
- [[news feed 系統]]
- [[uber app]]
- [[web crawler 網路爬蟲]]
- [[短網址 short url]]
- [[設計 youtube]]
- [[how s3 work]]
	- [[Erasure Coding（擦除編碼）]]
- [Bloom Filter 布隆過濾器 in notion](https://www.notion.so/nture4388/Bloom-Filter-4b88c99cc6744fe49a110e21821317c2?pvs=4)
- [[power of 2]]


- book
	- SYSTEM DESIGN INTERVIEW AN INSIDER'S GUIDE Alex xu
	- 中文翻譯：[目录 | 系统设计面试：内幕指南](https://learning-guide.gitbook.io/system-design-interview)
- wemo 經驗
	- [HackMD: Your Collaborative Markdown Workspace for Knowledge Sharing](https://hackmd.io/?nav=overview)
		- [Bi-directional Communication - App/Box/Service - HackMD](https://hackmd.io/cPXHm-8dTmK-ju94WSWy4A)
		- [Kafka - Data & BE Data Pipelines - HackMD](https://hackmd.io/cosVLzw3Tf-BMBDv7xiDSw)




看 system design 在文章時，要多思考
why 作者這樣改，是遇到什麼困難和瓶頸
這個要找到
因為如果我遇到了一樣的問難和瓶頸
我就可以解答
(樂高概念)
(頭腦要先有坑，才可以填)


## FB

Facebook Timeline: Brought To You By The Power Of Denormalization:
https://goo.gl/FCNrbm

Scale at Facebook: https://goo.gl/NGTdCs

Building Timeline: Scaling up to hold your life story: https://goo.gl/8p5wDV

Erlang at Facebook (Facebook chat): https://goo.gl/zSLHrj

Facebook Chat: https://goo.gl/qzSiWC

Finding a needle in Haystack: Facebook’s photo storage: https://goo.gl/edj4FL

Serving Facebook Multifeed: Efficiency, performance gains through redesign: https://goo.gl/adFVMQ

Scaling Memcache at Facebook: https://goo.gl/rZiAhX

TAO: Facebook’s Distributed Data Store for the Social Graph: https://goo.gl/Tk1DyH


## amazon

Amazon Architecture: https://goo.gl/k4feoW

Dynamo: Amazon’s Highly Available Key-value Store: https://goo.gl/C7zxDL


## netflix

A 360 Degree View Of The Entire Netflix Stack: https://goo.gl/rYSDTz

It’s All A/Bout Testing: The Netflix Experimentation Platform: https://goo.gl/agbA4K

Netflix Recommendations: Beyond the 5 stars (Part 1): https://goo.gl/A4FkYi

Netflix Recommendations: Beyond the 5 stars (Part 2): https://goo.gl/XNPMXm


## gooogle

Google Architecture: https://goo.gl/dvkDiY

The Google File System (Google Docs): https://goo.gl/xj5n9R

Differential Synchronization (Google Docs): https://goo.gl/9zqG7x

Seattle Conference on Scalability: YouTube Scalability: https://goo.gl/dH3zYq

Bigtable: A Distributed Storage System for Structured Data: https://goo.gl/6NaZca


## other

The Architecture Twitter Uses To Deal With 150M Active Users: https://goo.gl/EwvfRd 

Scaling Twitter: Making Twitter 10000 Percent Faster: https://goo.gl/nYGC1k

How Uber Scales Their Real-Time Market Platform: https://goo.gl/kGZuVy 


Instagram Architecture: 14 Million Users, Terabytes Of Photos, 100s Of Instances, Dozens Of Technologies: https://goo.gl/s1VcW5

Scaling Pinterest: https://goo.gl/KtmjW3  

Pinterest Architecture Update: https://goo.gl/w6rRsf

A Brief History of Scaling LinkedIn: https://goo.gl/8A1Pi8  

Flickr Architecture: https://goo.gl/dWtgYa  

The WhatsApp Architecture Facebook Bought For $19 Billion: https://bit.ly/2AHJnFn