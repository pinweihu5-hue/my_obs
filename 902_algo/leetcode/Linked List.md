related: [[cs61B linkedlist note]]

## 基本概念練習

題目
```js fold
// Definition for singly-linked list.
class ListNode {
  val
  next
  constructor(val, next) {
    this.val = val
    this.next = next
  }
}

function printList(head) {
}

// 這裡要 return head, 不要走到後面沒有 ret head
function addNodeAtEnd(head, val) {
}

function insertAfterAt(node, val) {
}

let first = new ListNode(1, null)
let second = new ListNode(2, null)
let third = new ListNode(3, null)
first.next = second
second.next = third

console.log('printList')
printList(first)
// ans: 1,2,3

console.log('============')

console.log('printList after addNodeAtEnd')
let newHead = addNodeAtEnd(first, 33)
printList(newHead)
// ans: 1,2,3, 33
console.log('============')
console.log('printList after insertAfterAt')
insertAfterAt(first.next, 8)
printList(first)
// ans: 1,2, 8, 3, 33
```

答案
```js fold
// Definition for singly-linked list.
class ListNode {
  val
  next
  constructor(val, next) {
    this.val = val
    this.next = next
  }
}

function printList(head) {
  let cur = head
  while (cur !== null) {
    console.log(cur.val)
    cur = cur.next
  }
}

function addNodeAtEnd(head, val) {
  let cur = head

  // 走到 cur.next undefined, 表示走到 cur 為最後一個 node
  while (cur.next !== null) {
    cur = cur.next
  }

  cur.next = new ListNode(val, null)
  return head
}

function insertAfterAt(node, val) {
  const newNode = new ListNode(val, null) // init a new Linked List Node

  newNode.next = node.next
  node.next = newNode // 這一步如果先走，原本 node.next 的 ptr 會被洗掉, 就需要另存了
}

let first = new ListNode(1, null)
let second = new ListNode(2, null)
let third = new ListNode(3, null)
first.next = second
second.next = third

console.log('printList')
printList(first)
// ans: 1,2,3

console.log('============')

console.log('printList after addNodeAtEnd')
let newHead = addNodeAtEnd(first, 33)
printList(newHead)
// ans: 1,2,3, 33
console.log('============')
console.log('printList after insertAfterAt')
insertAfterAt(first.next, 8)
printList(first)
// ans: 1,2, 8, 3, 33

```

### [206. Reverse Linked List](https://leetcode.com/problems/reverse-linked-list/)
```ts fold


var reverseList = function(head) {
	
	let prev = null
	let curr = head
	
	
	
		// 示意圖 for last step
	  //
    //                head 
    // before           1 -x-> 2 -x-> 3
    // after    null <- 1 
    //          prev   curr
    //
	
    while (curr){
        // use tmp since we overwrite it via `curr.next = prev`
				oldCurNext = curr.next
				curr.next = prev // this is the key, reverse the pointer!
		
        // shift curr to the next node
				prev = curr
				curr = oldCurNext
	}

    
    // 示意圖 for curr.next = prev
	  //
    //                head 
    // before           1 -x-> 2 -x-> 3 -x-> null
    // after    null <- 1 <--- 2 <--- 3 <--- null 
    //                               prev   curr
    //
	
	
    // after finish, prev is the new head
	return prev
		
};
```

