[(780) How to Think Like an Architect - Mark Richards - YouTube](https://www.youtube.com/watch?v=W7Krz__jJUg&list=WL&index=3)


談msg 要用key-based 還是 full-payload?

這是一個tradoff
- key-based
	- 好處包括:bandwith cost, 不需要處理版本(因為你會帶一堆東西了), 另外也不需要處理 stamp coupling(就是很多東西都沒用到), 最後也避免 data integrity 問題(你傳整包，傳的時候，如果 db 資料有變化，這樣兩個資料不同耶)
- full-payload
	- 好處: 好 scale, 因為不需要去 db, 可能 perf 也會比較好

![[IMG-How to Think Like an Architect - Mark Richards-20250807202917867.png|670]]

以下三點是架構思考需要有的


1. biz need <-> 軟體 capability
	- echo to architect elevator, [[Thinking Like an Architect by Gregor Hohpe#Connect levels]]
![[IMG-How to Think Like an Architect - Mark Richards-20250807202918655.png|694]]


2. 建立出技術的深度
![[IMG-How to Think Like an Architect - Mark Richards-20250807202919457.png|700]]


3. 一定有 tradeOff, 只是看你有沒有深度分析
![[IMG-How to Think Like an Architect - Mark Richards-20250807202920445.png|548]]


# 把 biz need -> 架構特性

要可以翻譯 from biz -> 技術
![[IMG-How to Think Like an Architect - Mark Richards-20250807202921620.png]]



![[IMG-How to Think Like an Architect - Mark Richards-20250807202922947.png]]


需要翻譯
![[IMG-How to Think Like an Architect - Mark Richards-20250807202924320.png|788]]


那到底我們需要哪些特性? 從下面這些地方去思考
1. biz domain
2. user story, req
3. discuss and engaging to biz ppl



![[IMG-How to Think Like an Architect - Mark Richards-20250807202925746.png]]


另外一個例子
time to market
- maintainablity
- testablity
- deployability


![[IMG-How to Think Like an Architect - Mark Richards-20250807202928329.png]]


![[IMG-How to Think Like an Architect - Mark Richards-20250807202929373.png]]


如果 scale 很重要，那你大概會選擇這三種
![[IMG-How to Think Like an Architect - Mark Richards-20250807202930339.png]]



如果 cost 和簡單很重要..
![[IMG-How to Think Like an Architect - Mark Richards-20250807202931145.png]]


可以來這裡下載
和對每一個特徵更細節的介紹
[Resources | Developer to Architect | Mark Richards](https://www.developertoarchitect.com/resources/)

![[IMG-How to Think Like an Architect - Mark Richards-20250807202931733.png]]



你不能沒有左邊作為一開始，然後就有右邊的架構
![[IMG-How to Think Like an Architect - Mark Richards-20250807202932718.png]]


# Technical Depth vs. Breadth


知識三角形
![[IMG-How to Think Like an Architect - Mark Richards-20250807202933259.png|780]]


game of life:
把最下面的東西移動到上面
how?
talk to ppl
go to conference
see new vid
![[IMG-How to Think Like an Architect - Mark Richards-20250807202933777.png]]
這樣哪天有需要，你就會知道...好像有這個可以試試看喔



when to move from "第二層" 到"第一層"?
這就是當你老闆說，下禮拜我們開始要用 zoo keeper
-> 這時候，你聽過, 但沒用過，你就可以開始學
![[IMG-How to Think Like an Architect - Mark Richards-20250807202934459.png]]




cautionaly tale:最上面你懂的，你要 maintain 才會 keep
不然又會變成下面
![[IMG-How to Think Like an Architect - Mark Richards-20250807202934940.png]]

![[IMG-How to Think Like an Architect - Mark Richards-20250807202935347.png]]



架構思考要花很多時間在中間層
![[IMG-How to Think Like an Architect - Mark Richards-20250807202935804.png]]



jr. 最上面比較多
![[IMG-How to Think Like an Architect - Mark Richards-20250807202936251.png]]


sr, 上面長更大
下面一樣很沒啥花時間學
![[IMG-How to Think Like an Architect - Mark Richards-20250807202936765.png]]


jr 架構 -> 開始經營中間層
![[IMG-How to Think Like an Architect - Mark Richards-20250807202937225.png|815]]


sr 架構師
中間層更大
![[IMG-How to Think Like an Architect - Mark Richards-20250807202937671.png|823]]




你要知道更多 tech, 才可以更好的找到最適合的 solution given current ctx


作者介紹幾個網站讓你探索
[InfoQ: Software Development News, Trends & Best Practices - InfoQ](www.infoq.com.md)
[Technology Radar | Guide to technology landscape | Thoughtworks](https://www.thoughtworks.com/radar)
[DZone: Programming & DevOps news, tutorials & tools](https://dzone.com/refcardz)
![[IMG-How to Think Like an Architect - Mark Richards-20250807202938338.png|662]]


打開 mail, 或是看 infoQ
找出 you don't know you don't know -> **找出那些從沒看過的東西** -> know what it is and when to use it or not

![[IMG-How to Think Like an Architect - Mark Richards-20250807202938785.png|548]]



一天 20 min
![[IMG-How to Think Like an Architect - Mark Richards-20250807202944277.png|576]]


# Analyzing Trade-offs

多數 開發者都只看到 benefit 但沒看到 tradeOff

![[IMG-How to Think Like an Architect - Mark Richards-20250807202945683.png|610]]



there is NO best practice for what architecture to use -> it all depends on your specific, current context, 100 % contextual



trade off 怎麼選?
看 biz need
![[IMG-How to Think Like an Architect - Mark Richards-20250807202946659.png|814]]



小心 out of ctx
![[IMG-How to Think Like an Architect - Mark Richards-20250807202947799.png]]



![[IMG-How to Think Like an Architect - Mark Richards-20250807202952571.png]]

![[IMG-How to Think Like an Architect - Mark Richards-20250807202953485.png]]

選 shared lib? no! 要看 ctx
![[IMG-How to Think Like an Architect - Mark Richards-20250807202954309.png|879]]



![[IMG-How to Think Like an Architect - Mark Richards-20250807202954926.png]]