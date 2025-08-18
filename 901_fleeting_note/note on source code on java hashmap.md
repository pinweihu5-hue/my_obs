

下面是計算出 hashMap 的 table length
table 是一個變數，array,  就是 buckets


- 下面透過對 n 進行多次的右移
- 不一定每一次都需要
- 結果就是讓數字變成 111111
- 然後最後加一，進位，變成 1000000
- 這樣長度就會是 2 的冪次

這是為了讓後續的取模操作可以更快和更多優化


![[IMG-note on source code on java hashmap-20250214082112757.png]]


---


why hash & (len-1)

-> 等價於 hash % len, 但是更高效, 只要 len 是 2 的冪次
[[math 取模]]

![[IMG-note on source code on java hashmap-20250222193846534.png]]



explain below?
![[IMG-note on source code on java hashmap-20250807203854679.png]]

對 h 左移 16 bit 拿到 16 higher bit
然後跟自己的 lower bit 進行 異或 運算
這樣可以增加這個數的隨機性
![[IMG-note on source code on java hashmap-20250807203856322.png]]

換個角度說，如果你直接拿 n-1 和 hash 進行 ^ 
基本上就只有最左邊 4 bit 有影響
左邊都沒影響
這樣基本上結果很容易都會落在同一個 bucket



==

v1.7 版本的 hash也是進行很多次的 ^ 來增加隨機性，減少碰撞
![[IMG-note on source code on java hashmap-20250807203858065.png]]