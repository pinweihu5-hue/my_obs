related
- [[cs61b pq and heap]]



## why PQ?
拿一個數據結構的最大和最小比較快的結構 `O(logN)`
如過用 array 的話，要先sort and 最快的 sort 也要 `O(NlogN)`
但是如果用 heap or pq, 拿最大或最小 → O(1)

很多應用都會需要用 pq 幫助，like graph 中的 Dijkstra

## 題目

### [215. Kth Largest Element in an Array](https://leetcode.com/problems/kth-largest-element-in-an-array/)
![[IMG-pq-20250810200102722.png]]

```ts fold

function findKthLargest(nums: number[], k: number): number {

    let pq = new MiniPQ((a, b) => a - b)

    // 先全部塞進去
    nums.forEach(ele => pq.enqueue(ele))


    // ensure pq 裡面 只有 k 個ele
    // 由小到大
    // 因此第一個就是地k大
    while (pq.size() > k) {
        pq.dequeue()
    }
    

    return pq.peak()
}


class MinPQ {
    pq;
    n;
    comparator;

    constructor(comparator) {
        this.pq = [];
        this.n = 0; // number of items on priority queue
        let defaultASC = (a, b) => a - b
        this.comparator = comparator || defaultASC;
    }

    isEmpty() {
        return this.n === 0;
    }

    size() {
        return this.n;
    }

    peek() {
        if (this.isEmpty()) throw new Error("Priority queue underflow");
        return this.pq[1]; // 我們把第一個 ele 放在 idx 1, 這樣會比較處理, since parent become k/2
    }

    enqueue(val) {
        // 先 increment x
        this.n++;
        // 再把 val 塞到這個新的空間
        this.pq[this.n] = val;
        this.swim(this.n);
    }

    dequeue() {
        if (this.isEmpty()) throw new Error("Priority queue underflow");
        // 先把第一個（從樹的角度，就是最上面那個node）拿出來, 後面會 ret
        const min = this.pq[1];

        // 把最下面那個放到移除的最上面那個位置 via swap
        this.swap(1, this.n);

        // 拔掉下面的 (上面存起來的，等等可以 ret)
        this.n--;

        this.sink(1); // then sink

        return min;
    }

	parent(k){
		return Math.floor(k/2)  // 錯誤點-> 忘記 Math.floor
	}

    // 往上游
    swim(k) {
        // 当 k == 1 时， 意味着该元素已经是根节点了， 不可能再向上调整， 因此循环条件不需要包含 k == 1 的情况
        // 且 parent 還比 k大，就繼續往上走 (min PQ, 越上面應該越小 )
        while (k > 1 && this.greater(this.parent(k), k)) {
            this.swap(this.parent(k), k); // 透過 swap 往上(前)移動
            k = this.parent(k); // update k to its parent, 然後繼續 while loop
        }
    }

    // 往下沉
    sink(k) {
        // 只要 k * 2 還沒有到最後一個位置就繼續往下走
        // 錯誤記錄：2 * k < this.n -> 少了等於
        // 這邊要用 <= this.n, 因為 2* k 還是需要跟最後一個node去進行比較
        while (2 * k <= this.n) {
            // 下面都用 kid ptr 做事情
            let kid = 2 * k;
            
	        // 我們要讓 kid ptr 走到比較大的那個 kid 
            // 因為 kid 有兩個可能的位置, 如果 kid 的值還比 kid+1 大, 大的應該要放在後面
            // 因此我們要把 kid++
            if (kid < this.n && this.greater(kid, kid + 1)) {
                kid++;
            }

            // 下面判斷是否已經到正確的位置

            // 如果 k 已經比 kid 小 -> 已經到正確的位址 -> break
            if (this.greater(kid, k)) {
                break;
            }
            // 如果沒有到正確的位置 -> swap 往下(後)移動
            this.swap(k, kid);

            // 更新 k as kid -> 繼續下一個 iteration
            k = kid; // 這裡要用 kid, 因為你要拿到最上面的 ref, 不可以用k*2, k 已經改變了 
        }
    }

    // helper
    greater(i, j) {
        return this.comparator(this.pq[i], this.pq[j]) > 0;
    }

    swap(i, j) {
        const swap = this.pq[i];
        this.pq[i] = this.pq[j];
        this.pq[j] = swap;
    }
}


```


