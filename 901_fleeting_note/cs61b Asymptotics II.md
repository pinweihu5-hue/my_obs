

下面討論 算 order of growth 的方法

直接算數量，加起來
![[IMG-cs61b Asymptotics II-20241003104933871.png]]


# 題目，下面的複雜度?
![[IMG-cs61b Asymptotics II-20241003104948804.png|770]]

提示1: 給你 cost model
![[IMG-cs61b Asymptotics II-20241003105002939.png|786]]

答案

看圖的話，介在 2 N and 0.5N 之間，算是 N
另外如果直接算 
1+2+4+8+17.... = 2N - 1  => 一樣是 N, linear 複雜度
![[IMG-cs61b Asymptotics II-20241003105003036.png|774]]




作者說
除非你有經驗，不然沒有用 "看" 的一些法則可以幫你用看 code 就知道複雜度 

除非你已經遇到，不然第一次遇到，你都要用以下幾種
- 畫圖
- 算出 sum
	- 下面列出兩個常用的 sum
		- 自然數加總 -> N(N+1)/2  -> N^2
		- 2的 power 加總 -> 2N - 1  -> N
- 舉例
![[IMG-cs61b Asymptotics II-20241003105021550.png|816]]




# 遞迴

以這個例子就是一個函數裡面會呼叫兩個自己
直覺上就是 2 倍的速度增加
就是呼叫  3 次 就是 `2*2*2`      2^3
        4              `2*2*2*2`  2^4
...

-> 2^n
![[IMG-cs61b Asymptotics II-20241003105021748.png|816]]


我們來算 sum

先找 final term
![[IMG-cs61b Asymptotics II-20241003105047952.png|699]]

1+2+4+...+2^(N-1)
加總 -> 2^N - 1
![[IMG-cs61b Asymptotics II-20241003105048126.png|793]]

加總的數學，就是代入之前"2的指數和公式"就可以算出
![[IMG-cs61b Asymptotics II-20241003105114943.png|721]]


另一種就是 approach
利用遞迴的方式去推導
C(1) = 1
C(N) = 2倍的 C 乘以 (N-1) + 1
note: 2倍的C 是因為 call 2 f3 for each fn, N-1 is cuz 少一個 N after first call
![[IMG-cs61b Asymptotics II-20241003105129863.png|805]]


# Binary search

[[CS61B Data Structures and Algorithms|cs61b_index]]


概念單純，但是實作要做好有坑, java 到 2006年還被發現有 bug
[Extra, Extra - Read All About It: Nearly All Binary Searches and Mergesorts are](https://research.google/blog/extra-extra-read-all-about-it-nearly-all-binary-searches-and-mergesorts-are-broken/) and its note: [[java binary search bug]]
![[IMG-cs61b Asymptotics II-20241003105130062.png|798]]



直覺上
就是每次減一半
N -> N/2 -> N/4 -> N/8 ...
N -> N/(2^1) -> N/(2^2) -> N/(2^3) ...
N/2^(c) 

因此 c = log2(N)
![[IMG-cs61b Asymptotics II-20241003105138897.png|833]]