### [21. Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/)
```ts fold
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     val: number
 *     next: ListNode | null
 *     constructor(val?: number, next?: ListNode | null) {
 *         this.val = (val===undefined ? 0 : val)
 *         this.next = (next===undefined ? null : next)
 *     }
 * }
 */
 function mergeTwoLists(l1: ListNode | null, l2: ListNode | null): ListNode | null {
    let newList = new ListNode(999999, null)

    // 拉出來一個 ptr, 會需要往後走
    let curNew = newList

    // 我們不會再需要 l1 and l2 的 head, 因此這邊就不 save l1 and l2 的 head ptr 了
    // let l1Cur  = l1
    // let l2Cur  = l2

    // 只要兩個 "同時" 不是 null...
    while(l1 && l2){
        if(l1.val <= l2.val){
            curNew.next = new ListNode(l1.val)
            l1 = l1.next
        } else if(l1.val > l2.val){
            // use l2
            curNew.next = new ListNode(l2.val)
            l2 = l2.next
        }
        curNew = curNew.next
    }

    // 處理剩下的, 這時候，會出現l1 or l2 會是 null
    // 沒有的就是剩下的, 繼續接在 curNew 後面
    if(l1){
        curNew.next = l1
    } else {
        curNew.next = l2
    }

    return newList.next
};

```



### [234. Palindrome Linked List](https://leetcode.com/problems/palindrome-linked-list/)
```js fold


var isPalindrome = function (head) {
    // handle single node LL
    if(head.next === null) return true

    // 1. cut half
    let d = new ListNode(200, head)
    let s = d
    let f = head
    while(f&& f.next){
        s=s.next
        f=f.next.next
    }

    let secondHalf = s.next // get the second halg


    // 清掉前半段的後面部分
    // 這邊不清掉也沒差，因為你下面的比較，你只會比較前面一樣你就給過了
    // s.next = null           


    // 2. reverse second half
    let prev = null
    let cur = secondHalf
    while(cur){
        let curNext = cur.next
        cur.next = prev
        prev = cur
        cur = curNext
    }

    let l1 = d.next
    let l2 = prev

    // console.log(`l1`, l1)
    // console.log(`l2`, l2)


    // 3: compare one by one
    while(l1 && l2){
        if(l1.val !== l2.val) return false
        l1=l1.next
        l2=l2.next
    }

    // 如果是 odd length, 這邊會多一個，但是多那一個沒差
    // 只要那一個以外的其他 node 的 val 一樣，就是 pali
    // 因此不需要下面這行的檢查
    // if(l1 && !l2) return false
    // if(l2 && !l1) return false

    return true

};
```



---


### [203. Remove Linked List Elements](https://leetcode.com/problems/remove-linked-list-elements/)

```ts fold
function removeElements(head: ListNode | null, val: number): ListNode | null {
    let d = new ListNode(333, head)
    let s = d
    let f = d.next

    while (f) {
        if (f.val === val) {
            s.next = s.next.next
            f = s.next // 跟下面作法不同，但也可以, 繼續把 f 放在 s 的下一個
        } else {
            s = s.next
            f = f.next
        }
    }

    return d.next
};





// use s and f
var removeElements = function(head, val) {
    let d = new ListNode(0, head) // 為了不影響，最好確認 dummy的val 不在題目會出現的範圍內，這題範圍是 1 <= Node.val <= 50
    let s = d
    let f = head
    
    while (f){
        if(f.val === val){
            s.next = s.next.next
            f=f.next
        } else {
            s=s.next
            f=f.next
        }
    }
    
    return d.next
    

};

var removeElements = function(head, val) {
    let d = new ListNode(0, head)  // 如果不用dummy, input [7,7,7,7] 會變成 [7]，沒有 check到第一個
    let cur = d
    while(cur){
        while(cur.next && cur.next.val === val) {
            cur.next = cur.next.next
        }
        cur = cur.next
    }
    return d.next
    
};
```

