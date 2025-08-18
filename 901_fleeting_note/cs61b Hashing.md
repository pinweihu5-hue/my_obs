[[CS61B Data Structures and Algorithms|cs61b_index]]
# Data Indexed Arrays

quick review
![[IMG-cs61b Hashing-20241003104933885.png|796]]

2 points to consider
- use tree as map, we need to the element comparable (可以比較)
- logN for tree..but can we faster?
  ![[IMG-cs61b Hashing-20241003104948815.png|874]]

- data is idx
- if we add it, we turn it to True
  ![[IMG-cs61b Hashing-20241003105003466.png|827]]

init all boolean with false

two method

- add
	- flip false to true
  contains
	- check if it is true
  ![[IMG-cs61b Hashing-20241003105003548.png|896]]

big O of 1
![[IMG-cs61b Hashing-20241003105022510.png]]

But.........

- waste a lot of mem sonce a lot of space is not used
- 只能放 integer - since we use idx to as our data
  ![[IMG-cs61b Hashing-20241003105022676.png]]

# DataIndexedEnglishWordSet

先談放 string
![[IMG-cs61b Hashing-20241003105048713.png]]

use first letter as an idx?
- a = 1, b=2, c=3, ... z = 26
- 很容易就衝突了, like "cat", "coco"
  ![[IMG-cs61b Hashing-20241003105048887.png]]

用下面的方法來避免衝突
- 模仿數字是如果用10進位來儲存的
- 7091 = 7×10$^3$  + 0×10$^2$ + 9×1$^1$  + 1×10$^0$ 
- 我們透過這個方式，讓每個數字都是唯一的，獨一無二的
- we called it base10

- a~z 共 26 個 digit
- +1 算為 27 (why?)
- cat -> c = 3, a = 1, t = 20
- 3x27$^2$ + 1x26$^1$ + 20x26$^0$
- base27
- 只要 base >= 26, this is collison free for hash lower-case English
![[IMG-cs61b Hashing-20241003105115221.png|920]]

7091 用 base10 拆解
![[IMG-cs61b Hashing-20241003105130299.png|857]]

try it
![[IMG-cs61b Hashing-20241003105130449.png|814]]

![[IMG-cs61b Hashing-20241003105139192.png]]

![[IMG-cs61b Hashing-20241003105139335.png|756]]

![[IMG-cs61b Hashing-20241003105147145.png|791]]

![[IMG-cs61b Hashing-20241003105147265.png|769]]

把 string 轉為 int, as a indx, 把那個 idx 設為 true
![[IMG-cs61b Hashing-20241003105153764.png|823]]

# DataIndexedStringSet

想要存不只 26 lower case 怎半?
![[IMG-cs61b Hashing-20241003105153873.png]]

introduce ASCII

in java, char 和 integer 是對應的
how 對應
the mapping is ASCII
![[IMG-cs61b Hashing-20241003105159430.png|768]]

so we can use base126
-> 確保 獨一無二 for ascii base text
![[IMG-cs61b Hashing-20241003105159508.png|788]]

更好實作
no need helper method
just use raw char 本身可以轉為 integer
![[IMG-cs61b Hashing-20241003105207461.png|821]]

如果要 涵蓋超過 ascii? -> unicode!
![[IMG-cs61b Hashing-20241003105207554.png]]

例子：
![[IMG-cs61b Hashing-20241003105211832.png|855]]

# Integer Overflow and Hash Codes

java, integer 最大是 2,147,483,647 ->  > 2 billion 
超過就是 interger overflow
then 就是從最小開始

上面 unicode 三個字就已經超過
不用 unicide, 如果字數太多，也會超過
![[IMG-cs61b Hashing-20241003105215509.png]]

因此你就會有碰撞
如下例
![[IMG-cs61b Hashing-20241003105218824.png]]

hash code 本質就是把無限的東西 -> 有限的數字
因此碰撞是無法避免的

鴿籠問題
9 個籠子，10 個鴿子，你一定會有碰撞
![[IMG-cs61b Hashing-20241003105218927.png|841]]

