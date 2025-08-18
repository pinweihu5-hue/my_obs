

Jack 希望我 vehicle tag service 應該要透過 tag action service 去呼叫 tagAction 的 repo
不應該直接呼叫


大案子，需要架構更嚴謹，這樣更好, 因為
- 透過職責明確的架構來避免重複造車
	- 大家如果都在自己的 server 呼叫 其他人的 repo, 會有重複邏輯，但也不會發現。如果你一定要去專屬負責的 svc 再去呼叫那個svc 自己的 repo, 那你就會發現有重複，並且要解決重複。
	- 另外如果你想像一個大專案，大家都負責自己的 "資源"(tag / vehicle tag /   等等), 哪你自然不應該呼叫別人資源底下的 db, 你要呼叫那個 svc 才應該要呼叫 db
- 大專案適合，但是小專案這樣做的缺點?
	- 會出現為了架構而架構，類似會出現 one-line function, 為了要透過 tag action svc 呼叫其 repo, 你需要寫 one line fn at tag action svc
![[IMG-分層架構 one repo per module-20250807202924770.png]]

另外這邊有提到另一個是避免 circular dependnece
因為有些 架構是 svc 層可以直接 access repo 層的 db
這樣如果大家可以互相 在 svc 呼叫到 repo 的 db, 那 就可能又呼叫到 svc, 這樣就有可能發生


不過目前的 VS and PS, 或是多數 wemo repo 都有切初一層 repo 層
只要我們不在 repo 層往上呼叫，都不太會出現 circular dependnece