### [83. Remove Duplicates from Sorted List](https://leetcode.com/problems/remove-duplicates-from-sorted-list/)
```js fold

// 2 ptr with dummy

var deleteDuplicates = function(head) {
    // let d = new ListNode(0, head) <-- 錯誤點，dummy node設定為0 然後因為 node.val 也會是 0
    // 因此會變成這個 dummy node的 0 也會被砍掉，會變成如上截圖的結果！！
		
    // 結論：dummy 不可以設定為 題目限制 （-100 <= Node.val <= 100）的範圍
    let d = new ListNode(-1000, head)  
    let s = d
    let f = head
    while (f){
        if (s.val ===f.val){
            s.next = s.next.next
            f=f.next
        } else{
            s=s.next
            f=f.next 
        }
    }
    // return head
    return d.next
    
};






var deleteDuplicates = function(head) {
//     這題如果用 s, f 兩個ptr ，像是這種[0,0,0] ，會變 [] 會都remove掉
//     但是我們其實還要留下 cur, 因此用 cur, 不需要用 s f 兩個 ptr （要另外想怎麼弄）
//     這樣邏輯比較單純
    let cur = head
    while (cur){
        while(cur.next && cur.val === cur.next.val){
            cur.next = cur.next.next
        }
        cur = cur.next
    }
    
    return head
    
};

// 也可以用 dummy, 但是 dummy 不可以設定為 題目限制 （-100 <= Node.val <= 100）的範圍
// 不然會被因為判斷到被砍掉
var deleteDuplicates = function(head) {
    let d = new ListNode(-1000, head)
    let cur = d
    // let cur = head
    while (cur){
        while(cur.next && cur.val === cur.next.val){
            cur.next = cur.next.next
        }
        cur = cur.next
    }
    
    // return head
    return d.next
    
};
```


### [143. Reorder List](https://leetcode.com/problems/reorder-list/)

```ts fold


function reorderList(head: ListNode | null): void {
    let d = new ListNode(2000, head)

	// let s = d  // <-- s 放在 d, 一前一後走，奇數的 example, 會漏掉一個沒有 merge 到，需要另外寫一段 code 處理
    let s = head  // <-- 一起從 head 走，會讓 cut half 的 first half 比較大，下面 merge 邏輯會好處理很多
    let f = head

    /////////////  1. 切半
    while (f && f.next) {
        s = s.next
        f = f.next.next
    }
    let secondHalf = s.next
    s.next = null

    /////////////  2. reverse secondHalf
    let prev = null
    let cur = secondHalf
    while (cur) {
        let curNxt = cur.next
        cur.next = prev
        prev = cur
        cur = curNxt
    }


    /////////  3. link two alternatively
    let l1 = d.next
    let l2 = prev

    while (l1 && l2) {
        let l1Next = l1.next
        let l2Next = l2.next
        l1.next = l2
        l2.next = l1Next
        l1=l1Next
        l2=l2Next
    }
};

```


### [86. Partition List](https://leetcode.com/problems/partition-list/)
以 用 x的值去把這個LL 分成兩個list, 然後join them

這題問的比較奇怪喔，不是說比x大的放左，比x小的放右
這題要的結果是：
x小的放右 (實作的作法要小心不會改變相對位置)
x大的不要管他(如果再左邊，不用管，如果at右邊, 也不要改變它)
```ts fold

// 這個作法比較直接，直接建立兩個 list, 再接起來
function partition(head: ListNode | null, x: number): ListNode | null {

    let d1 = new ListNode(999) // 999, 看題目限制。不要撞到題目會有的 val 即可
    let d1Cur = d1
    let d2 = new ListNode(999)
    let d2Cur = d2

    let cur = head
    while(cur){
        if(cur.val < x){
           d1Cur.next = new ListNode(cur.val)
           d1Cur = d1Cur.next
        } else if (cur.val >= x){
            d2Cur.next = new ListNode(cur.val)
            d2Cur = d2Cur.next
        } else {
            console.log("shall not happen")
        }
        cur=cur.next
    }

    d1Cur.next = d2.next
    
    return d1.next
};
```


這個作法是另一種，我覺得沒有上面簡潔。這個作法是 移除小於 x 的，然後再接起來
```js fold

var partition = function (head, x) {
    let d = new ListNode(999, head)
    let d2 = new ListNode(999, null)
    let new1 = d2

    let s = d
    let f = head
    while (f) {
        if (f.val < x) {

			// create new LL 
            new1.next = new ListNode(f.val, null)
            new1 = new1.next

			// remove
            s.next = s.next.next  
		    // only f need to go next
            f = f.next
        } else {
            s = s.next
            f = f.next
        }
    }
    new1.next = d.next
    return d2.next
};

```



