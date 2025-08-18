- related
	- [[CS61B Data Structures and Algorithms|cs61b_index]] 中有很多 tree related algo



## tree traversal

### [Binary Tree Preorder](https://leetcode.com/problems/binary-tree-inorder-traversal/)

try 
Inorder / PostOrder / Level order / iterative DFS inorder

pre, post and in
```js fold


var preorderTraversal = function(root) {
    let res = []
    
    function dfs(node){
        if (node === null) return null
        res.push(node.val)
        dfs(node.left)
        dfs(node.right)
        
    }
    
    dfs(root)
    
    return res
    
};


var inorderTraversal = function(root) {
let res = []
    function dfs(node){
        if (node === null) return null
        dfs(node.left)
        res.push(node.val)
        dfs(node.right)
        
    }
    dfs(root)
    return res
    
};



var postorderTraversal = function(root) {
    let res = []
    function dfs(node){
        if (node === null) return null
        dfs(node.left)
        dfs(node.right)
        res.push(node.val)
    }
    dfs(root)
    return res
};



// Iterative w/ stack
var preorderTraversal = function(root) {
    let stack = []
    let res = []
    stack.push(root)
    
    while (stack.length > 0){
        let n = stack.pop()
        if (n !== null){
            res.push(n.val)
            stack.push(n.right)
            stack.push(n.left)
        }
    }
    
    return res
};

// # Iterative w/ stack
var inorderTraversal = function(root) {

    let stack = []
    let res = []
    while ( stack.length > 0 || root !== null){
        while (root !== null){
            stack.push(root)
            root = root.left
        }

        n = stack.pop()
        res.push(n.val)
        root = n.right
    }
    
    return res
    
};
```


level order
```ts fold
var levelOrder = function(root) {
    let res = []
    let q = [root]

    while(q.length > 0){
        let tmp = []
        let q_len = q.length
        for (let i =0; i<q_len; i++){
            let n = q.shift()
            if(n){
                tmp.push(n.val)
                if(n.left) q.push(n.left)
                if(n.right) q.push(n.right)
            }
        }
        if(tmp.length!==0){
            res.push(tmp)
        }
    }
    
    return res
};
```




## preorder
### [226. Invert Binary Tree](https://leetcode.com/problems/invert-binary-tree/)
每一個level下的都left tree 和right tree 要invert

```js fold

function invertTree(root: TreeNode | null): TreeNode | null {
    function dfs(node) {
        if (node) {
            let tmp = node.left
            node.left = node.right
            node.right = tmp
            dfs(node.left)
            dfs(node.right)
        }
    }
    dfs(root)
    return root
};



or

var invertTree = function(root) {
    
    function dfs(node){
        if(node){
            [node.left, node.right] = [node.right, node.left]
            dfs(node.left)
            dfs(node.right)
        }
    }
    
    dfs(root)
    
    return root
    
};


```


## postorder, Get height
### [104. Maximum Depth of Binary Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree/)

```js fold


// 這是錯誤的版本, 卡滿久, 
// 這樣寫，當你遇到 node is null, 你就沒有設定 ret 東西 -> 但是求 depth, 你是需要的
function maxDepth(root: TreeNode | null): number {

    function dfs(node) {
        if (node) {
            let leftH = dfs(node.left)
            let rightH = dfs(node.right)
            return 1 + Math.max(leftH, rightH)
        }

    }

    return dfs(root)

};


// 正確版本
function maxDepth(root: TreeNode | null): number {

    function dfs(node) {
        if (!node) return 0 // <--------- when reach node is null, ret 0
        let leftH = dfs(node.left)
        let rightH = dfs(node.right)
        return 1 + Math.max(leftH, rightH) // <---then 每一層 +1

    }

    return dfs(root)

};
```

