# Backstory, Partitioning

each sort has its core idea
![[IMG-cs61b quick sort-20241003104933906.png]]

quick sort 的起源
tony 想要翻譯俄文到英文

一個想法是，英文 list 找到 cat, 俄文 list 找 Kowka, 假設 d 個字
用 binary search, 分別需要 logD
N 的 sentense 需要 N log D

![[IMG-cs61b quick sort-20241003104948839.png]]

那個時代，資料記錄在 tape 裡面, 二元搜尋不好用，要真的去移動 tape, 不是 log time
最後他選擇先 sort sentense 然後再去 tape scan 一次 -> N log N + D
那他要如何 sort?
![[IMG-cs61b quick sort-20241003105004754.png]]

先談如何 partition
![[IMG-cs61b quick sort-20241003105024156.png]]

![[IMG-cs61b quick sort-20241003105024306.png]]

給定 pviot 點，一個 arr 有比他大的，有比他小的，如何分兩邊?
![[IMG-cs61b quick sort-20241003105050083.png]]

scan 一次，如果比較小，移到左端點，大的 skip
![[IMG-cs61b quick sort-20241003105050206.png]]

塞入 bst, 會分兩邊
![[IMG-cs61b quick sort-20241003105119424.png]]

scan 3 次
第一次拿小的，第二次拿白的，最後拿藍的
依序放入 new arr
![[IMG-cs61b quick sort-20241003105119696.png]]

# Quick sort / Parition sort

/Users/re4388/project/personal/CS61B/Berkeley-CS61B-Student/lab11/QuickSort.java
/Users/re4388/project/personal/CS61B/Berkeley-CS61B-Student/clab11/BnBSolver.java

how to use partition in sort?

after partition:

1. 5 已經排好了
2. 5 兩邊的 parition, 基本上就是獨自的問題，不會需要交互

![[IMG-cs61b quick sort-20241003105132442.png]]