---



### [61. Rotate List](https://leetcode.com/problems/rotate-list/)
```ts fold
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     val: number
 *     next: ListNode | null
 *     constructor(val?: number, next?: ListNode | null) {
 *         this.val = (val===undefined ? 0 : val)
 *         this.next = (next===undefined ? null : next)
 *     }
 * }
 */

function rotateRight(head: ListNode | null, k: number): ListNode | null {
    // edge case
    if(head === null) return null


    // 找出 real K
    let LL_len = 0
    let cur = head
    while(cur){
        LL_len++
        cur=cur.next
    }
    // console.log(LL_len)

    let real_k = k % LL_len
    if(real_k===0) {
        return head
    }


    // s, f 抓出後半段

    let d = new ListNode(999, head)
    let s = d
    let f = d

    // f 先走 k step
    let step = real_k
    while(step>0){
        f=f.next
        step--
    }

    while(f.next){
        s=s.next
        f=f.next
    }

    let sec = s.next
    // console.log('sec', sec)

    s.next = null // 這樣前半段的後面才會是 null
    // console.log('d.next', d.next)



    // 要走到 sec 的 尾巴, 才可以接到 d.next 的前面
    let secCur = sec
    while(secCur.next){
        secCur=secCur.next
    }

    secCur.next = d.next // 切到前面

    return sec // 返回 sec head
};


```

### [24. Swap Nodes in Pairs](https://leetcode.com/problems/swap-nodes-in-pairs/)



解法：
![[IMG-Linked List-20250810200102700.png]]
- step1
    1. 先畫出本來的樣子
    2. 再畫出goal
    3. 然後比對後，改一下 指向
- step2
    1. 只要有改指向的地方，都會是一行code
    2. 有三個地方要handle
- step3
    - 如果handle 的地方涉及到需要 overwrite, 就會需要先saved old ptr
    - 然後要 shift
        - shift 也可能會涉及要 saved ptr
- 錯誤點!!
    - 一定要由後面往前改變，不然就壞掉了
    - `prev = cur` 這個 shift, cur 是已經移動過的 cur(ex1 中的 1 上)，這樣也才會合理。

```ts fold

/**
 * Definition for singly-linked list.
 * class ListNode {
 *     val: number
 *     next: ListNode | null
 *     constructor(val?: number, next?: ListNode | null) {
 *         this.val = (val===undefined ? 0 : val)
 *         this.next = (next===undefined ? null : next)
 *     }
 * }
 */

function swapPairs(head: ListNode | null): ListNode | null {
    let d = new ListNode(999, head)
    let prev = d
    let cur = head

    while(cur && cur.next){
        let oldCurNextNext = cur.next.next
        let oldCurNext = cur.next


        // 這邊一定要依照順序, 從最後往前 modify
        cur.next.next = cur
        cur.next = oldCurNextNext
        prev.next = oldCurNext

        // 沒有依照順序 -> fail
        // cur.next = oldCurNextNext // 這個也會連帶影響 cur.next.next
        // cur.next.next = cur
        // prev.next = oldCurNext
        

        // shift
        prev = cur
        cur = oldCurNextNext
    }

    return d.next
    
};



```




錯誤記錄
**ㄟㄟ...你怎麼用 d, 不是用會一直移動的指針 prev**
下面 d->prev 就對了 XD

**LL 題目的一個關鍵，就是用 moving ptr 去移動，另外fixed ptr 可隨時存取晚點要用的資料**
```js fold

var swapPairs = function(head) {

    let d = new ListNode(200, head)
    let prev = d
    let cur = prev.next

    while(cur && cur.next){
        let curNN =cur.next.next
        let curN = cur.next

        cur.next.next = cur
        cur.next = curNN
        d.next = curN  // ㄟㄟ...你怎麼用 d, 不是用會一直移動的指針 prev
        
        d = cur  // ㄟㄟ...你怎麼用 d, 不是用會一直移動的指針 prev
        cur = curNN
    }

    return d.next
};

```