### [111. Minimum Depth of Binary Tree](https://leetcode.com/problems/minimum-depth-of-binary-tree/)
```ts fold 
function minDepth(root: TreeNode | null): number {
    if(root===null) return 0 // edge case, 下面會沒有 cover 到
    

    function dfs(node) {
        // 這一題我們一樣在雙null children的上一層就判斷（why?), 有以下四種 case
        // 下面的 1 + 都是表示自己這個 node 算上去的一層 height
        if (node.left && node.right) {
            return 1 + Math.min(dfs(node.left), dfs(node.right))
        }

        if (!node.left && !node.right) {
            return 1
        }

        if (node.left && !node.right) {
            return 1 + dfs(node.left)
        }

        if (!node.left && node.right) {
            return 1 + dfs(node.right)
        }
    }

    return dfs(root)
};

// 求最大比較簡單，直接算Max.max兩邊高度加上當前高度的1就好。
// 求最小需要考慮到，因為如果當一邊沒有時，那一邊就是0 直接用 Math.min(leftH, rightH)        
// 只會拿到0, 因此我們需要另外幫一邊是0的情況下，分開處裡，把拿的到值的那一邊加上1

```



---

### [543. Diameter of Binary Tree](https://leetcode.com/problems/diameter-of-binary-tree/)
diameter是一個tree中某兩個node的最長距離


===
解法


遞迴
上面一直往下問，what is the max diameter for this node
最下面的的兩邊是0，再往上問，下面算過，上面就不用算了。
height可以用 1 + max(l, r)去算

Diameter 可以用 height of L tree 和 height R tree  加上  2 (兩個edge)去算

and we say height of a null tree is -1 since the height of a single node tree is 0
```js fold
var diameterOfBinaryTree = function(root) {
    
    let res = -Infinity
    
    function dfs(node){
        if(!node) return -1
        let leftH = dfs(node.left)
        let rightH = dfs(node.right)
        
        let d = leftH +  rightH + 2
        res = Math.max(res, d)
        
        return 1+ Math.max(leftH, rightH)
    }
    
    dfs(root)
    
    return res
    
};
```


TIL
what is diameter 跟 height 的關係
![[IMG-tree-20250810200102764.png|452]]









### [110. Balanced Binary Tree](https://leetcode.com/problems/balanced-binary-tree/)
BST的定義：左樹和右樹的 hight 相差不能大於一

```js fold
var isBalanced = function(root) {
    
    let res = true    
    
    function dfs(node){
        if(!node) return 1
        
        let leftH = dfs(node.left)
        let rightH = dfs(node.right)
        
        let dif = Math.abs(leftH - rightH)
        if(dif > 1 ) res = false
        
        return 1 + Math.max(leftH, rightH)
        
    }
    
    dfs(root)
    return res
    
};
```

## same tree kind
### [100. Same Tree](https://leetcode.com/problems/same-tree/)

```ts fold


// 更簡潔的寫法
function isSameTree(p: TreeNode | null, q: TreeNode | null): boolean {
    return dfs(p, q)
    
    function dfs(p, q){
        if(!p && !q) return true
        if(!p || !q) return false
        if(p.val !== q.val) return false
        return dfs(p.left, q.left) && dfs(p.right, q.right)
    }
};








function isSameTree(p: TreeNode | null, q: TreeNode | null): boolean {
    if(!p && !q) {
        return true
    } else if(p && !q){
        return false
    } else if(!p && q) {
        return false
    } else if (p && q){

        // 觀念
        // 可能要去思考，當 p and q 都已經 non-null下，還有"哪些" 都要成立才可以return true
        // left 的遞迴結果 && right的遞迴結果 && 這一層的數值一樣
        // 以上三個都要一樣，才可以
        let leftResult= isSameTree(p.left, q.left)
        let rightResult = isSameTree(p.right, q.right)
        let sameValResult = p.val === q.val
        return leftResult && rightResult && sameValResult
    }
}

```

### [572. Subtree of Another Tree](https://leetcode.com/problems/subtree-of-another-tree/)