Hash 的兩個要解決的問題
- 碰撞處理
- 如何算 hashCode
  ![[IMG-cs61b Hashing-20241003105223292.png]]

# Hash Tables: Handling Collisions

我們把會 hashcode 一樣的放到一個 bucket

bucket 可以如何實作?
很多方法
LL, array, Set, tree...等等等
![[IMG-cs61b Hashing-20241003105223439.png]]

add:
如果 bucket 是空的 -> 建立一個新的 list, x 放入 list
如果 bucket 已經有 list, if x 不存在(need to go thru to check)，加入 list
![[IMG-cs61b Hashing-20241003105227282.png|784]]

![[IMG-cs61b Hashing-20241003105227406.png]]

contains: \
compute the hashcode -> 找到 buclet -> 需要去看那個 bucket 裡面的 list, go thru, 看看是否存在
![[IMG-cs61b Hashing-20241003105230810.png|799]]

so..
contains and add 都是 theta Q
Q is length of longest list of all buckets
![[IMG-cs61b Hashing-20241003105230944.png|819]]

省點空間
![[IMG-cs61b Hashing-20241003105234645.png|756]]

say we use 10 bucekt and take mod
tip: mod 10 基本上就是看最後一個 digit
![[IMG-cs61b Hashing-20241003105234748.png|906]]

下面就是 how hash table work!
概念上的話
![[IMG-cs61b Hashing-20241003105237542.png|840]]

## Hash Table Performance

目前為止...
好消息：我們不需要百萬個 bucket
壞消息: runtime 是 theta Q
![[IMG-cs61b Hashing-20241003105237628.png|872]]

請問 Q 隨著 N 提高的提高速度是....
![[IMG-cs61b Hashing-20241003105239963.png]]

thea N!
很慘

![[IMG-cs61b Hashing-20241003105240082.png]]

問題
假設 item 都可以均勻分布的話 (這個假設要成立，要透過 how hash function work 決定)
那我們要怎麼做，才可以讓 thea Q 的增長速度隨持常數??
![[IMG-cs61b Hashing-20241003105242563.png]]

讓 bucket number, M 也一起 growth
![[IMG-cs61b Hashing-20241003105242648.png]]


基本上就類似 arrayList 的 resize 概念！
只要 M (bucket 數量)的 growth rate 跟 N(item number) 一樣，那 add, contain 的  平均 run time的就是  O(N/M), O(1)
![[IMG-cs61b Hashing-20241003105245105.png]]


下面是一個例子
![[IMG-cs61b Hashing-20241003105245220.png|856]]




doubling stgy 確保 worse case runtime 是 O(1)
doubling stgy 是指，每次 N/M 大於一個給定數字，類似上面的 1.5, 就 乘以某個倍數，類似 2
![[IMG-cs61b Hashing-20241003105247306.png]]


resize 的那一次，每一個 item 都需要重新去找新的 bucket，因此會是 O(N), 但大多數都是 O(1)
只要 resize  是 multiplicative 倍數，那 平均 runtime 就會是 O(1)
![[IMG-cs61b Hashing-20241003105247399.png]]


## 小節
要讓 平均 worse case O(1)
- 需要平均分配
- 隨 N 成長, M 用 某個倍數 resize
![[IMG-cs61b Hashing-20241003105249663.png]]





接下來，我們來討論，如何平均分配....
![[IMG-cs61b Hashing-20241003105249746.png|805]]



# Hash Tables in Java

hash table 是很熱門的實作 for sets and masps
- good perf in practice
- 不需要 item 是可比較的類型, comparable
- 實作相對簡單


Java 中有 java.util.HashMap 和 java.util.HashSet，都是用 hash table 去實作的 Map and Set



Java 中，HashMap and HashSet 中的 object/item 是如何確保 item 可以 hash
- 需要跟 iterable 一樣去 `implemetns hashable`?? -> 不是
- 這些 object 都會用一個函數: `hashCode()` 來算 hashCode
![[IMG-cs61b Hashing-20241003105254661.png]]


hashCode default impl ->  return obj mem addr
![[IMG-cs61b Hashing-20241003105254754.png|845]]


