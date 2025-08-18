
### [207. Course Schedule](https://leetcode.com/problems/course-schedule/)

給課程數 `numCourses` 和 課程先修關係的 `prerequisites` 

course 用數字表示
0-based
`[[ai, bi]]`表示 b 是 a 的 preq

返回 true如果可以依照順序把課程都上完

```ts fold

function canFinish(numCourses: number, prerequisites: number[][]): boolean {
    // let adj = new Array(numCourses).fill([]) <--這個寫法不行
    
    let adjList = Array.from({length: numCourses}, ()=>[])
    
    let indegree = Array.from({length: numCourses}, ()=>0)

    for (let [course, pre] of prerequisites) {
        // 因為題目給 [[ai, bi]] 表示 b 是 a 的 preq
        // 但是我們要上課是先上 a 再上 b
        // 所以這邊要反過來建立 adj
        adjList[pre].push(course) 
        indegree[course]++
    }

    // console.log("adjList", adjList)
    // console.log("indegree", indegree)


    let q = []
    let res = []
    // 一開始先把不需要先修的推入 q
    for (let i = 0; i < numCourses; i++) {
        if (indegree[i] === 0)  q.push(i)
    }


    while (q.length !== 0) {
        const v = q.shift()
        res.push(v) // 不需要修，就可以馬上推入 res 了


        // visit v 的 neibor w
        // 減掉 indrgree
        for (let w of adjList[v]) {
            indegree[w]--
            if (indegree[w] === 0) q.push(w) // 到零就可以推入 res
        }
    }

    // 如果所有課程都已完成，則返回 true，否則返回 false
    if (res.length === numCourses) return true
    else return false

}

```


要考什麼:
- 會利用 ingress arr 來記錄每一個 課 的 preq 的數量
	- 就是記錄每一個 vertex 連到 其他vertex 的數量
- 用 BFS, init q 裡面放的是，沒有連到 vertex 的 vertex
	-  `ingress[curN] = 0` 的那些 vertex 就是了
- 然後 traversal graph, 每一個走過的, `ingress[curN]--`,  when `ingress[curN] = 0` -> push into `res`

關鍵字：
- ingress arr
- BFS



---


### [210. Course Schedule II](https://leetcode.com/problems/course-schedule-ii/)
給課程數 `numCourses`
和 課程先修關係的 `prerequisites` 
[0,1 ] 表示要先修1才可以修0
求修課的課程順序arr, 如果沒有辦法完成，返回空arr



```ts fold

function findOrder(numCourses: number, prerequisites: number[][]): number[] {

    // 這邊用 array of array 來 repr adjList
    const adjList: number[][] = [];
    const inDegree: number[] = [];
    for (let i = 0; i < numCourses; i++) {
        adjList.push([]);
        inDegree.push(0);
    }

    const queue: number[] = [];
    const result: number[] = [];

    // 建立鄰接表和入度表
    for (const [course, preReq] of prerequisites) {
        adjList[preReq].push(course); // 這邊是關鍵，把 preq 放在 vertx, 連出去的是 course
        inDegree[course]++;
    }

    // 佇列用於儲存所有入度為 0 的課程，這些課程是當前可以修讀的
    // 將入度為 0 的課程加入佇列
    for (let i = 0; i < numCourses; i++) {
        if (inDegree[i] === 0) {
            queue.push(i);
        }
    }

    // 拓撲排序
    while (queue.length > 0) {
        // 推出來的都是 indegree 為 0, 可以馬上修課，推入 result
        const currentCourse = queue.shift()!;
        result.push(currentCourse);

        // visit 當前 vertex 的 nei
        for (const neighbor of adjList[currentCourse]) {
        
		        // 關鍵是這邊
            // visit 後 indgree -1 
            // 如果為 0, 推入 queue
            inDegree[neighbor]--;
            if (inDegree[neighbor] === 0) {
                queue.push(neighbor);
            }
        }
    }

    // 如果所有課程都被加入結果，則返回結果，否則返回空陣列
    return result.length === numCourses ? result : [];
}




//   same code, just comment
function findOrder(numCourses: number, prerequisites: number[][]): number[] {

    const adjList = []
    const indegree = []
    for (let i = 0; i < numCourses; i++) {
        adjList[i] = []
        indegree[i] = 0
    }

    for (let[course, pre] of prerequisites){
        adjList[pre].push(course)  // 這個adjList 決定了，每一個 preq -> 有多少個couse (in arr) 需要上 
        indegree[course]++ // 決定了這個 couse 有幾個 preq 需要上
    }

    
    let q = []
    for (let i = 0; i < numCourses; i++) {
        if (indegree[i] === 0) q.push(i) // 把沒有需要 preq 的都先推入 q, 從這邊開始去上課
    }

    let res = []
    while (q.length !== 0) {
        let c = q.shift()
        res.push(c)

        for(let w of adjList[c]){
            indegree[w]--
            if(indegree[w]===0) q.push(w)
        }
    }

    if(res.length===numCourses) return res
    else return []
}

```

心得
- 建立 adj list 要反過來 preq → course, 
	- 因為題目給 [[ai, bi]] 表示 b 是 a 的 preq
	- 但是我們要上課(上課就是 adj traversal )是先上 a 再上 b
    - 拿出來
        - 當做上過， `inDegree[neighbor]--`;
        - 當這些課的  indegree 是 0, 就可以推入 q, 下一輪都被推入 res
- 最後就是檢查 res

more 
[Problem List - LeetCode](https://leetcode.com/problem-list/topological-sort/)