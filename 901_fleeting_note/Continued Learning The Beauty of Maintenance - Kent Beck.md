[(793) Continued Learning: The Beauty of Maintenance - Kent Beck - DDD Europe 2020 - YouTube](https://www.youtube.com/watch?v=3gib0hKYjB0&t=0s)

- coupling and cohesion -> make change ez


# coupling 的精準定義
給定 A, B 兩個 element (can be fun, module, package, service, expression, argument...都可以)
A, B coupled 表示 有一個 change (下圖三角形)
如果 A 改了, B 就要改  -> 就是 coupling
![[IMG-Continued Learning The Beauty of Maintenance - Kent Beck-20250807202916319.png]]

例子
say you have a 資料結構 in A1，裡面有 A, B 兩個key -> say you pass from service A1 -> A2 -> A3
then A2 and A3 都會使用這兩個 key
如果你改了A1 的 B to C, 那你 A2, A3 service 都要改 
這表示 A1, A2, A3 coupling to this 資料結構

要你要怎麼改? migrate this? 如何同時讓三個A1,a2,a3 上線然後不會噴錯?

# **建新拆舊** / parallel
不要砍掉b
你先建立c
c 裡面放的就是 b 的 val
然後A1 上線
a2, a3 都也上新版，也都加上可以用c 的邏輯，但沒有拔掉 用b的邏輯
這樣 a2, a3 舊版的使用者的就可以用b
然後部分新版(有升級)的使用者就會用c
等到舊版的使用者全部都不見了
那我們再把 b拆掉
![[IMG-Continued Learning The Beauty of Maintenance - Kent Beck-20250807202916650.png]]


life lesson 也是 programming  concept:
- life always get worse if you want to get better
	- related [[Thinking Like an Architect by Gregor Hohpe#life always get worse if you want to get better]]
- you can do it 





# cost is long tail
change 就是成本
大多數成本都是那些 big chnage
很多改動的 cost 都小小的
但有些改動會有 cascade effect, like 雪崩,  就是 coupling 導致的
like long tail -> 會由最右邊的那些 dominate
![[IMG-Continued Learning The Beauty of Maintenance - Kent Beck-20250807202917353.png]]


# cohesion 定義

cohesion
一個 element, 下面有很多 sub ele
如果我改了一個，剩下的我都可能要 check 需要去改
因為我們無論如何都會有 coupking, 因此這是縮小 coupling 的 scope 的作法


上面比較cohesion

上圖
say 這是一個檔案，裡面有4個 fun
如果我改第一個，我第二個 fun也要改
然後我檢查另外兩個要不要改


下圖
我分成兩個檔案
我連檢查另一邊要不要改都不用
![[IMG-Continued Learning The Beauty of Maintenance - Kent Beck-20250807202918147.png]]



cost of coupling -> ripple effect -> 要改很多
cost of decoupling -> 更複雜架構和設計 -> 更難 maintain
兩者要找到平衡


# tidy first
talk about "tidy first" , 作者的書 -> 看 pdf 的highlights
- [[Kent Back, tidy first]]
是否要先 tidy? -> also a trade-off


解 couple 有時候很難，不好分析
我們可以從 cohesion下手
我們可以試著讓一起變動的東西放在一起, make it cohesion
這些東西本來就會要一起調整，都會一起 examine



兩種 style
lumper and splitter
related: [[Can Great Programmers Be Taught? - Prof. Dr. John Ousterhout#class should be deep]]

各有好壞




可以先改結構，然後行為比較好改
tidy first
like 你要改 db
你要動5個以上不同的地方
先把他們放在一起，然後再改
![[IMG-Continued Learning The Beauty of Maintenance - Kent Beck-20250807202919036.png]]


tidy first:
"make change easy" first then we can "make easy change"


盡量分開 strcuture change 和 behavior change into 2 MR
或是沒辦法的話，也可以先 behavior change 再 clean up


why?

因為結構改變你可以簡單的 redo
但是行為改變無法

分成兩個 mr 去檢視也讓你比較輕鬆去確認對錯