```ts fold
var isSubtree = function (root, subRoot) {
    let res = false

    function dfs(node) {
        if (!node) return
        if (isSameTree(node, subRoot)) res = true
        dfs(node.left)
        dfs(node.right)
    }

    dfs(root)

    return res
};



function isSameTree(l1, l2) {
    if (!l1 && !l2) {
        return true
    } else if (!l1 || !l2) {
        return false
    } else {
        let valTheSame = l1.val === l2.val
        let leftTreeSame = isSameTree(l1.left, l2.left)
        let rightTreeSame = isSameTree(l1.right, l2.right)
        if (valTheSame && leftTreeSame && rightTreeSame) return true
        else return false
    }
}
};
```


錯誤記錄
ㄟ.....我實作的是 isSameTree
所以我少了一段 code XD


下面是只有  isSameTree 的邏輯
```js fold

var isSubtree = function (root, subRoot) {

    return dfs(root, subRoot)


    function dfs(l1, l2) {

        if (!l1 && !l2) {
            return true
        } else if (!l1 || !l2) {
            return false
        } else {
            let valTheSame = l1.val === l2.val
            let leftTreeSame = dfs(l1.left, l2.left)
            let rightTreeSame = dfs(l1.right, l2.right)
            if (valTheSame && leftTreeSame && rightTreeSame) return true
            else return false
        }

    }

};

```

---

### [Symmetric Tree ](https://leetcode.com/problems/symmetric-tree/description/)  

```ts fold

function isSymmetric(root: TreeNode | null): boolean {
  // handle case empty tree is sym
  if (root === null) return true;

  // 由兩個子樹判斷四種情況
  return isMirror(root.left, root.right);
}

// 判断两个子树是否镜像对称
function isMirror(left, right) {

  // 四種情況
  
  // 1. both null -> true
  if (!left && !right ) return true

  // 2. only one is null -> false
  if (!left  || !right ) return false;

  // 3. val is not the same -> false
  if (left.val !== right.val) return false;

  // 4. val is the same, need to recur to check
  //    -> check 左子树的左子树 是否与 右子树的右子树
  //    -> 左子树的右子树 是否与 右子树的左子树
  //      see below 圖
  return isMirror(left.left, right.right) && isMirror(left.right, right.left);
}
```


![[IMG-tree-20250810200106990.png|555]]




## base case  is not at null level
### [257. Binary Tree Paths](https://leetcode.com/problems/binary-tree-paths/)
```ts fold



var binaryTreePaths = function(root) {

    let res = []
    
    function dfs(node, path){
        // 處理單邊 null, 單邊 null 不需要處理任何東西
        if (!node) return
        
        // handle both child are null, no need arrow string
        if (!node.left && !node.right){
            // 依照題意，雙邊 null 才需要推入，也要 ret
            res.push(path + `${node.val}`)
            return
        }
        
        // rest are both are not null
        
        path += `${node.val}`
        
        
        dfs(node.left, path + "->")
        dfs(node.right, path + "->")
    }
    
    dfs(root, "")
    return res
    
};




// or use JS auto implicit change type
var binaryTreePaths = function(root) {
    let res = []
    function dfs(node, path){
        if(node){
            if(!node.left && !node.right){
                res.push(path + node.val)
            }
            dfs(node.left, path+node.val+"->")
            dfs(node.right, path+node.val+"->")
        }
    }
    dfs(root, "")
    
    return res
    
    
    
};
```

### [112. Path Sum](https://leetcode.com/problems/path-sum/)
```js fold
function hasPathSum(root: TreeNode | null, targetSum: number): boolean {
    let res = false

    function dfs(node, sum){
        if(!node) return
        if(!node.left && !node.right){ // 當兩個 children都是 null
            if(sum-node.val === 0) res = true
        }
        dfs(node.left, sum-node.val)
        dfs(node.right, sum-node.val)
    }
    dfs(root, targetSum)
    return res
};
```