demo [cs61b quick sort demo - Google 簡報](https://docs.google.com/presentation/d/1QjAs-zx1i0_XWlLqsKtexb-iueao9jNLkN-gW9QxAD0/edit#slide=id.g463de7561_042) -> 建議還是看一下，來 convince yourslef how recursion work
這邊 demo 都選最左邊的 item as pivot
![[IMG-cs61b quick sort-20241003105140975.png]]

實證上來說， quick sort 最快的
不過有 case 會 N$^2$
![[IMG-cs61b quick sort-20241003105148748.png]]

# Quick sort Runtime

![[IMG-cs61b quick sort-20241003105154898.png]]

![[IMG-cs61b quick sort-20241003105200377.png]]

![[IMG-cs61b quick sort-20241003105208508.png]]

快排理論上要證明比 merge sort 快，比較難理解，也很很多假設..
實證上是比較快的， N^2 的機率很低很低，且也有辦法避免
![[IMG-cs61b quick sort-20241003105212955.png]]

假設 parition 後，都是 一邊 10%, 一邊 90% -> 可以證明是 nlogn, 這個證明可以一般化到 0.1%
![[IMG-cs61b quick sort-20241003105216505.png]]


BST insert 和 快排 呼叫 compareTo 的次數是一樣的
因此如果 BST 是 complete -> logn, 如果 BST 是 splindly tree -> 退化 LL -> n
對比到快排，就是 nlogn 和 n^2
![[IMG-cs61b quick sort-20241003105221053.png]]



for n = 1000
實證上幾乎不會有 1000,1000 次 (n^2)比較的例子
大多數都是 11000~ 13000中間
標準差也很低, 約 0.64
![[IMG-cs61b quick sort-20241003105224523.png]]


如果你的 arr 是 sorted or close to sorted, 快排就有可能 N^2
![[IMG-cs61b quick sort-20241003105228601.png]]


# sorting summary (so far)
- heap sort-nlogn
	- 用 heap 找最大來排 -> 下面有提到有 caching 問題(?)
	- 缺點
		- 需要維護另一個 heap,  如果資料量大，空間如果是 constraint，就要小心
		- 不是 stable sort, 如果要 stable, 要用 merge sort
		- 如果資料分佈在有限的範圍內，則可以使用計數排序（Counting Sort）或基數排序（Radix Sort）等線性時間複雜度的排序演算法。
- selection sort-n square
	- 找最小放前面， fixed it 
- insertion sort-n square
	- 把目前的item插入在合適的位置(in-place 算法：每一次 i, j 都會跑到 unexamined 的 head, j 會把需要調整的(vid 裡面叫做 traverler)，帶動到 左邊sorted 裡面的正確位置)
	- 如果 array is nearly sorted (low inversion)or N is smaller enough (like < 12) -> 可以達到快 O(N) -> 最快的 sorting
- merge sort-nlogn
	- 遞迴切halves recursive 然後按序合併 recursive   -> 有提過, in-place 反而慢，且實作比較複雜
- quicksort:-nlogn
	- 更好的名字可以叫做parition排，用pivot 來切然後來比pivot小放左，比pivot大放右，recursive doing this-> 實證上"一般情況下"最快的
	- 小心:如果 pivot 選不好，每一次都只會把一個排到一邊，那recur depth 變成 O(N), 需要跑 N 個 -> 會變成 N^2
		- 如果 array 幾乎 sort 好(然後選端點 as pivot)，或是 array 很多重複的 -> 可能會導致上面情況
		- 解法：
			- random pivot pick
			- 前中後挑一個element, 取中位數當 pivot
![[IMG-cs61b quick sort-20241003105232199.png]]


# Avoiding the Quicksort Worst Case
![[IMG-cs61b quick sort-20241003105232361.png]]



![[IMG-cs61b quick sort-20241003105235952.png]]


- 如果輸入資料已經排序或幾乎已排序，而我們選擇了最左或最右元素作為 pivot，則每次分割只會將一個元素劃分到另一側，導致遞迴深度變成 O (n)，每次遞迴都需要 O (n) 的時間比較元素，最終導致 O (n^2) 的時間複雜度。
- 如果輸入資料包含大量重複元素，並且 pivot 選擇不當，Quicksort 的分割步驟可能會變得低效。例如，如果所有元素都相同，每次分割都只會將陣列分成一個空子陣列和一個與原陣列大小相同的子陣列，同樣導致 O (n^2) 的時間複雜度。
- pivot 選擇對 Quicksort 的效能至關重要。如果每次都選擇到最大或最小的元素作為 pivot，就會導致最差情況發生


How to overcome
- 隨機化 pivot 選擇
- 使用三數取中法選擇 pivot： 從陣列中選擇三個元素（例如第一個、中間的和最後一個元素），並使用這三個元素的中位數作為 pivot，可以更穩定地選擇到一個較好的樞紐元。
- 使用其他排序演算法： 對於已知會導致 Quicksort 效能不佳的特定情況，可以考慮使用其他排序演算法，例如合併排序（Merge Sort）或堆排序（Heap Sort），它們在最差情況下也能保持 O (n log n) 的時間複雜度。

![[IMG-cs61b quick sort-20241003105238615.png]]



# What is stable sort?

- 如果輸入資料中有兩個或多個相等的元素，在排序後的結果中，這些相等元素的相對位置可能與它們在原始資料中的相對位置不同的話，這個排序算法就是 not stable，反之，就是 stable.
- 如果你要 sort 的東西如下 [(物件 A, 值 1), (物件 B, 值 2), (物件 C, 值 1)]
- 但如果使用**不穩定**的排序演算法，A 和 C 的相對順序就**有可能**會改變，例如：[(物件 C, 值 1), (物件 A, 值 1), (物件 B, 值 2)]
- 如果你要讓 A, C 保持相對位置不變，那你就要用 stable 類的 sort.

**常見的不穩定排序演算法：**
- 快速排序 (Quicksort)
- 堆排序 (Heapsort)
- 選擇排序 (Selection Sort)

**常見的穩定排序演算法：**
- 合併排序 (Merge Sort)
- 插入排序 (Insertion Sort)
- 氣泡排序 (Bubble Sort)
    
**什麼時候需要在意排序演算法的穩定性？**
- _當需要根據多個鍵值排序時，穩定性就很重要。例如，先按照年齡排序，然後再按照姓名排序，如果第二次排序使用不穩定的演算法，則第一次排序的結果可能會被打亂。_
- 當處理的資料包含指向相同物件的指標或引用時，穩定性也很重要，因為它可以確保排序後這些指標或引用的相對順序保持不變。