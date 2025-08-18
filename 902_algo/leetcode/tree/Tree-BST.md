
### [783. Minimum Distance Between BST Nodes](https://leetcode.com/problems/minimum-distance-between-bst-nodes/)
```ts fold
function minDiffInBST(root: TreeNode | null): number {
    
    let prev = Infinity 
    // take care on this, if this is 0, then if the first one is 0 and then you get
    // 0 as reult, but the seq is 0,1,3,4 => expcted result is 1
    
    let res = Infinity
    
    let dfs = (node) => {
        if(!node) return
        dfs(node.left)
        
        let dif = Math.abs(node.val - prev)
        res = Math.min(res, dif)
        prev = node.val
        
        
        dfs(node.right)
    }
    
    
    dfs(root)
    return res

};
```

### [98. Validate Binary Search Tree](https://leetcode.com/problems/validate-binary-search-tree/)
```ts fold
/**
 * Definition for a binary tree node.
 * class TreeNode {
 *     val: number
 *     left: TreeNode | null
 *     right: TreeNode | null
 *     constructor(val?: number, left?: TreeNode | null, right?: TreeNode | null) {
 *         this.val = (val===undefined ? 0 : val)
 *         this.left = (left===undefined ? null : left)
 *         this.right = (right===undefined ? null : right)
 *     }
 * }
 */

function isValidBST(root: TreeNode | null): boolean {

    let res = true
    let prev = -Infinity

    function dfs(node){
        if(!node) return
        
        dfs(node.left)
         
        if(prev >= node.val) res = false
        prev = node.val
        
        dfs(node.right)
    }
    dfs(root)

    return res
    
};
```

### [230. Kth Smallest Element in a BST](https://leetcode.com/problems/kth-smallest-element-in-a-bst/)
```js fold
var kthSmallest = function(root, k) {
    
    let res = -1 // just use -1 as default, any number 不在 nod.val range內都可
    
    function dfs(node){
        if(!node) return
        
        dfs(node.left)
        
        // inorder go here
        k-=1
        if (k === 0) res = node.val
        
        
        
        dfs(node.right)
    }
    
    dfs(root)
    
    return res
    
};
```

### [538. Convert BST to Greater Tree](https://leetcode.com/problems/convert-bst-to-greater-tree/)
```js fold
function convertBST(root: TreeNode | null): TreeNode | null {
    let prevNodeVal = 0  // 因為會走到null 返回，因此就是0開始

	dfs(root)
    function dfs(node){
        if(!node) return
        dfs(node.right)

        node.val += prevNodeVal  // 這個點的 val 疊加 prevNodeVal
        prevNodeVal = node.val   // 這個疊加 prevNodeVal 的 node.val 傳到下一個


        dfs(node.left)
    }

  

    return root
    
};
```

### [235. Lowest Common Ancestor of a Binary Search Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)
给 p, q 找 p, q 的 最小共同祖先
```js fold

// 直接用return 解，建議作法，實際的利用遞迴

var lowestCommonAncestor = function (root, p, q) {

    return dfs(root)

    function dfs(node) {
        if (!node) return
        if (node.val < p.val && node.val < q.val) {
            return dfs(node.right)
        } else if (node.val > p.val && node.val > q.val) {
            return dfs(node.left)
        } else {
            return node
        }

    }

};



// 沒有用 return 解，另外使用一個 global var 去拿答案，比較不優雅，因此recursive 去答案，但是可以用recusive ret 答案確沒使用
// 還需要 waste 一個 變數

var lowestCommonAncestor = function (root, p, q) {
    let res = null
    dfs(root)
    return res

    function dfs(node) {
        if (!node) return
        if (node.val < p.val && node.val < q.val) {
            dfs(node.right)
        } else if (node.val > p.val && node.val > q.val) {
            dfs(node.left)
        } else {
            res = node
            return
        }

    }

};







```


這個寫法的 if-else 有點多餘，也是可以 pass
```js fold



function lowestCommonAncestor(root: TreeNode | null, p: TreeNode | null, q: TreeNode | null): TreeNode | null {

    let res
    function dfs(node) {
        if (node) {
        
        
            // 等於也可以，因此下面的比較有 "等於"
            // node.val 只要在 p,q 中間即可，因此會有兩種情況
            // 一種是 q >= node >= p
            if (p.val <= node.val && q.val >= node.val) {
                res = node
                return
                
            // 另一種是 p >= node >=q
            } else if (p.val >= node.val && q.val <= node.val) {
                res = node
                return
            
            // 剩下兩種情況，利用 BST 的特性來 traversal
            } else if (p.val > node.val && q.val > node.val) {
                dfs(node.right)
            } else if (p.val < node.val && q.val < node.val) {
                dfs(node.left)
            } 
            
            
        }
    }

    dfs(root)
    return res
};




```

錯誤記錄 
忘記 p 是 node. 需要用 p.val 去比較
```js fold

var lowestCommonAncestor = function(root, p, q) {
    
    let res = null
    
    function dfs(node){
        
        if(!node) return
        
        // 錯誤點，忘記 p 是 node. 需要用 p.val 去比較
        if (p.val > node.val && q.val > node.val) {
   	        dfs(node.right)
        } else if (p.val < node.val && q.val < node.val) {
            dfs(node.left)
        } else {
            // 這裡用 res 去拿node比較簡單
            // 用 return node..你上面的base case還需要調整...
            // 用 res 去接，就是全部path都找一次，不用管每一個path return的情況和補回等等
            res = node
            return
        }
    }
    
    dfs(root)
    
    return res
};



```


錯誤記錄
你要用 dfs 的 return 作為答案的話，你的函數的每一個 return 的地方就要正確的 return
因為這是遞迴，因此你 return 後，是會跑到其他地方的邏輯去 return 然後一層層的 解掉 callstack 後才會到 return 結束的
```js fold

var lowestCommonAncestor = function (root, p, q) {

    return dfs(root)

    function dfs(node) {
        if (!node) return
        if (node.val < p.val && node.val < q.val) {
            dfs(node.right) // 前面少了 return
        } else if (node.val > p.val && node.val > q.val) {
            dfs(node.left) // 前面少了 return
        } else {
            return node
        }

    }

};

```



---