### [Path Sum II](https://leetcode.com/problems/path-sum-ii/description/)
```js fold
var pathSum = function (root, targetSum) {
    let res = []

    dfs(root, 0, [])

    return res

    function dfs(node, sum, path) {
        if (!node) return

        if (!node.left && !node.right) {
            if (node.val + sum === targetSum) {
                res.push(path.concat(node.val))
                return
            } else {
                return
            }
        }
       
        let newPath = path.concat(node.val)
        let newSum = sum + node.val


        dfs(node.left, newSum, newPath)
        dfs(node.right, newSum, newPath)
    }

};



```
TIL
- 你的操作是在 dfs 遞迴 的上面 -> preorder
- 因為是preorder, 因此你那個 node 還沒走
- 因此你對於 sum 的加總，和 path 的 concat 要在 return case 那邊處理最後一個還沒走的node
- 這題也要小心，要用 concat 來 copy 一份再傳進去。


## level order

### [637. Average of Levels in Binary Tree](https://leetcode.com/problems/average-of-levels-in-binary-tree/)
```js fold

var averageOfLevels = function(root) {
    let res = [];
    let q = [];
    q.push(root);
    while (q.length > 0) {
        let q_len = q.length;
        let tmp = []
        // 盲點：如果用這種 for loop in queue question
        // 需要先定義好 q_len, 而不是動態的q.length
        // 因為你下面也會 push into q
        for (let i = 0; i < q_len; i++) {
            let node = q.shift();
            if (node!==null){
                tmp.push(node.val)
                if (node.left!==null) q.push(node.left);
                if (node.right!==null) q.push(node.right);
            }
        }
        let total = tmp.reduce((prev, cur)=> prev+cur, 0)
        res.push(total/tmp.length)
    }
    return res;
    // T.C: O(N), we visit all nodes 
    // S.C: O(N), the bottom level can contain at most N / 2 nodes
};
```

### [199. Binary Tree Right Side View](https://leetcode.com/problems/binary-tree-right-side-view/)
```js fold

var rightSideView = function(root) {
    let q = [root]
    let res = []
    
    while (q.length > 0){
        let tmp = null
        let q_len = q.length
        for (let i=0; i<q_len;i++){
            let n = q.shift()
            if(n){
                tmp = n.val
                if(n.left) q.push(n.left)
                if(n.right) q.push(n.right)
            }
        }
        if (tmp === null) continue
        res.push(tmp)
    }
    
    return res
};

```


### [993. Cousins in Binary Tree](https://leetcode.com/problems/cousins-in-binary-tree/)
找parent不同但同level的

```js fold
var isCousins = function(root, x, y) {
    q = []
    q.push([root, null])
    
    while (q.length > 0){
        let m1 = new Map()
        q_len = q.length
        for (let i = 0; i < q_len; i++){
            let [n, parNVal] = q.shift()
           
            if (n){
                 // 因為題目參數給的是數字，這邊要傳入map都要轉為 value 
                 m1.set(n.val, parNVal)
                // JS 下面要進行 map compare 很麻煩，直接傳值 n.val，下面比父母的值就好
                if (n.left) q.push([n.left, n.val])
                if (n.right) q.push([n.right, n.val])
            }
        }
        
        // 題目參數給的x y 是數值喔
        if (m1.has(x) && m1.has(y) && m1.get(x) !== m1.get(y)) return true
    }
    
    return false
    
};
```


### [Find Bottom Left Tree Value](https://leetcode.com/problems/find-bottom-left-tree-value/description/)
Q: 給 root of binary tree,  求 last row 的最左邊的值


```js fold

// 關鍵:  level order (from right→left) and get last one

function findBottomLeftValue(root: TreeNode | null) {
    let q = [root]
    let res = []
    while (q.length > 0) {
        let tmp = null
        let qLen = q.length
        for (let i = 0; i < qLen; i++) {
            let n = q.shift()
            if (n) {
                res.push(n.val)
                if (n.right) q.push(n.right) // 先推右邊, queue, 先推先出 
                if (n.left) q.push(n.left) // 再推左邊
            }
        }
    }
    return res[res.length-1]

};

```


## other
### [1315. Sum of Nodes with Even-Valued Grandparent](https://leetcode.com/problems/sum-of-nodes-with-even-valued-grandparent/)
把grandparent是even的那些node.val 加總

