[[CS61B Data Structures and Algorithms|cs61b_index]]

## pq 的目的
to easily get max/min 或是某個 best 值
pq 的 interface
![[IMG-cs61b pq and heap-20241003104933903.png|780]]



想要每天蒐集大家的心情，需要 sort, return 前幾高 
![[IMG-cs61b pq and heap-20241003104948837.png|764]]


天真作法：
每天都撈出全部的資料
根據某個條件 sort
最後返回 top M
![[IMG-cs61b pq and heap-20241003105004606.png|818]]


使用 PQ 的作法
```java

public List<String> unharmoniousTexts(Sniffer sniffer, int M) {
  
  // new 一個 string 比較器
  Comparator<String> comparator = new HarmoniousnessComparator();
  
  // new priority queue
  MinPQ<String> pq = new HeapMinPQ<Transaction>(comparator);

  // for every day
  for (Timer timer = new Timer(); timer.hours() < 24; ) {
  
    // get msg and add to pq
    pq.add(sniffer.getNextMessage());

    // we only need to check the first M messages
    // so if size > 0, we can remove the smallest
    if (pq.size() > M) {
      pq.removeSmallest();
    }
  }

  // new a arrayList to store the result
  ArrayList<String> textList = new ArrayList<String>();
  
  // push all the messages into the list
  while (pq.size() > 0) {
    textList.add(pq.removeSmallest());
  }

  // and return the list
  return textList;
}

```


好處? 
PQ internal 已經 sorted, 排好
使用的人，不需要去進行比較了
![[IMG-cs61b pq and heap-20241003105023992.png]]


## how to impl PQ?

orderArr -> worst case is O(N)
BST -> althought LogN but awkward to store duplicate value
hashTable -> O(N) to find the min/max
![[IMG-cs61b pq and heap-20241003105049930.png]]


# Heaps


binary min-heap


## heap 的 invariants
- ordered, 每一個 node <= its children for miniHeap
- if not a complete tree, leaves 一定得靠左
![[IMG-cs61b pq and heap-20241003105119205.png]]



![[IMG-cs61b pq and heap-20241003105132211.png]]

![[IMG-cs61b pq and heap-20241003105140799.png]]



![[IMG-cs61b pq and heap-20241003105148634.png]]


![[IMG-cs61b pq and heap-20241003105154791.png]]