Javba 的 String 的 override 的 hashCode, 可以試試看
![[IMG-cs61b Hashing-20241003105256967.png|853]]

如何處理負的?


0 -> 0
1 -> 1
2-> 2
3 -> 3

4 -> 0
5 -> 1
6 -> 2
7 -> 3

8 -> 0
....0 ~4 依序
所以..
-1 -> ?

依照順序的話，-1 就是上一個 -> 3

![[IMG-cs61b Hashing-20241003105257070.png]]



use Math.floorMod can let you get the above result
![[IMG-cs61b Hashing-20241003105259028.png]]


this is how we do in Hash Tables in Java
出現negative number (overflow) -> 用 floorMod 來算負數
![[IMG-cs61b Hashing-20241003105259128.png]]



## 另一種確保 32-bit integer 的方法

This code masks off the sign bit (to turn the 32-bit integer into a 31-bit nonnegative integer)

- 0x7fffffff 是十六進位表示的整數，二進位表示為 0111 1111 1111 1111 1111 1111 1111 1111。
- 將 key.hashCode() 的結果與 0x7fffffff 進行位元 AND 運算。這會將 hash code 的**最高位元（符號位元）設為 0**，其他位元保持不變。
- 這個操作後，hash 結果仍然是 32 位元的整數，只是最高位元恆為 0
- 將可能為負數的 hash code 轉換為非負數
    
```java


private int hash(Key key) {
   return (key.hashCode() & 0x7fffffff) % M;
}

```



