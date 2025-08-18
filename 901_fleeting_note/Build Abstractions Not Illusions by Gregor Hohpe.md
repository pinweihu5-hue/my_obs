[(778) Build Abstractions Not Illusions • Gregor Hohpe • YOW! 2023 - YouTube](https://www.youtube.com/watch?v=aWZFRk-w3ng&list=WL&index=3)



# 好抽象讓你可以讓大家更好去使用，建立出更多東西
![[IMG-Build Abstractions Not Illusions by Gregor Hohpe-20250807202915555.png]]


# 如果你的抽象使用的詞彙跟你的東西是同一個world, level, 那就不是一個好的抽象
- 通常用用組成來命名抽象不是一個好的方式，因為這樣就是同一個 level 的詞彙

![[IMG-Build Abstractions Not Illusions by Gregor Hohpe-20250807202915811.png]]
![[IMG-Build Abstractions Not Illusions by Gregor Hohpe-20250807202916334.png]]



# 用組合的naming 來命名抽象，你等於讓使用人強迫了解下層的東西/語言
![[IMG-Build Abstractions Not Illusions by Gregor Hohpe-20250807202916758.png]]


# 抽像 leak


- 一定都會有抽像 leak, 但夠用就好
	- 因為邏輯層抽象一定無法完全 abstract away 底下的物理層
- 也提到就算是很好的抽象 -> like 插座 也會因為停電而失效
	- 但是如果你有 UPS 不斷電系統(`All problems in computer science can be solved by another level of indirection`) 就可以解



- rpc 就是一個不夠好的抽象, 因為有很多details都應該要關心(如下), 但這個名字讓你以為就是 fn call XD
![[IMG-Build Abstractions Not Illusions by Gregor Hohpe-20250807202917466.png]]



# 抽象可以用不同的維度去看
![[IMG-Build Abstractions Not Illusions by Gregor Hohpe-20250807202918440.png]]


下圖左邊只有資料流
右邊提供一個新的維度，控制流
![[IMG-Build Abstractions Not Illusions by Gregor Hohpe-20250807202919325.png]]



# 資料流和控制流
![[IMG-Build Abstractions Not Illusions by Gregor Hohpe-20250807202920309.png]]


# API設計讓你可以看清楚 control flow
![[IMG-Build Abstractions Not Illusions by Gregor Hohpe-20250807202921500.png]]
 



# 遺漏了重要的細節，你反而建立一個危險的幻覺

- 抽象的level需要適合，不會太多 -> congnative lod
	- 但也要 expose 應該要 expose 的讓人可以百花齊放，可以更好運用
	- 如果你遺漏了重要的細節，你反而建立一個危險的幻覺
![[IMG-Build Abstractions Not Illusions by Gregor Hohpe-20250807202922823.png]]


好抽象
- 降低認知負擔
- 需要不同 level 的語言
- 重要的細節需要 expose
![[IMG-Build Abstractions Not Illusions by Gregor Hohpe-20250807202924181.png]]