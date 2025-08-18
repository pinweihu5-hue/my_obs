
[(796) Core Design Principles for Software Developers by Venkat Subramaniam - YouTube](https://www.youtube.com/watch?v=llGgO74uXMI&list=WL&index=5&t=8636s)


TIL
- a good design is almost impossible to get it right the first time, it takes iterations, 很多好作品，不同領域，都是要不斷的修改後形成的。
- To create good design first step and also the most difficult step is let go of the ego. not attach to the ur solution
- simple is not familiar, simple means the code is easier to understand, easir to focus on its logic
- A good design is the one that hides inherent complexity and eliminates the accidental complexity
- YANGI(y)
	- 要問自己，這個可以之後再寫嗎？我只要確定現在不會讓未來難改就好。



---


# what is a good design
- a good design is a deisgn that is easier to change
	- almost impossible to get it right the first time
	- it takes iterations
	- 很多好作品，不同領域，都是要不斷的修改後形成的。
	- Software is never written, it is always rewritten .
- To create good design first step and also the most difficult step is let go of the ego.
	- ego is like 膽固醇, you need a certain amt of it, but too much is bad.
- be unemotioanl about not attach to the ur solution



# keep is simple
- what is simple (a jr said, it's simple, we only use if, 50 times, only if statement)
- simple is not familiar
- simple code make you eaier to understand and keep u focus while reading




# complex
- inherent and accidental
- accidental complex comes from the solution we use
- like we use concurrency (bring complexity) when we don't need it
- A good design is the one that hides inherent complexity and eliminates the accidental complexity.


# YANGI(y)
- You Aren't Gonna Need It (yet)
- 問自己，我是否需要 now! or do it later?
- how much do I know? cost of impl?
- 如果可以 defer decision to later, future, not impl now
- 到未來，更多可能會發生，類似可以真正確認系統的影響, 寫了可以產生 value, 再寫
- 要問自己，這個可以之後再寫嗎？我只要確定現在不會讓未來難改就好。
- 作者提到鎖螺絲，四個螺絲都不要鎖太死，然後確定了再鎖好
	- 類似觀念，我們寫code,  一開始也不要都定死
- tdd 也類似
	- 寫 test -> fail -> write code to pass it
- 作者提到一個經驗
	- tdd, 寫 edge case, 然後再修正，沒想到卻會 code
	- 但是 code 沒有 handle阿!! 後來 dig more -> 原來套件已經幫忙handle!!
	- 因此如果寫先 code 再寫 test -> 你可能根本就是多寫的
![[IMG-Core Design Principles for Software Developers by Venkat Subramaniam-20250807202916376.png]]

Why don't we postone? We are scared
Manual testing -> 最後一天要改code -> are you crazy
Automated testing -> 最後一天要改code -> give it try

If you want to postone we need to good automated testing .

# cohesion
We want software to change, but not too expensive
If a code is cohesive, it has to change less frequently
like things stay together, unlike thing stay apart
(作者舉例，他小時候都放學都ㄌ亂丟東西，因為方便，他要衝出去玩，但是要找就很麻煩)


# coupling

coupling what you depend on .
Worst form of coupling - Inheritance
Try to see if you can remove coupling.

"knock out (dependency) before you mock out" (如果可以不用 dependency, 設計更好! less coupling)



But we can't remove all the depencies.
1. get rid of it
2. Make it loose instead of tight
Depending on a class is tight coupling
Depending on an interface is 1oose coupling
Use caution.

A good design has high cohesion and low coupling


# DRY
Don't Repeat yourself
Don't duplicate code and also effort
Every piece of knowledge in a system should have a single unambiguous authorative represention .
如果你發現一樣的邏輯散落在多地方，應該就一個地方處理
goal: It reduces the cost of development

why should we care? the future you will thank you



# Long methods
Long methods are bad because:
- hard to test
- hard to read
- hard to remember
- obscured business rules
- hard to reuse
- leads to duplication
- many reasons to change
- can't be optimized by anything
- lot of variables and .
- not developers friendly
- mixed levels
- 1ow cohesion
- high coupling
- obsolete comments
- hard to debug
- ...



有人問你，你週末去哪裡..
我走西門路往左轉
然後我安北走路往左轉
之後我去廁所了一下
然後我洗手
...
...
你會這樣回嗎?
那你為何要這樣寫 code?
(this is what long method looks like)

how long means long func?
it's not about line
it's not about fit into screen
it's about SLAP: single level of abstraction principle

你週末去哪裡?
回答：park on sat, mondy on sunday  (level is 星期幾的活動)


comment:
不要用 comment 掩蓋 bad code
refactor code instead

don't comment what, instead comment why


a code is like a joke: a good code no need to explain, a bad code need to explain


[[Compose Method Pattern]]


# OCP
Software module should be open for extension but closed from modification .
Abstraction and polymorphism are the key to make this happen .

two options - to make an enhancement
1. change existing code
2. add a small new module of code

不過 extensible 是有一個特定目標的，不是什麼都可以 extensive
另外 make it extensilble bring complexity



Who can make code extensible?
We need to know software and domain .
There are three kinds of people we work with:
1. know domain really wel1, knows no software
2. know no domain, know software really well -> need to ask domian expert to know which direction we need to plan for extensibility
3. know domain really well and know software really well -> need this to really design extensiblity



# LSP
Inheritance overused

Inheritance should be used only for substitutability.
If an obiect of B should be used anywhere an obiect of A is used then use inheritance.
If an object of B should use an object of A, then use composition / delegation.

Inheritance demands more from a developer than composition or delegation does.
Services of the derived class should require no more and promise no less than the corresponding services of the base class .

Why?
The user of a base class should be able to use an instance of a derived class without knowing the difference.




Good
- public vs protected in base vs. derived
	- derived function can't throw any new checked exception not thrown by the base (unless the new exception extends the old one )
- Collection of derived does not extend from collection of base


Use composition or delegation instead of inheritance unless you want substitutability.




![[IMG-Core Design Principles for Software Developers by Venkat Subramaniam-20250807202916792.png]]

![[IMG-Core Design Principles for Software Developers by Venkat Subramaniam-20250807202917473.png]]


==
ruby, 這就沒有違反上面那些問題
因為 ruby 底層有幫你處理
ruby 的這些 code在 bytecode層級 vs 上面那些 java code violate OCP and DRT, 也就是說你要 maintain的 java code
(modern java 應該是有套件來幫你處理依賴注入了)
![[IMG-Core Design Principles for Software Developers by Venkat Subramaniam-20250807202918447.png]]


# DIP

dependent inversion principle
A class should not depend on another class, they both have to depend on an abstraction Cinterface).

Use caution.


not all the time, nut mostly of the time, we use DIP to help us to get OCP


![[IMG-Core Design Principles for Software Developers by Venkat Subramaniam-20250807202919332.png|553]]


stgy pattern 就用了 DIP



作者提到除了用 interface, 也可以用 functional Interface (in Java)
![[IMG-Core Design Principles for Software Developers by Venkat Subramaniam-20250807202920316.png|500]]


# ISP

Keep Interfaces Cohesive wi th Interface Segregation Principle

cohesion at interface level
not fat interface




DRY
YAGNI
SRP
OCP
LSP
ISP
DIP



[agiledeveloper.com/index.html](https://agiledeveloper.com/index.html)