## fast and slow

### [Remove Nth Node From End of List](https://leetcode.com/problems/remove-nth-node-from-end-of-list/)
```ts fold

/**
 * Definition for singly-linked list.
 * class ListNode {
 *     val: number
 *     next: ListNode | null
 *     constructor(val?: number, next?: ListNode | null) {
 *         this.val = (val===undefined ? 0 : val)
 *         this.next = (next===undefined ? null : next)
 *     }
 * }
 */

function removeNthFromEnd(head: ListNode | null, n: number): ListNode | null {
    /**
    
    自己用 s, f 走走看
    讓 f 先走n個
    然後s, f  再一起走
    until f.next is null (aka: while f.next)

    這時候 s 的下一個就是要砍的
    
     */
    let d = new ListNode(999, head)
    let s = d
    let f = d
    
    let step = n
    while(step > 0){
        f=f.next
        step--
    }


    while(f.next){
        s=s.next
        f=f.next
    }
    s.next = s.next.next
    return d.next
};
```

### [141. Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/)
```ts fold
function hasCycle(head: ListNode | null): boolean {
    let s = head
    let f = head
    while(f&&f.next){
        s=s.next
        f=f.next.next
        if(f==s) return true // 要放走的後面，不然一開始一樣就撞到了
    }

    return false
    
};
```


### [142 Linked List Cycle II](https://leetcode.com/problems/linked-list-cycle-ii/)
```ts fold


function detectCycle(head: ListNode | null): ListNode | null {
  // 初始化快慢指针，都指向头节点
  let s = head;
  let f = head;

  // 快慢指针相遇的节点
  let m = null;

  // 1. 判断是否存在环
  while (f && f.next) {
    s = s.next; // 慢指针走一步
    f = f.next.next; // 快指针走两步

    // 如果相遇，说明有环
    if (s === f) {
      m = s;
      break;
    }
  }

  // 如果没有相遇，说明不存在环，返回 null
  if (m === null) return null;

  // 2. 找到环的入口节点
  s = head; // 慢指针回到头节点
  while (s !== m) {
    s = s.next; // 慢指针走一步
    m = m.next; // 相遇节点走一步
  }

  // 当慢指针和相遇节点相遇时，就是环的入口节点
  return s;
}



```
TIL:
- 這一題如果用dummy node, s and f 從不同的位置出發，雖然 f 可以追上 s, 但是 遇到 到那個 node 不同，這會影響後續第二輪去找的節點的答案。
	- 因此，這一題不要用 dummy node, s and f 應該從 head 一起出發。



錯誤記錄1

我的 m 沒有 init as null
你這樣下面就不會撞到  `if (m === null) return null`
不然就是你下面要改為   `if (m === null || undefined) return null`

但是題目有設定 Node 的 type 是 `head: ListNode | null`
因此還是乖乖 init 的時候 m = null 才是合理的作法
```ts

    let s = head
    let f = head
    let m   -> 應該要 let m = null

```



錯誤記錄2
上面寫法跟下面寫法的差異是
上面寫法會忽略掉，如果剛好當下 s && m 就已經一樣了，你會 skip 掉第一次的檢查，就往下 .next 了
這樣答案就會錯
```ts


// 這一題的錯法寫法
// 就算 s and m 一開始進入 while 時，都已經一樣，還是會先 .next 
while (s && m) {
	s = s.next
	m = m.next
	if (s === m) {
		break
	}
}


// 正確寫法：
// 如果一樣 跳出 while, 如果一開始不同，就不會進入 while
while (s !== m) {
	s = s.next; 
	m = m.next;
}


```




錯誤記錄3
s and f 一前一後開始
這題的算法要一起開始
```js

let s = head
let f = head.next


```



