

# why need sort
- 快速找 duplicate
- 讓你做binary search
- 好轉換到一些資料結構 like BST, kd tree 等
![[IMG-cs61b Basic Sorts-20241003104933879.png]]



# sort 定義
- 一定是其中一個 a < b, a = b, a> b
- 可以傳遞 a < b and b < c => a < c
以上兩點又叫 total order

sort 的定義: non-decreasing order, 因為要考慮 <= 
![[IMG-cs61b Basic Sorts-20241003104948810.png]]



根據上面定義的一個例子
![[IMG-cs61b Basic Sorts-20241003105003265.png]]


上面的長度比較， in Java world
![[IMG-cs61b Basic Sorts-20241003105021941.png]]


# inversion

你可以兩兩來比較，找出下面的序列，有哪些 pair out of order, 下例就是 6 inversion out of 55 沒有排

另一個角度看 sort
"消除 inversion"
![[IMG-cs61b Basic Sorts-20241003105048020.png]]



離題談一下 per def

除了time複雜度，也有空間複雜度
另外空間複雜度，通常不用包括 input 所佔用的 空間
![[IMG-cs61b Basic Sorts-20241003105114682.png]]


# Selection Sort
[cs61b selection sort demo - Google 簡報](https://docs.google.com/presentation/d/1p6g3r9BpwTARjUylA0V0yspP2temzHNJEJjCG41I4r0/edit)
![[IMG-cs61b Basic Sorts-20241003105114804.png]]
`/Users/re4388/project/personal/CS61B/cs61b_re4388/selectionSort_and_test/SelectionSort.java`


在 unsorted 裡面找最小
![[IMG-cs61b Basic Sorts-20241003105129656.png]]

放到 sorted 裡面的尾巴，目前只有一個
![[IMG-cs61b Basic Sorts-20241003105138340.png]]


在 unsorted 裡面找最小
![[IMG-cs61b Basic Sorts-20241003105146625.png]]


放到 sorted 裡面的尾巴
![[IMG-cs61b Basic Sorts-20241003105146743.png]]


在 unsorted 裡面找最小
![[IMG-cs61b Basic Sorts-20241003105153374.png]]


放到 sorted 裡面的尾巴
![[IMG-cs61b Basic Sorts-20241003105159095.png]]


在 unsorted 裡面找最小
![[IMG-cs61b Basic Sorts-20241003105207140.png]]

放到 sorted 裡面的尾巴
![[IMG-cs61b Basic Sorts-20241003105211188.png]]


在 unsorted 裡面找最小
![[IMG-cs61b Basic Sorts-20241003105211276.png]]

放到 sorted 裡面的尾巴
![[IMG-cs61b Basic Sorts-20241003105215218.png]]

在 unsorted 裡面找最小
![[IMG-cs61b Basic Sorts-20241003105218513.png]]

放到 sorted 裡面的尾巴
![[IMG-cs61b Basic Sorts-20241003105222968.png]]
以此類推....

# heap Sort
## not  in place
heap sort: [cs61b naive heap sort demo - Google 簡報](https://docs.google.com/presentation/d/1HVteFyWOxBW4mmUgkDnpUoTkWexiHt7Ei30Qolbc_I4/edit#slide=id.g463de7561_042)
![[IMG-cs61b Basic Sorts-20241003105223046.png]]


第一個階段，把東西都塞到 heap, 把 max heap 建立出來
![[IMG-cs61b Basic Sorts-20241003105226796.png]]

deleteMax
![[IMG-cs61b Basic Sorts-20241003105226912.png]]

放到 new output arr 的尾巴
![[IMG-cs61b Basic Sorts-20241003105230304.png]]


deleteMax
![[IMG-cs61b Basic Sorts-20241003105234166.png]]


放到 new output arr 的尾巴
![[IMG-cs61b Basic Sorts-20241003105237086.png]]


deleteMax
![[IMG-cs61b Basic Sorts-20241003105239565.png]]

放到 new output arr 的尾巴
![[IMG-cs61b Basic Sorts-20241003105242228.png]]

deleteMax
![[IMG-cs61b Basic Sorts-20241003105244708.png]]

放到 new output arr 的尾巴
![[IMG-cs61b Basic Sorts-20241003105247096.png]]
以此類推...


![[IMG-cs61b Basic Sorts-20241003105249302.png]]，一些 mem 吃緊的情況下，就很重要了


## in-place

![[IMG-cs61b Basic Sorts-20241003105254362.png]]


[cs61b in-place heap sort demo 2019 - Google 簡報](https://docs.google.com/presentation/d/1SzcQC48OB9agStD0dFRgccU-tyjD6m3esrSC-GLxmNc/edit)
- Bottom-up heapify input array
	- sink(k),  level order 反過來的順序去 sink node
	- sink 後，我們確保 at position k 以下都是一個 heap

上面做完，整個 array 已經變成一個 max heap

- 持續以下操作 N 次
	- remove max item from max heap(O(1)), 然後 swap this item with last time
	- swap 過去的就是 sorted, 就不要動了

上面做完，整個 array will be sorted, 從小到大



![[IMG-cs61b Basic Sorts-20241003105256659.png]]


![[IMG-cs61b Basic Sorts-20241003105258776.png]]

why in-place heap sort is a good choice
say you have a array take 10 gb of data and you want to sort it, do you like to allocate another 10 gb to sort it OR sort it in place?



![[IMG-cs61b Basic Sorts-20241003105300774.png]]


# merge sort


/Users/re4388/project/personal/CS61B/Berkeley-CS61B-Student/lab11/MergeSort.java

[cs61b merge sort demo - Google 簡報](https://docs.google.com/presentation/d/1h-gS13kKWSKd_5gt2FPXLYigFY4jf5rBkNFl3qZzRRw/edit)
![[IMG-cs61b Basic Sorts-20241003105302606.png]]


Top-Down merge sorting N items: 
- Split items into 2 roughly even pieces.
	- 會一直切到剩下一個，然後 merge (next step)
- Mergesort each half.
	- merge 的過程： `i``, j` 指向要被 merge 的 two halves, 然後另一個 p 指向 另一個 arr, ` a1[i]`,` a2[j]` 比大小，小的放入 `arr[p]`, 然後 `i++` and `p++`
- Merge the two sorted halves to form the final result.
    
![[IMG-cs61b Basic Sorts-20241003105304701.png]]

# Insertion Sort
[cs61b naive insertion sort demo - not in place - Google 簡報](https://docs.google.com/presentation/d/181Lhn8jf4N-VG1BOkV4-Caj1wKcavkls8fnTKJlCuXc/pub?start=false&loop=false&delayms=3000&slide=id.g463de7561_042)
## not in-place

建立一個 new output arr to place result
從原本的 arr 的最前面拿出來
![[IMG-cs61b Basic Sorts-20241003105306211.png|839]]


只有 32, 就直接放進去
![[IMG-cs61b Basic Sorts-20241003105307752.png]]


從 ori arr 拿出第二個，15
比 15 小，放在新arr的32前面
![[IMG-cs61b Basic Sorts-20241003105309136.png]]


從 ori arr 拿出第3個
最小，放新arr第一個
![[IMG-cs61b Basic Sorts-20241003105309211.png]]


從 ori arr 拿出第4個
17, 放 15 and 32 中間
![[IMG-cs61b Basic Sorts-20241003105310450.png]]

從 ori arr 拿出第5個
19, 放 17 and 32 中間
![[IMG-cs61b Basic Sorts-20241003105312489.png]]

... 以此類推
原本的 arr 要 go thru 一次 -> N
新的 arr 要全部判斷才可以知道要放在哪裡, so the worse case is also need to go thru it -> N
-> N$^2$




in place insertion sort, use ptr

![[IMG-cs61b Basic Sorts-20241003105312585.png]]


[cs61b in-place insertion sort demo - Google 簡報](https://docs.google.com/presentation/d/10b9aRqpGJu8pUk8OpfqUIEEm8ou-zmmC7b_BE5wgNg0/edit)

## in-place

i, j 指向第一個
![[IMG-cs61b Basic Sorts-20241003105314522.png]]

一開始只有一個，直接就是 sorted
全部會區別為 sorted and unsorted
![[IMG-cs61b Basic Sorts-20241003105316693.png]]

i, j 一起到 unsorted 的第二個
![[IMG-cs61b Basic Sorts-20241003105317495.png]]

j 會把這個 15 insert 到 sorted 那邊適合的位置
![[IMG-cs61b Basic Sorts-20241003105318600.png]]

因此 insert at 第一個 pos via swap
![[IMG-cs61b Basic Sorts-20241003105318695.png]]

下一動，i, j 移到 unsorted的第一個
![[IMG-cs61b Basic Sorts-20241003105319704.png]]

j 一樣把 2 往前 swap 
![[IMG-cs61b Basic Sorts-20241003105320818.png]]

swap/insert 到第一個對的位置
![[IMG-cs61b Basic Sorts-20241003105320939.png]]

就 sorted 了
![[IMG-cs61b Basic Sorts-20241003105322185.png]]

i,j 一樣移到 unsort的第一個 pos
![[IMG-cs61b Basic Sorts-20241003105326488.png]]

j 把 17 swap..
![[IMG-cs61b Basic Sorts-20241003105328136.png]]

到sorted 裡面對的位置
via 比大小
![[IMG-cs61b Basic Sorts-20241003105329632.png]]

again, i, j move to unsorted first pos
![[IMG-cs61b Basic Sorts-20241003105329727.png]]

j 往前swap 19 to its correct pos
(what is correct pos? 可以比大小，他要swap itself until 前面比自己小或等於)
![[IMG-cs61b Basic Sorts-20241003105330639.png]]


then it sorted
![[IMG-cs61b Basic Sorts-20241003105330730.png]]

一樣 i, j move to unsorted first pos
![[IMG-cs61b Basic Sorts-20241003105331274.png]]


以此類推...後面不贅述，可以去看 demo link




每一次 i, j 都會跑到 unexamined 的 head, j 會把需要調整的(vid 裡面叫做 traverler)，帶動到 左邊sorted 裡面的正確位置

Q: 如果我們已經知道了最好的 sort algo, why we learn so many sort algo?
A: 每個 sort 機制都不同，時間複雜度只是一個理論參考，實作上還是都可以試試看。每個都有他們適用的情境, 舉例一，如果你的 array, 幾乎 sort, insersion sort 可能會是最好解，merge and quick sort 反而會慢。 (Java 和很多語言的內建在 N is low or low inversion 會切換為 insertion sort)




![[IMG-cs61b Basic Sorts-20241003105331620.png]]


![[IMG-cs61b Basic Sorts-20241003105331740.png]]



N, 至少都要跑過一次
N$^2$ -> 每個都要shift N 個
![[IMG-cs61b Basic Sorts-20241003105332297.png]]


![[IMG-cs61b Basic Sorts-20241003105332496.png]]

ans => insertion sort



insertion sort 的property:
runtime is propotional to the number of inversions
![[IMG-cs61b Basic Sorts-20241003105333138.png]]


![[IMG-cs61b Basic Sorts-20241003105333587.png]]


![[IMG-cs61b Basic Sorts-20241003105334272.png]]


![[IMG-cs61b Basic Sorts-20241003105335027.png]]

![[IMG-cs61b Basic Sorts-20241003105335160.png]]



# Shell’s Sort (Extra)
(Not on Exam)

![[IMG-cs61b Basic Sorts-20241003105335604.png]]


![[IMG-cs61b Basic Sorts-20241003105335706.png]]

![[IMG-cs61b Basic Sorts-20241003105336102.png]]


![[IMG-cs61b Basic Sorts-20241003105336244.png]]