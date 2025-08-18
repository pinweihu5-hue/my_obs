ref: [Thinking Like an Architect by Gregor Hohpe - YouTube](https://www.youtube.com/watch?v=yflW4BG7Ves&t=2495s)


- Architects aren't the smartest people in the team. They make everyone else smarter.



how to make ppl smarter?
- Connect levels
- Sketch
- See more dimensions
- Sell options
- zoom in and out
- Make better decisions with models
- See shades of gray




"The value of a modern architect is measured by how many floors they can cover"


# Connect levels
說上層以為一切都很好，coder都可以做想要做的事情，但中間已經拖溝了，這是不好的
架構師就是可以填補這中間的gap
![[IMG-Thinking Like an Architect by Gregor Hohpe-20250807202922807.png]]

應該要變成
![[IMG-Thinking Like an Architect by Gregor Hohpe-20250807202924162.png]]
ref: [YouTube](https://www.youtube.com/watch?v=Zq2VcRZmz78&list=WL&index=9&t=785s)


目前組織往右邊轉變
另外也提到，你跟人互動要知道他們 care 什麼? 
你要搞 k8s, cicd, unit test (care speed)，但是他們 care cost -> 那就不是他們 care 的
![[IMG-Thinking Like an Architect by Gregor Hohpe-20250807202925519.png]]



layer 的好壞，要權衡
通常在一個快速變動的系統中，cost 很容易 outweight benefit
也提到
`All problems in computer science can be solved by another level of indirection`

作者這邊也提到，組織架構和軟體架構很多地方都是很像的
replacebility -> outsoucing
你如果組織沒有切好職責，你也很難外包
![[IMG-Thinking Like an Architect by Gregor Hohpe-20250807202927469.png]]
左邊比較是架構，靜態, static design
右邊比較是動態，效能, dynamic behavior



![[IMG-Thinking Like an Architect by Gregor Hohpe-20250807202929191.png]]


![[IMG-Thinking Like an Architect by Gregor Hohpe-20250807202930308.png]]


Architect Insight
- Using metaphors invites your audience into the thought process
- Not inviting your audience into the thought process is an underutilization of mental resources.


# Sketch


那些 box 架構圖大家都會畫
但sketch 保留最 本質 的東西
opinionted
特定的 view point
但是可以很好的表達你的意見
![[IMG-Thinking Like an Architect by Gregor Hohpe-20250807202931053.png]]



# See more dimensions


沒時間了要儘快 vs 我們要品質
可以從其他維度切入
我們要 unit test, automate CI/CD 就可以幫助品質且讓開發快

不要陷入 a or b
要提供更多角度去思考問題
![[IMG-Thinking Like an Architect by Gregor Hohpe-20250807202931710.png]]


vendor lock in or not 是一個維度, 線性
另一個維度是 lock-in 提供的價值 -> 下此下圖就是一個 matrix 
![[IMG-Thinking Like an Architect by Gregor Hohpe-20250807202932669.png]]

![[IMG-Thinking Like an Architect by Gregor Hohpe-20250807202933239.png]]



# sell options 選擇權/期貨



就是 期貨
讓你可以買未來選擇的權力

類似你花 xx 元讓你一年後可以買 yy 股
你預期一年後這個股要不要買可以更清楚

下面的西隔馬^2 的那個 西隔馬就是不確定性, 變動性

變動性越高，defer decision 就越有價值

![[IMG-Thinking Like an Architect by Gregor Hohpe-20250807202933756.png]]


trading option
有很多選擇
但是你要根據目標，進行取捨

如果很簡單，類似10個 user, 不需要 sell option 了 XD 
1. 通常 sell option comes with cost of 複雜度
2. 通常越高的不確定和變動， 架構(師)的重要性就越高  -> 我們就需要有一些 auto scale 的架構


我們加入 harmonization 層的限制 (standard interface, standard api) -> give up 
但是讓我們可以有更多使用不同語言的好處 -> gain
![[IMG-Thinking Like an Architect by Gregor Hohpe-20250807202934323.png]]



用scale 舉例，如果你很明確使用人數，那你很好去架構
但是如果很多不確定性，like black friday等等，那你就需要一個可以 可以 auto scale 的架構去處理,
defer decision 或是甚至都不用作決定
- related: [[延遲決策 - Deferred Decision Making]]

aglile 適合於 uncertainty, volitility 環境
而架構師就是善於在這個環境提供的 option
![[IMG-Thinking Like an Architect by Gregor Hohpe-20250807202934924.png]]


# zoom in and out

boss and 員工看那一個 cloud 適合用的角度 level不同
一個看那一個未來可以幫助就業
一個看哪一個好找人，成本合理
![[IMG-Thinking Like an Architect by Gregor Hohpe-20250807202935330.png]]



ppl don't care about ur 架構
ppl 只會 care 架構帶來的 capability
![[IMG-Thinking Like an Architect by Gregor Hohpe-20250807202935787.png|530]]
![[IMG-Thinking Like an Architect by Gregor Hohpe-20250807202936238.png|532]]


left and right  
component一樣，但特徵差很多
左邊依賴關係簡單，複雜度低, 好替換。
右邊依賴關係複雜多了，不好替換，但也有了一定程度的 availability, 如果有東西 down, 可能還可以用

架構師 like 廚師
component/材料 重要，但要如何組裝/搭配原料 更重要!

![[IMG-Thinking Like an Architect by Gregor Hohpe-20250807202936752.png]]



不只是 non-funcitonal req, 也要處理沒有列入 req 但應該要列入的東西
![[IMG-Thinking Like an Architect by Gregor Hohpe-20250807202937169.png|695]]



antifragile example: like chaos-monkey system, 刻意製造意外, injecting distirbance (其實人類本質就是，打疫苗就是 injecting distirbance )
![[IMG-Thinking Like an Architect by Gregor Hohpe-20250807202937657.png]]
- [BTD12: The Architect Elevator: Connecting Penthouse and Engine - Gregor Hohpe - YouTube](https://www.youtube.com/watch?v=Zq2VcRZmz78&list=WL&index=9&t=785s)



這邊是說，大家都在做自己認為對的事情，最後就是惡性 spin
因此要改要整個系統的調整, this is how 架構師要了解的
![[IMG-Thinking Like an Architect by Gregor Hohpe-20250807202938070.png]]


# life always get worse if you want to get better
會先下去才會再上去
但是如果不 get worse, 只會更 worse
![[IMG-Thinking Like an Architect by Gregor Hohpe-20250807202938730.png|628]]



小心不要 local optimum
![[IMG-Thinking Like an Architect by Gregor Hohpe-20250807202944143.png]]


大家都很快，不代表 system很快
![[IMG-Thinking Like an Architect by Gregor Hohpe-20250807202945576.png|575]]


Architect Insight: "The sum of local optima is rarely a global optimum”

# Make better decisions with models


好的模型讓我們可以更好思考
![[IMG-Thinking Like an Architect by Gregor Hohpe-20250807202946568.png]]

![[IMG-Thinking Like an Architect by Gregor Hohpe-20250807202947679.png]]


你要用哪一個 model 要看你問那一個問題
你要如何架構，也要看你的問題和目標是什麼
![[IMG-Thinking Like an Architect by Gregor Hohpe-20250807202948439.png]]

Architect Insight
- "No tapestries": Simple models are most helpful because they abstract the most and bring clarity
- “Model uncertainty away'": In face of uncertainty, models clarify assumptions and help you overcome paralysis.
- "Ask the question first”: To find a useful model, you need to have a question first,

跟上面一樣，不確定性高，越是 volitity, model 越重要


# 	See shades of gray


一定要用什麼，不可以用什麼 的詞彙是一個 warning sign
![[IMG-Thinking Like an Architect by Gregor Hohpe-20250807202953416.png]]



移轉成本很大
但是我們可以 over time 去 invest 讓我們好移轉的技術 (類似使用一些可以跨雲的技術，轉成為container, 把一些無法移轉的東西改為可以移轉的技術等等)
移轉成本 x 移轉的可能性就是我們的 liability

下圖是說，要找中間點， sweet spot
不要都不投資在上面那些技術, 這樣很天真
也不要花太多錢在那些技術，這樣你乾脆就 open source
要找到中間的 sweet spot

你也可以加入 discount rate 的思考，進行更多模型上的思考

![[IMG-Thinking Like an Architect by Gregor Hohpe-20250807202954239.png]]



作者的書
![[IMG-Thinking Like an Architect by Gregor Hohpe-20250807202954867.png]]