錯誤記錄4
```js


var detectCycle = function (head) {
    if (head === null) return false
    let s = head
    // let m
    let cycleDete = 0
    while (f && f.next) {

        // 這一塊要寫在"移動後"，因為要走完後撞到才是 cycle，"移動前"撞到不是 cycle
        // why?? 因為你上面已經 s = head, f = head, 你這個放前面，馬上就撞到了XD
        if (f === s) {
            cycleDete = 1
            // m = s
            break
        }

        s = s.next
        f = f.next.next
    }


```


---


## 其他題型

### [160. Intersection of Two Linked Lists](https://leetcode.com/problems/intersection-of-two-linked-lists/)

自己寫的解法, 邏輯比較單純
```ts fold

function getIntersectionNode(headA: ListNode | null, headB: ListNode | null): ListNode | null {
    let l1 = headA
    let l2 = headB
    
    let isl1TurnAround = false
    let isl2TurnAround = false

    while (l1 && l2) {
        // 檢查是否遇到
        if (l1 === l2) return l1

        // 各自往下走
        l1 = l1.next
        l2 = l2.next

        // 如果走到底，就接上另一個
        // 用一個 flag 控制只能 turnAround 一次
        if (l1 === null && !isl1TurnAround) {
            l1 = headB
            isl1TurnAround = true

        }
        if (l2 === null&& !isl2TurnAround) {
            l2 = headA
            isl2TurnAround = true
        }

    }
};


```


這題要考的是：
- 你要怎麼從一個接上另一個
- 需要用到分清楚ptr
- 一個是 running ptr(l1, l2, 自己建立的), 一個是本來的沒有動的(headA, headB)

卡住記錄：
我忘記用兩個 isTurnAround flag 來控制 l1 , l2



另一種解法
```ts fold
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     val: number
 *     next: ListNode | null
 *     constructor(val?: number, next?: ListNode | null) {
 *         this.val = (val===undefined ? 0 : val)
 *         this.next = (next===undefined ? null : next)
 *     }
 * }
 */

function getIntersectionNode(headA: ListNode | null, headB: ListNode | null): ListNode | null {
    /**
    
    headA and B 分別開始走
    如果走到底，就直接連到另一個的頭開始繼續走
    如果重複撞到，那就是答案
    如果沒有撞到，那就是 null
     */

    let curA = headA
    let curB = headB

    // 這邊一定會是 ||, 因為你裡面的邏輯就已經會發生，如果沒有 curA 你要 link to headB
    // 然後當 curA and curB 都是 null, 就會跳出while loop
    while (curA || curB) {

        // 如果一開始就 intersect的case
        if (curA == curB) {
            return curA
        }


        if (curA) {
            curA = curA.next
        } else {
            curA = headB
        }

        if (curB) {
            curB = curB.next
        } else {
            curB = headA
        }

    }

    return null

};

```




---


### [2. Add Two Numbers](https://leetcode.com/problems/add-two-numbers/)
```ts fold
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     val: number
 *     next: ListNode | null
 *     constructor(val?: number, next?: ListNode | null) {
 *         this.val = (val===undefined ? 0 : val)
 *         this.next = (next===undefined ? null : next)
 *     }
 * }
 */

function addTwoNumbers(l1: ListNode | null, l2: ListNode | null): ListNode | null {
    let d = new ListNode(-1)
    let newCur = d


    let carry = 0
    while(l1 || l2){
        let rawNum = 0 
        
        if(l1===null && l2!==null){
            rawNum = l2.val
            l2 = l2.next
        } else if(l1!==null && l2===null){
            rawNum = l1.val
            l1 = l1.next
        } else {
            rawNum=l1.val+l2.val
            l1=l1.next
            l2=l2.next
        }

        let digit = carry + rawNum

        newCur.next = new ListNode(digit % 10)
        newCur = newCur.next

        carry = Math.floor(digit/10)
    }

    if(carry===1){
        newCur.next = new ListNode(1)
    }

    return d.next
};
```