TIL
概念如下
use MiniPQ
dequeue 直到剩下 k 的
peak 出來的就是第 k 大的 element
![[IMG-pq-20250810200106967.png|647]]




錯誤記錄


```js fold

bigger(p, q) {
	return this.comparator(this.pq[p], this.pq[q]) // <-- 忘記加上 > 0
}

```


### [703. Kth Largest Element in a Stream](https://leetcode.com/problems/kth-largest-element-in-a-stream/)
題目提供以下 框架 code

ctor
初始用 要 return max 第幾大
和一開始的 nums
add(val)
加入一個 val
同時 return 目前第 k 大的 element

```ts
class KthLargest {
    constructor(k: number, nums: number[]) {
        
    }

    add(val: number): number {
        
    }
}

/**
 * Your KthLargest object will be instantiated and called as such:
 * var obj = new KthLargest(k, nums)
 * var param_1 = obj.add(val)
 */
```


```ts fold

class KthLargest {
    pq
    k
    constructor(k: number, nums: number[]) {
        let comparator = ((a, b) => a - b)
        this.pq = new MinPQ(comparator)
        this.k = k

        for (let ele of nums) {
            this.pq.enqueue(ele)
            if (this.pq.size() > this.k) this.pq.dequeue()
        }
    }

    add(val: number) {
        this.pq.enqueue(val)
        if (this.pq.size() > this.k) this.pq.dequeue()
        return this.pq.peak()

    }
}



class MinPQ {
    pq
    n
    comparator
    constructor(comparator) {
        this.n = 0
        this.pq = []
        this.comparator = comparator || ((a, b) => a - b)
    }

    isEmpty() {
        return this.n === 0
    }
    size() {
        return this.n
    }
    enqueue(val) {
        this.n++
        this.pq[this.n] = val
        this.swim(this.n)
    }
    dequeue() {
        let retVal = this.pq[1]
        this.swap(1, this.n)
        this.n--
        this.sink(1)
        return retVal
    }
    peak() {
        return this.pq[1]
    }
    swim(k) {
        while (k > 1 && this.bigger(this.parent(k), k)) {
            this.swap(this.parent(k), k)
            k = this.parent(k)
        }
    }
    sink(k) {
        while (k * 2 <= this.n) {
            let kid = k * 2
            if (kid < this.n && this.bigger(kid, kid + 1)) {
                kid++
            }
            if (this.bigger(kid, k)) break

            //o.w.
            this.swap(kid, k)
            k = kid
        }
    }
    swap(i, j) {
        let tmp = this.pq[i]
        this.pq[i] = this.pq[j]
        this.pq[j] = tmp
    }
    bigger(i, j) {
        return this.comparator(this.pq[i], this.pq[j]) > 0
    }
    parent(k) {
        return Math.floor(k / 2)
    }
}


```


錯誤記錄：
我傻傻的把資料都塞到pq
然後 dequeue 到第k個拿答案
又把 dequeue 出來的塞回去
最後這種解法是 TLE

這題腦筋要轉一下

**要去思考，要如何讓第 K 大的一直在 PQ 的 head (so we can use `pq.peak()` to O(1) to get it)**

**就是你的 PQ 要 由小到大去排序, say**
 **`[1,2,3,4,5]`**

**只要超過K size 就 dequeue**
因為 pq 是 由小到大
所以 如果 k = 3
1,2 會被 dequeue掉
這時候的 peak 就是 3 
this is the ans!




