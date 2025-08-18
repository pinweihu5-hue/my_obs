
我們來學一些，建立比較大型軟體會遇到的問題
![[IMG-cs61b Software Engineering I-20241003104933889.png]]


參考這本書
![[IMG-cs61b Software Engineering I-20241003104948820.png]]


# Complexity Defined

跟硬科學比，軟體工作有更多自我發揮的空見，因為不會總是被law of physic 限制 (expcep cpu and mem)
![[IMG-cs61b Software Engineering I-20241003105003936.png]]


目標是對抗複雜性
![[IMG-cs61b Software Engineering I-20241003105023178.png]]


兩方法降低複雜度
- 讓 code simpe and obvious
- 模組化，好的抽象
![[IMG-cs61b Software Engineering I-20241003105049438.png]]


what is 高複雜?
- 難改
	- 改一個要改很多地方，或是有些隱藏的地方
	- 改一個東西的 effort or cost 很大
- 不好懂
	- 太多if-else
	- 太多重複的東西也會不好懂
	- 太多隱藏的概念
![[IMG-cs61b Software Engineering I-20241003105115655.png]]

![[IMG-cs61b Software Engineering I-20241003105115841.png]]

k-d tree 的例子
![[IMG-cs61b Software Engineering I-20241003105131093.png]]


code see [here](https://gist.github.com/re4388/85ef354a7e63ba1ec50ae237e8ca1c91)
why this is too complicated?
- 太多重複
- 很難一般化, hard to extent
- 很難 debug
- 很多 邏輯 分支
- 很難跟人解釋，或是跟自己解釋這些code
![[IMG-cs61b Software Engineering I-20241003105131175.png]]

就算是小函數，也是可以搞的很複雜
 a lot of if else
![[IMG-cs61b Software Engineering I-20241003105139920.png]]




換的角度說，如果有一個函數很複雜，但是沒有跟其他部分  couple, 那對整體的複雜度不會太大

但是如果有一個函數相對簡單，但是這個函數會被很多地方影響到，或是影響很多地方，那這個函數的總複雜度影響還比較大
![[IMG-cs61b Software Engineering I-20241003105140034.png]]


# Symptoms and Causes of Complexity


- Is about how much you need to know to make this change, not how much lines of code
- `return x++`  this means you return x and then increment it, 這個有時候看漏，就是 bug
![[IMG-cs61b Software Engineering I-20241003105147912.png]]![[IMG-cs61b Software Engineering I-20241003105148023.png]]


很多依賴
很難懂
![[IMG-cs61b Software Engineering I-20241003105154319.png]]

what is obvious system?
![[IMG-cs61b Software Engineering I-20241003105159962.png]]


會慢慢的越來越複雜
因此建議心態上應該是 zero tolerance
![[IMG-cs61b Software Engineering I-20241003105208034.png]]


# Strategic vs. Tactical Programming

![[IMG-cs61b Software Engineering I-20241003105208229.png]]


可以 work 就好
不思考整體設計
不思考是否會讓複雜度增加但卻可以避免
![[IMG-cs61b Software Engineering I-20241003105212724.png]]


working code is not enough
應該要去思考架構是否合理，是否有擴充性
![[IMG-cs61b Software Engineering I-20241003105216248.png]]



多練習
寫完 working code 
- 想想是否有其他更好的方法，但是有更好的擴充性
- 想想未來有可能會怎麼使用
	- 是否可以讓你的設計可以讓未來要擴充可以"很好改"
	- 或是你的設計可以讓未來要擴充時，不會整個要重寫
	- 或是你的設計可以讓未來要擴充時，都不會被用到(但是有關連)
![[IMG-cs61b Software Engineering I-20241003105219731.png]]


不要習慣性的 patch, patch patch
要思考要如何設計一個系統/架構/機制，可以讓未來不會有錯誤，或是讓未來可以擴充

好例子： sentinel nodes
![[IMG-cs61b Software Engineering I-20241003105220201.png]]


FB 的例子
一開始都是 tactical programing
![[IMG-cs61b Software Engineering I-20241003105224290.png|768]]

google 就很重視 code quality
![[IMG-cs61b Software Engineering I-20241003105228319.png]]


# Seeking Obvious Code through Decomposition

decompose 邏輯是一招，讓 code 比較好懂
![[IMG-cs61b Software Engineering I-20241003105231885.png]]

問題
![[IMG-cs61b Software Engineering I-20241003105235569.png|753]]

![[IMG-cs61b Software Engineering I-20241003105238303.png|737]]

![[IMG-cs61b Software Engineering I-20241003105240922.png|768]]

![[IMG-cs61b Software Engineering I-20241003105243427.png]]

my first though



先把 MAX_X and MAX_Y 分別 cut to 4 interval
x_interval_1 is [0, MAX_X*  1/4]
x_interval_2 is [0, MAX_X* 2/4]
x_interval_3 is [0, MAX_X* 3/4]
x_interval_4 is [0, MAX_X* 4/4]

y 以此類推



input: 給定一個 range, like 20-40
output: 給我落在哪幾個 interval 中: x_interval_1 and x_interval_2
getOverlapInterval(input): output


x_interval is x_interval_1, x_interval_2, x_interval_3,x_interval_4 ，就是 x 切成四分等, enum
y_interval is y_interval_1, y_interval_2, y_interval_3,y_interval_4 ，就是 y 切成四分等, enum
input: x_interval_1, y_interval_3 
output: "ba"
getGridName(x_interval, y_interval) 


==
這邊作者的重點是，使用一些 obvious helper method will allow ur code easier to reason abt