```js fold
// 這一層的parent會是下一層的grandparent, 因此可以在這一層判斷後透過參數傳到下一層


var sumEvenGrandparent = function(root) {
    let res = 0
    
    function dfs(node, pNode){
        if(node) {
            
            if(pNode && (pNode.val % 2) === 0){
                if(node.left) res += node.left.val
                if(node.right) res += node.right.val
            }
            
            dfs(node.left, node)
            dfs(node.right,node)
        }
    }
    
    dfs(root, null)
    
    return res
    
};


```
### [1448. Count Good Nodes in Binary Tree](https://leetcode.com/problems/count-good-nodes-in-binary-tree/)
Good node ⇒ from root to node itself, node has the max value
```ts fold

var goodNodes = function(root) {
    
    let res = 0
    
    function dfs(node, curSum){
        if(!node) return
        
        
        if(node.val >= curSum) res++
        
        
        dfs(node.left, Math.max(curSum, node.val))
        dfs(node.right, Math.max(curSum, node.val))
    }
    
    // 錯誤點，dfs(root, 0)
    // 這樣如果 node.val 是 負數，Math.max 就會是 你自己定義的 `0`, 但應該還是要使用負數
    // 需要留意題目的邊界條件
    // 因為如果是 root, 那負數的 root 也算一個
    dfs(root, -Infinity) 
    return res
};
```


## N-ary Tree
### traversal
[N-ary Tree Preorder Traversal - LeetCode](https://leetcode.com/problems/n-ary-tree-preorder-traversal/)
[N-ary Tree Postorder Traversal - LeetCode](https://leetcode.com/problems/n-ary-tree-postorder-traversal/)
[N-ary Tree Level Order Traversal - LeetCode](https://leetcode.com/problems/n-ary-tree-level-order-traversal/)

```ts fold

var preorder = function(root) {
    
    let res = []
    function dfs(node){
        if(node){
            
            res.push(node.val)
            
            for(let n of node.children){
                dfs(n)                 
            }
            
        }
    }
    
    dfs(root)
    return res
};


var postorder = function(root) {
    let res = []
    function dfs(node){
       
        for(const n of node.children){
            dfs(n)
        }
         res.push(node.val)
    }
    
    dfs(root)
    return res
    
};


var levelOrder = function(root) {
    let res = []
    let q = [root]
    while (q.length>0){
        let tmp = []
        let qlen = q.length
        for(let i=0;i<qlen;i++){
            let n = q.shift()
            if(n){
                tmp.push(n.val)
                if(n.children){
                    for(let node of n.children){
                        q.push(node)
                    }
                }
            }
        }
        if(tmp.length>0) res.push(tmp)
    }
    
    return res
    
    
};
```


### [559. Maximum Depth of N-ary Tree](https://leetcode.com/problems/maximum-depth-of-n-ary-tree/)
```js fold

function maxDepth(root: _Node | null): number {

    function dfs(node){
        if(!node) return 0
        let height = 0
        for(let ele of node.children){
            height = Math.max(height, dfs(ele))
        }
        return 1 + height
    }

    return dfs(root)
    
};


// binary tree 參考
// function maxDepthForBinaryTree(root: _Node | null): number {
//     function dfs(node){
//         if(!node) return 0
//         let leftH = dfs(node.left)
//         let rightH = dfs(node.right)
//         return 1 + Math.max(leftH, rightH)
//     }
// };



```
就是寫出等價於 binary tree 的 nary 版本




## Build tree


## [108 Convert Sorted Array to Binary Search Tree](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/description/)
```js fold

var sortedArrayToBST = function(nums) {
    let l = 0
    let r = nums.length-1
    return dfs(l, r)
    function dfs(l, r){
        
        if(l>r) return null  // 左邊已經超過右邊，建構完成
        
	    // 取中間元素作為根節點
        let m = Math.floor((r+l)/2)  
        let node = new TreeNode(nums[m], null, null)  
        
        node.left = dfs(l, m-1)  // 遞迴構建左子樹
        node.right = dfs(m+1, r) // 遞迴構建右子樹
        
        return node
    }
};

```

TIL
![[IMG-tree-20250810200112108.png|685]]