### [973. K Closest Points to Origin](https://leetcode.com/problems/k-closest-points-to-origin/)
Q: 要拿離(0,0)最近的 K 個點

```ts fold

function kClosest(points: number[][], k: number) {

    // 算 dist
    let pointsDit = points.map(p => {
        return [p, getDist(p, [0, 0])]
    })

    // 根據第一個 ele 排序
    let fn = (a, b) => a[1] - b[1]
    let pq = new MinPQ(fn)

    // 塞 pq
    pointsDit.forEach(ele => pq.enqueue(ele))


    // 前 k 個推進去
    let res = []
    while (k > 0) {
        res.push(pq.dequeue())
        k--
    }


    return res.map(v => v[0]) // 只需要第一個 ele
};



/**
距離是 Euclidean distance 決定
 */
function getDist(p1, p2) {
    let [x1, y1] = p1
    let [x2, y2] = p2
    return Math.sqrt((x1 - x2) ** 2 + (y1 - y2) ** 2)
}



class MinPQ {
    pq
    n
    comparator
    constructor(comparator) {
        this.n = 0
        this.pq = []
        this.comparator = comparator || ((a, b) => a - b)
    }

    isEmpty() {
        return this.n === 0
    }
    size() {
        return this.n
    }
    enqueue(val) {
        this.n++
        this.pq[this.n] = val
        this.swim(this.n)
    }
    dequeue() {
        let retVal = this.pq[1]
        this.swap(1, this.n)
        this.n--
        this.sink(1)
        return retVal
    }
    peak() {
        return this.pq[1]
    }
    swim(k) {
        while (k > 1 && this.bigger(this.parent(k), k)) {
            this.swap(this.parent(k), k)
            k = this.parent(k)
        }
    }
    sink(k) {
        while (k * 2 <= this.n) {
            let kid = k * 2
            if (kid < this.n && this.bigger(kid, kid + 1)) {
                kid++
            }
            if (this.bigger(kid, k)) break

            //o.w.
            this.swap(kid, k)
            k = kid
        }
    }
    swap(i, j) {
        let tmp = this.pq[i]
        this.pq[i] = this.pq[j]
        this.pq[j] = tmp
    }
    bigger(i, j) {
        return this.comparator(this.pq[i], this.pq[j]) > 0
    }
    parent(k) {
        return Math.floor(k / 2)
    }
}


```






