see this
[cs61b sp16 lec24 heap demo - Google 簡報](https://docs.google.com/presentation/d/1VEd2Pm_3OuvkC1M8T5XAhsBTQFxVHs386L79hktkDRg/pub?start=false&loop=false&delayms=3000)


## min heap 操作
- get smallest
	- return the root
- add
	- 把 element insert 在最後一個位置
	- swim: 透過比大小 and swap node 來把element升到適合的位置
- removeSmallest()
	- remove root
	- sink: 然後把最後的那個 node 放在 root, 然後透過比大小 and swap node 來 把element 降到到適合的位置
![[IMG-cs61b pq and heap-20241003105200178.png]]



# Tree Representations - a jounery to find how to impl heap 

most common way
寫死 field 結構
left
right
...
![[IMG-cs61b pq and heap-20241003105208397.png]]


用一個 arr or mem place to put children
可以動態調整大小
![[IMG-cs61b pq and heap-20241003105212838.png]]


use LL to link
![[IMG-cs61b pq and heap-20241003105216373.png]]


類似 union find 的作法
keys in a arr
parents in another arr
![[IMG-cs61b pq and heap-20241003105220912.png]]


因為 parent arr 的順序是固定 pattern, won't change, no need it
![[IMG-cs61b pq and heap-20241003105224403.png]]



那這個方法要如何 "swim" (就是 insert at bottom 後，升到適合的位置)

如果 parent's key > curr's key
那表示 需要 swap, 因為大的應該要往下，小的要往上
一直遞迴call, 直到這個條件無法成立

![[IMG-cs61b pq and heap-20241003105228470.png]]


## swim

另一種 swim 寫法
```java

private void swim(int k){

    // while k 還大於 1(非root), 且 parent(k/2) 還比自己(k)大
	while (k > 1 && greater(k/2, k)){
        
        // swap
		exch(k, k/2);
	    
	    // k 變成 parent
		k = k/2;
	    
	    // 持續下去 (if condition is true in while loop)
	}
}

// private int parent(int k){
//	return k/2
// }


// if idx i 的 ele 比 idx j 的 ele 大，會 return true
private boolean greater(int i, int j) {
	return keys[pq[i]].compareTo(keys[pq[j]]) > 0;
}

```
ref: [IndexMinPQ.java](https://algs4.cs.princeton.edu/24pq/IndexMinPQ.java.html) 


## sink

另一個重要的 heap operation sink
用在 remove biggest 後，把 last node swap root 後，從 root 位置 sink to its correct position

```java


private void sink(int k) {

    // 只要 child idx 還沒超過 N (total number of node), 繼續往下走    
	while (2*k <= n) {
        
        // 往後走，j as new_child_idx
		int j = 2*k;
	    
	    // 繼續往後走, 這是處理 parent to child 有兩種算法 cur*2 and cur*2+1     
		if (j < n && greater(j, j+1)) j++;

        // k 的值 比 child 大了，不用往下 sink 了， break loop
		if (!greater(k, j)) break;


        // swap current idx k to new_child_idx
		exch(k, j);

        // k become new new_child_idx 
		k = j;
	}
}

```



![[IMG-cs61b pq and heap-20241003105232055.png]]



![[IMG-cs61b pq and heap-20241003105235688.png]]


目前選擇的方案
offset by 1 -> math is easier
![[IMG-cs61b pq and heap-20241003105238410.png]]


runtime比較

heap 拿 min 是 O of 1
另外 add 或是 removeSamllest 都只有樹高的複雜度
![[IMG-cs61b pq and heap-20241003105241026.png]]


PQ 的比大小, 可以用比較器自定義
![[IMG-cs61b pq and heap-20241003105243660.png]]


algo book on [Priority Queues](https://algs4.cs.princeton.edu/24pq/)







# Data Structures Summary


nearly everything is search problem
![[IMG-cs61b pq and heap-20241003105246047.png]]



search probelm data strcutrue we learned so far!!
their API
![[IMG-cs61b pq and heap-20241003105248155.png]]


ADT and their possible impl
red means perf is not that good
![[IMG-cs61b pq and heap-20241003105253215.png]]
![[IMG-cs61b pq and heap-20241003105255525.png]]



一層一層的抽象層
e.g.

hash table use hash fn to find a bucket
a bucket can have many different kind of impl
![[IMG-cs61b pq and heap-20241003105257747.png]]



特別功能的 data structure
stack
queue
dequeu
等等
都有不同的功能
![[IMG-cs61b pq and heap-20241003105259933.png]]


![[IMG-cs61b pq and heap-20241003105301874.png]]





# discuss 小節


parent 利用 2.xx for int 會變成 2 的 java int 資料的特性
![[IMG-cs61b pq and heap-20241003105301997.png|838]]

![[IMG-cs61b pq and heap-20241003105303623.png|854]]



--
recall:
- pq:
	- runtime:
	- O(1) to get the smallest/biggest
	- O(logN) for insert and remove
		- insert at bottom and swim up
		- remove at top, use the last node to replace it and sink down
	- use array representation to save space
		- a trick to begin from array idx 1, leave empty at idx 0
			- then you can use 2k(k is current node idx) and 2k+1 to get left/right child, and  k/2 to get parent



projet2A
[Project 2AB: Extrinsic PQ and KDTree | CS 61B Spring 2019](https://sp19.datastructur.es/materials/proj/proj2ab/proj2ab)