## 有關於選 table size M
- table size 要用 2 的冪次方作為大小, 就是 2 的 power 次方的, 類似4,8,32,64,128,256,....
	- 可以用 bit ADD 來做，會很快，因此除法很慢
	- 除非你用  2 的 power, `a % m` 才會等於 `a & (m - 1)`,  where is hashCode, m is bucket
	* [java - Why Are HashMaps Implemented Using Powers of Two? - Stack Overflow](https://stackoverflow.com/questions/53526790/why-are-hashmaps-implemented-using-powers-of-two)


下面列出取power of 2, 和 -1 後的 binary representation:
7 0111
8 1000

15 01111
16 1000

31  01 1111
32 10 0000


..


## Java 需同時覆寫 `equals()` 和 `hashCode()`

- All Java object 都有 hashCode 可以把 objects 轉為 integers
- Object 的 default 實作 for `x.equals(y)` 是 `x == y`   -> 是看記憶體位置
	- 不適合 string 比較，因為我們通常會想要  String A = "apple"  和 String B = "apple", 是一樣的，因此 String 有 override `equals`
- `x.hashCode()` 也是有跟上面一樣的情況， default 是 return 記憶體位置 -> 因此 String 也有 override  `hashCode()`



Java 兩個要注意的
- 如果key會變化，不要 存 hashMap/hashSet
	- 因為下次要查，如果你變了，hash就不同了 XD
- 底層會用 equals 來確認是否item if in bucket
	- 因此如果你要 override 來寫 custom 的 hashCode, equals 也要一起改


Why?
**原因：**

1. **Java 規範:** Java 的 Object 類別文件明確指出：
    > 如果兩個物件根據 equals(Object) 方法是相等的，則在兩個物件上呼叫 hashCode 方法必須產生相同的整數結果。
    
2. **基於雜湊的集合:** HashMap 和 HashSet 等集合使用雜湊碼來快速定位物件。
    - 當你呼叫 contains(Object o) 或 remove(Object o) 時，這些方法會先使用 o.hashCode() 找到可能的桶位置。
    - 然後，它們會使用 equals() 方法比較桶中的每個元素，直到找到相符的物件。
        
3. **不一致的行為:** 如果 hashCode() 和 equals() 不一致，基於雜湊的集合將無法正常運作。
    - 例如，如果兩個物件在邏輯上相等 (根據 equals() 方法)，但它們的雜湊碼不同，HashMap 可能會將它們儲存在不同的桶中，導致 contains() 或 remove() 無法找到正確的物件。


例子
在這個例子中，我們同時覆寫了 equals() 和 hashCode()，並使用 name 和 age 欄位來判斷相等性和計算雜湊碼
```java


class Person {
    String name;
    int age;

    // ...

    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (!(obj instanceof Person)) return false;
        Person other = (Person) obj;
        return Objects.equals(name, other.name) && age == other.age;
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, age); 
    }
}


```




![[IMG-cs61b Hashing-20241003105301305.png]]






# Good HashCodes (Extra)
this website provide 很多不同 hash fn 的討論
[Hash Tables](https://algs4.cs.princeton.edu/34hash/)




寫一個好的 hashCode can be tricky
![[IMG-cs61b Hashing-20241003105301409.png|837]]



薯泥例子，就是讓他很 random...
this topic is deep, we just give it a 想像 at this course
![[IMG-cs61b Hashing-20241003105303031.png]]



## hash function in practice


## Positive integers
- 最常見的就是 _modular hashing_: 把 int 除以質數(M) 拿 remainder  (k % M, in Java) -> 就會落在  0 and M-1  之間
- M 是 bucket size


## Floating-point numbers

- If the keys are real numbers between 0 and 1, we might just multiply by M and round off to the nearest integer to get an index between 0 and M-1. 
- Although it is intuitive, this approach is defective because it gives more weight to the most significant bits of the keys; 
- the least significant bits play no role. 
- One way to address this situation is to use modular hashing on the binary representation of the key (this is what Java does).
- 把 float 變成 binary, 然後 hash it, 一樣用 _modular hashing_



## Strings
- Modular hashing works for long keys such as strings, too: we simply treat them as huge integers. For example, the code below computes a modular hash function for a String s, where R is a small prime integer (Java uses 31).
- 把 string 先轉成 int, 再 _modular hashing_



怎麼理解下面這個 loop?

- 每一次 loop 都把 hash 乘上 base 然後加上 int(char coverted)
- hash 從 0 開始


因此第一次 loop 只有 int 本身 構成的 hash
下一次 loop 這個 hash 就會 乘上 base 再加上下一個 int, 成為新的 hash
下一次 loop 這個新的 hash 就會 乘上 base 再加上下一個 int, 成為下一個新的 hash
...

就是每一次 loop 都會加上一個新的 int 然後乘上一個 base
最後就是一個不小的 int

![[IMG-cs61b Hashing-20241003105303151.png]]


why use 31 as base?
- It's prime, so that when the user mods out by another number, they have no common factors (unless it's a multiple of 31). 
- 31 is also a Mersenne prime (like 127 or 8191) which is a prime number that is one less than a power of 2. This means that the mod can be done with one shift and one subtract if the machine's multiply instruction is slow.
- ref: [Hash Tables](https://algs4.cs.princeton.edu/34hash/)


why 不用 126, 可以不會有衝突阿?
![[IMG-cs61b Hashing-20241003105305178.png|747]]



126 會 overflow
然後 overflow 後，一些多餘的變化全部都是 0
結果就是...下面的那幾個的 hash全部都一樣
![[IMG-cs61b Hashing-20241003105305271.png|780]]




- 盡量用 質數 
	- 減少上面的問題
	- 也可以減少 hash code 跟 bucket 數量的問題
![[IMG-cs61b Hashing-20241003105306708.png|835]]




## 複合欄位, phoneNum(area, exch, ext)

- _User-defined hashCode()_. 
	- Client code expects that hashCode() disperses the keys uniformly among the possible 32-bit result values. 
	- That is, for any object x, you can write x.hashCode() and, in principle, expect to get any one of the 2^32 possible 32-bit values with equal likelihood. 
	- Java provides hashCode() implementations that aspire to this functionality for many common types (including String, Integer, Double, Date, and URL), but for your own type, you have to try to do it on your own. 


	- Program [PhoneNumber.java](https://algs4.cs.princeton.edu/34hash/PhoneNumber.java.html) illustrates one way to proceed: make integers from the instance variables and use modular hashing.


![[IMG-cs61b Hashing-20241003105306794.png|506]]
	

## 複合欄位,transaction

- Program [Transaction.java](https://algs4.cs.princeton.edu/34hash/Transaction.java.html) illustrates an even simpler approach: use the `hashCode()` method for the instance variables to convert each to a 32-bit int value and then do the arithmetic.
![[IMG-cs61b Hashing-20241003105308274.png|511]]





## List
![[IMG-cs61b Hashing-20241003105308383.png|787]]


## binary tree 
![[IMG-cs61b Hashing-20241003105309660.png|791]]



## 另一個例子
摘要 from  java data structure book p140

If keys are not simple integers (strings, for example), a workable strategy is to first mash them into integers and then apply the remaindering method above. Here is a representative string-hashing function that does well empirically, taken from a C compiler by P. J. Weinberger. 

It assumes 8-bit characters and 32-bit ints.
 h << 4 左移 4 bit -> means  mutiple by 2$^4$
 
h << 4 的目的是為了將目前 hash 值 h 向左位移 4 位。這樣做的效果相當於將 h 乘以 2 的 4 次方，也就是 16。

這個操作的主要目的是為了在計算 hash 值時，將字串中每個字元的影響放大。透過將目前的 hash 值乘以一個固定的數字 (這裡是 16)，可以讓每個字元在最終的 hash 值中佔據更大的權重，進而減少 hash 碰撞的機率。


 下一行是類似上面提到的 floodMod, 用 bit operation 處理 negative number


具體來說，這個 hash function 的運作方式如下：
1. 初始化一個 hash 值 h 為 0。
2. 迭代遍歷輸入字串 S 中的每個字元。
3. 對於每個字元，將目前的 hash 值 h 向左位移 4 位 (相當於乘以 16)，然後加上該字元的 ASCII 碼。
4. 將新的 hash 值 h 與 0xf0000000 進行位元 AND 運算，然後再向右位移 24 位。這個操作的目的是為了將 hash 值限制在一個固定的範圍內 (32 位元整數)。
5. 將新的 hash 值 h 與 0xffffffff 進行位元 AND 運算。這個操作的目的是為了確保 hash 值是一個無符號的 32 位元整數。
6. 返回最終的 hash 值 h。
![[IMG-cs61b Hashing-20241003105309750.png|816]]





# Summary
![[IMG-cs61b Hashing-20241003105310969.png]]



# 另一個方法， open addressing

![[IMG-cs61b Hashing-20241003105311071.png]]


### Open Addressing vs Chaining

**Open Addressing** 是將所有元素存儲在哈希表本身內部的一種方法。當發生碰撞時，會在哈希表中尋找下一個可用的槽位來存儲元素。

 優點

1. [**空間效率高**：不需要額外的數據結構來存儲碰撞的元素，所有元素都存儲在同一個表中](https://www.geeksforgeeks.org/open-addressing-collision-handling-technique-in-hashing/)[1](https://www.geeksforgeeks.org/open-addressing-collision-handling-technique-in-hashing/)。
2. [**良好的緩存性能**：由於所有數據都存儲在連續的內存位置，這有助於提高緩存命中率](https://eng.libretexts.org/Courses/Delta_College/C_-_Data_Structures/11%3A_Hashing/11.04%3A_Hashing-_Separate_Chaining)[2](https://eng.libretexts.org/Courses/Delta_College/C_-_Data_Structures/11%3A_Hashing/11.04%3A_Hashing-_Separate_Chaining)。

缺點

1. [**處理刪除操作較複雜**：刪除元素時需要標記槽位為“已刪除”，否則會影響後續的查找操作](https://www.geeksforgeeks.org/open-addressing-collision-handling-technique-in-hashing/)[1](https://www.geeksforgeeks.org/open-addressing-collision-handling-technique-in-hashing/)。
2. [**當負載因子高時性能下降**：當哈希表接近滿時，查找和插入操作的性能會顯著下降](https://www.geeksforgeeks.org/open-addressing-collision-handling-technique-in-hashing/)[1](https://www.geeksforgeeks.org/open-addressing-collision-handling-technique-in-hashing/)。


**Chaining** 是在每個哈希表槽位中存儲一個鏈表（或其他動態數據結構），當發生碰撞時，將元素添加到該槽位的鏈表中。

 優點

1. [**簡單處理碰撞**：每個槽位都有一個鏈表，可以輕鬆處理多個元素映射到同一槽位的情況](https://www.geeksforgeeks.org/open-addressing-collision-handling-technique-in-hashing/)[3](https://www.codingdrills.com/tutorial/hashing-data-structure/chaining-vs-open-addressing)。
2. [**負載因子影響較小**：即使哈希表的負載因子很高，查找和插入操作的性能也不會顯著下降](https://www.geeksforgeeks.org/open-addressing-collision-handling-technique-in-hashing/)[3](https://www.codingdrills.com/tutorial/hashing-data-structure/chaining-vs-open-addressing)。

缺點

1. [**空間效率較低**：需要額外的內存來存儲鏈表](https://www.geeksforgeeks.org/open-addressing-collision-handling-technique-in-hashing/)[2](https://eng.libretexts.org/Courses/Delta_College/C_-_Data_Structures/11%3A_Hashing/11.04%3A_Hashing-_Separate_Chaining)。
2. [**緩存性能較差**：由於鏈表中的元素可能分散在內存的不同位置，這會降低緩存命中率](https://eng.libretexts.org/Courses/Delta_College/C_-_Data_Structures/11%3A_Hashing/11.04%3A_Hashing-_Separate_Chaining)[2](https://eng.libretexts.org/Courses/Delta_College/C_-_Data_Structures/11%3A_Hashing/11.04%3A_Hashing-_Separate_Chaining)。

 推薦

一般來說，**Chaining** 更適合於需要處理高負載因子的情況，因為它能夠更有效地處理碰撞，而不會顯著影響性能。除非說內存使用吃緊，且哈希表的負載因子也低，那這個時候，可以選擇 **Open Addressing** 。




## 比較 on Java data structure book, p138

author suggest 優先選擇 channing method
- open addressing 的一個缺點
	- 在 channing mehod 是不同 bin, 但 open addressing 會容易出現更多衝突
- 另一個缺點是，當你 find key..
	- channing:
		- hash to get bin -> 找完 all keys in that bin -> 知道沒有
	- open addressing
		- need to find all bins, 要找的數量大的多



The performance of open-addressing and  separate chaining methods is dependent on the ratio α = N/M, but we interpret it differently. 
- For separate chaining: α is the average number of items per list and is generally larger than 1. 
- For open addressing, α is the percentage of table positions that are occupied; it _must_ be less than 1. We refer to α as the _load factor_ of the hash table.
ref: [Hash Tables](https://algs4.cs.princeton.edu/34hash/)








## algo book 的 實作


- liner probe 的實作: [LinearProbingHashST.java](https://algs4.cs.princeton.edu/34hash/LinearProbingHashST.java.html)
- [SeparateChainingHashST.java](https://algs4.cs.princeton.edu/34hash/SeparateChainingHashST.java.html) 
	- implements a symbol table with a separate-chaining hash table. 
	- It maintains an array of [SequentialSearchST](https://algs4.cs.princeton.edu/34hash/SequentialSearchST.java.html) objects 
	- The SequentialSearchST class represents an (unordered) symbol table of generic key-value pairs.
	- this class  implements get() and put() by computing a hash function to choose which SequentialSearchST can contain the key and then using get() and put() from SequentialSearchST to complete either job. 
- [SeparateChainingLiteHashST.java](https://algs4.cs.princeton.edu/34hash/SeparateChainingLiteHashST.java.html) 
	- similar but does it using an explict Node nested class.




# 課外學習
## 常見 JavaScript 雜湊函數實作
以下是一個常見的 JavaScript 雜湊函數實作，稱為 **DJB2 雜湊函數** (由 Daniel J. Bernstein 設計)，以及它的解釋：

```javascript
function djb2Hash(str) {
  let hash = 5381; // 初始雜湊值

  for (let i = 0; i < str.length; i++) {
    hash = (hash << 5) + hash + str.charCodeAt(i); // 左移 5 位，加上自身，再加上當前字元的 ASCII 碼
  }

  return hash & 0x7FFFFFFF; // 將雜湊值限制在 31 位元內，確保為正數
}
```

**解釋：**

1. **初始化：**
   - `hash = 5381;`：函數首先將 `hash` 變數初始化為一個質數 `5381`。這個初始值可以是其他質數，但 `5381` 被認為在實踐中效果良好。

2. **迭代字符串：**
   - `for (let i = 0; i < str.length; i++) { ... }`：函數使用一個迴圈迭代輸入字符串 `str` 中的每個字符。

3. **計算雜湊值：**
   - `hash = (hash << 5) + hash + str.charCodeAt(i);`：這是雜湊函數的核心部分。它執行以下操作：
     - `hash << 5`：將當前的 `hash` 值左移 5 位（相當於乘以 32），目的是將每個字元的影響放大，進而減少 hash 碰撞的機率。
     - `+ hash`：將左移後的結果加上原始的 `hash` 值。
     - `+ str.charCodeAt(i)`：將當前字符的 ASCII 碼加到結果中。
   - 這些操作的目的是將每個字符的信息混合到雜湊值中，並產生一個看似隨機的分布。

4. **限制雜湊值範圍：**
   - `return hash & 0x7FFFFFFF;`：最後，函數使用按位與運算符 (`&`) 將雜湊值限制在 31 位元內，並確保結果為正數。

**使用方法：**

```javascript
const str1 = "Hello";
const str2 = "World";

const hash1 = djb2Hash(str1);
const hash2 = djb2Hash(str2);

console.log(hash1); // 輸出：1794130035
console.log(hash2); // 輸出：2085656035
```

**優點：**

- **簡單易懂：**  DJB2 雜湊函數的實作非常簡單，易於理解和實作。
- **速度較快：**  它在計算上相對較快，適合處理字符串。

**缺點：**

- **不適合加密：**  DJB2 雜湊函數不是加密安全的雜湊函數，不應在需要安全性的情況下使用。
- **可能產生衝突：**  像所有雜湊函數一樣，DJB2 雜湊函數也可能產生衝突，即不同的輸入字符串產生相同的雜湊值。

**總之，DJB2 雜湊函數是一個簡單、快速且常用的 JavaScript 雜湊函數實作，適用於非加密場景，例如雜湊表或數據結構中的鍵值映射。**




# PPT discuss 

## 什麼是 valid 的 hashCode
## 什麼是 好的 的 hashCode

![[IMG-cs61b Hashing-20241003105312972.png|687]]

![[IMG-cs61b Hashing-20241003105313072.png|709]]



![[IMG-cs61b Hashing-20241003105315150.png|808]]

# what is valid hash fn?


如果你的兩個對象，呼叫 equals(), 如果返回 true
那這兩個對象 經過 hash fn 處理，需要是一樣的 hash code

(this is also the reason, why you change the overried the hashCode in javam ensure you overrite the equals impl in the same way)

下面三題可以幫你確認認知是否正確
![[IMG-cs61b Hashing-20241003105315273.png|683]]




---




[Challenge Lab 8: Heaps and Hashes | CS 61B Spring 2019](https://sp19.datastructur.es/materials/clab/clab8/clab8#your-task)




# Rabin–Karp algorithm (string search algo)



## calculateHash


```java

public static int calculateHash(String str, int start, int end) {  
    int hash = 0;  
    for (int i = start; i < end; i++) {  
        hash = (hash * UNIQUE_CHARS + str.charAt(i)) % PRIME_BASE;  
    }  
    return hash;  
}
```

`calculateHash` 方法的作用是計算一個字符串（或其子串）的哈希值。這個方法在 Rabin-Karp 算法中扮演著關鍵角色。讓我們逐步解析它的工作原理：

1. 方法簽名：
    `public static int calculateHash(String str, int start, int end)`
    
    這個方法接受三個參數：要計算哈希值的字符串 `str`，以及計算範圍的起始索引 `start` 和結束索引 `end`。
2. 初始化：
    
    `int hash = 0;`
    
    我們從 0 開始，逐步構建哈希值。
3. 哈希計算循環：
    
    `for (int i = start; i < end; i++) {     hash = (hash * UNIQUE_CHARS + str.charAt(i)) % PRIME_BASE; }`
    
    這是方法的核心部分。它遍歷指定範圍內的每個字符，並按以下步驟更新哈希值： a. 將當前哈希值乘以 `UNIQUE_CHARS`（256，表示 ASCII 字符集的大小）。 b. 加上當前字符的 ASCII 值。 c. 對結果取模（除以 `PRIME_BASE` 並取餘數）。 這個計算過程實際上是在構建一個多項式哈希。每個字符都被視為一個 256 進制數字系統中的一位。
4. 防止溢出： 使用 `% PRIME_BASE` 操作可以防止在計算過程中發生整數溢出，同時保持哈希值在一個可控的範圍內。
5. 返回結果： 循環結束後，返回計算得到的最終哈希值。

這種哈希計算方法的優點在於：

- 它能夠生成一個唯一的（或幾乎唯一的）數字來代表一個字符串。
- 它允許我們進行高效的 "滾動哈希" 計算，這是 Rabin-Karp 算法的核心。
- 通過選擇適當的 `PRIME_BASE`，我們可以最小化哈希衝突的可能性。

在 Rabin-Karp 算法中，這個哈希函數的效率至關重要，因為我們需要頻繁地計算和比較哈希值。




## 「滾動哈希」（Rolling Hash）


```java
textHash = (UNIQUE_CHARS * (textHash - text.charAt(i) * highestPower) + text.charAt(i + m)) % PRIME_BASE;

```

這行程式碼是 Rabin-Karp 演算法中非常重要的一部分，它實現了所謂的「滾動哈希」（Rolling Hash）技術。

讓我們逐步分析這行程式碼：
`textHash = (UNIQUE_CHARS * (textHash - text.charAt(i) * highestPower) + text.charAt(i + m)) % PRIME_BASE;`

1. 整體目的： 這行程式碼的目的是高效地計算下一個子字串的哈希值，而不需要重新計算整個子字串。
2. 組成部分：
    - `textHash`：當前視窗（子字串）的哈希值
    - `text.charAt(i)`：當前視窗的第一個字符
    - `highestPower`：`UNIQUE_CHARS` 的 m-1 次方（其中 m 是模式字串的長度）
    - `text.charAt(i + m)`：新視窗中的最後一個字符
3. 步驟解析： a. 移除舊的首字符的影響： `textHash - text.charAt(i) * highestPower` 這步驟從當前哈希值中減去第一個字符的貢獻。`highestPower` 代表這個字符在哈希值中的權重。 b. 為新的哈希值騰出空間： `UNIQUE_CHARS * (...)` 將步驟 a 的結果乘以 `UNIQUE_CHARS`，相當於在多項式表示中左移一位。 c. 加入新的尾字符： `... + text.charAt(i + m)` 將新視窗的最後一個字符加到哈希值中。 d. 取模運算： `% PRIME_BASE` 確保最終的哈希值在合理範圍內，並減少衝突的可能性。
4. 數學原理： 假設我們有一個字串 "abcd"，要滑動到 "bcde"：
    
    - 原哈希值：h ("abcd") = a * 256³ + b * 256² + c * 256¹ + d * 256⁰
    - 新哈希值：h ("bcde") = b * 256³ + c * 256² + d * 256¹ + e * 256⁰
    
    我們可以從 h ("abcd") 得到 h ("bcde")：
    1. 減去 a * 256³
    2. 將剩餘部分乘以 256
    3. 加上 e
5. 效率： 這種方法允許我們在 O (1) 時間內更新哈希值，而不是 O (m) 時間重新計算整個子字串的哈希值。

這種滾動哈希技術是 Rabin-Karp 演算法效率的關鍵。它允許我們快速比較大量的子字串，而不需要逐個字符進行比較。


[RabinKarp.java](https://algs4.cs.princeton.edu/53substring/RabinKarp.java.html)