## 其他
其他 pq 題: [https://hackmd.io/@meyr543/SJ_L8J_9c](https://hackmd.io/@meyr543/SJ_L8J_9c)

## leet code 內建 PQ
可以看這篇
https://dev.to/raaynaldo/leetcode-heap-priority-queue-in-javascript-4f25
https://support.leetcode.com/hc/en-us/articles/360011833974-What-are-the-environments-for-the-programming-languages

```md fold
leetcode 提供 MinPriorityQueue and MaxPriorityQueue class
API包括

enqueue
size
dequeue
front  return the highest priority element
back   return the lowest priority element
toArray() 
clear
isEmpty  T/F

也可以自定義比較器
pq class 吃一個 obj
至個 obj 裡面放一個 compare FN
FN 自己定義

  const ageArr = [
	  { name: 'John', age: 33 }, 
	  { name: 'Jane', age: 42 }, 
	  { name: 'Jack', age: 16 }, 
	  { name: 'Jill', age: 35 }, 
	  { name: 'Jessica', age: 33 }
  ]
  
  const ageQueue = new PriorityQueue({
	  compare: (a, b) => {
		  if (a.age < b.age) {
			  return -1;
		  } else if (a.age > b.age) {
			  return 1;
		  } else {
			  return b.name.localeCompare(a.name);
		  }
	  }
  });

  for (const person of ageArr) {
	  ageQueue.enqueue(person);
	  const a2 = ageQueue.toArray()
	  console.log(a2)

  }
```




## not try this round

下面notion 有old note
### [1046. Last Stone Weight](https://leetcode.com/problems/last-stone-weight/)


### [355. Design Twitter](https://leetcode.com/problems/design-twitter/)
### 621. Task Scheduler

Q:  
給定任務 arr ` ["A","A","A","B","B","B"]` 和
做同一個任務需要間隔多少 cpu intervals:  n
求 minimum number of CPU intervals required to complete all tasks.

ex:
Input: tasks = ["A","A","A","B","B","B"], n = 2
Output: 8

Explanation: a possible sequence is: A -> B -> idle -> A -> B -> idle -> A -> B.
做完A, 你要等兩輪
一樣適用於 B
第三輪，兩個都還無法，因此 idle
第四輪，A已經可以，所以是 A


```ts fold

function leastInterval(tasks: string[], n: number): number {
  if (n === 0) return tasks.length; // 冷却时间为 0，可以直接执行所有任务


  // 用 taskCount 追蹤任務
  const taskCount = new Map(); // 统计每个任务出现的次数
  for (const task of tasks) {
    taskCount.set(task, (taskCount.get(task) || 0) + 1);
  }

  // 为了尽可能减少 CPU 的空闲时间，我们需要优先执行剩余次数多的任务。
  // 最大优先队列可以保证每次都取出剩余次数最多的任务，从而最大限度地利用 CPU 时间
  const pq = new MinPQ((a, b) => b[1] - a[1]);
  for (const [task, count] of taskCount.entries()) {
    pq.enqueue([task, count]);
  }

  let time = 0; // 模拟 CPU 时间
  let q = []; // [task, nextAvailableTime]

  // 只要 pq 還要任務 或是 q 還有任務
  while (pq.size() > 0 || q.length > 0) {
  
    time++;  // time每一輪 ++

    // q 還有任務，且時間 >= nextAvailableTime, 從 q shift 出來放入 pq
    if (q.length > 0 && q[0][1] <= time) {
      const [task, nextAvailableTime] = q.shift();
      pq.enqueue([task, taskCount.get(task)]);
    }

    // pq 會優先執行剩余次数多的任务
    if (pq.size() > 0) {
      const [task, count] = pq.dequeue();
      taskCount.set(task, count - 1); // 任务剩余次数减 1

      // 只要有還有下次task需要跑，add to queue
      if (count > 1) {
        // 關鍵：冷卻時間等於 目前時間 + n + 1
        q.push([task, time + n + 1]);
      }
    }
  }

  return time;
}



class MinPQ {
    pq
    n
    comparator
    constructor(comparator) {
        this.n = 0
        this.pq = []
        this.comparator = comparator || ((a, b) => a - b)
    }

    isEmpty() {
        return this.n === 0
    }
    size() {
        return this.n
    }
    enqueue(val) {
        this.n++
        this.pq[this.n] = val
        this.swim(this.n)
    }
    dequeue() {
        let retVal = this.pq[1]
        this.swap(1, this.n)
        this.n--
        this.sink(1)
        return retVal
    }
    peak() {
        return this.pq[1]
    }
    swim(k) {
        while (k > 1 && this.bigger(this.parent(k), k)) {
            this.swap(this.parent(k), k)
            k = this.parent(k)
        }
    }
    sink(k) {
        while (k * 2 <= this.n) {
            let kid = k * 2
            if (kid < this.n && this.bigger(kid, kid + 1)) {
                kid++
            }
            if (this.bigger(kid, k)) break

            //o.w.
            this.swap(kid, k)
            k = kid
        }
    }
    swap(i, j) {
        let tmp = this.pq[i]
        this.pq[i] = this.pq[j]
        this.pq[j] = tmp
    }
    bigger(i, j) {
        return this.comparator(this.pq[i], this.pq[j]) > 0
    }
    parent(k) {
        return Math.floor(k / 2)
    }
}



```