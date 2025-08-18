[[CS61B Data Structures and Algorithms|cs61b_index]]


recap
![[IMG-cs61b tries-20241003104933910.png|810]]

如果我們對 key 的特徵有更多了解，或是可以限縮只存某一種，我們可以設計更快的 impl

say 我們只需要存 ascii char?
![[IMG-cs61b tries-20241003104948843.png]]

we can use DataIndexedCharMap

O(1) with no caveat

char 本身對應的 int 就是 idx

simple and fast

![[IMG-cs61b tries-20241003105004952.png]]

那如果我們要存的是 string
![[IMG-cs61b tries-20241003105026799.png]]

可以用 bst or hashtable, 還有其他方法嗎？
![[IMG-cs61b tries-20241003105051402.png]]

我們可以用 tries

- 每個 node 放一個 letter
- node 可以 share letter of other string

try to add 下面的字
![[IMG-cs61b tries-20241003105120121.png]]

blue node 表示這個 string 到這邊結束
![[IMG-cs61b tries-20241003105132624.png]]

two way to know you have a miss

- 最後一個 node 不是 end-node(blue node)
- 跑到樹外
  ![[IMG-cs61b tries-20241003105141370.png]]

也可以 是 map, 多一個放 vaue
![[IMG-cs61b tries-20241003105141533.png]]

# Trie Implementation and Performance

root node
用 128 的空間放
isKey means blue node
用 next 連到一個 DataIndexedCharmap 來連下去
![[IMG-cs61b tries-20241003105148972.png]]

DataIndexedCharmap 其實就是一個 arr
每一個 node 都有 一個 next 可以 access to this arr
![[IMG-cs61b tries-20241003105155101.png]]

![[IMG-cs61b tries-20241003105155178.png]]

![[IMG-cs61b tries-20241003105200624.png]]

我們不需要 char ch 這個欄位
因為連過去時就已經知道 char 了
![[IMG-cs61b tries-20241003105208831.png]]

N 是這個 ties 存有多少 key

不管你有100個 key 還是 1million key
你要 access apple, 都是一樣的 run time
-> 因此是 constant run time

如果你要看更細
那access 的確會因為你的 string 的length 長度有差異
因此你也可以說是 O(L) where L is the legnth of string

![[IMG-cs61b tries-20241003105213158.png]]


總覽
到目前為止，看到的一個缺點就是滿多空間的，因為你需要建立 128 len arr for each node in every layer and probably most of them is not used
![[IMG-cs61b tries-20241003105216731.png]]

# Alternate Child Tracking Strategies


這種方法的缺點建立的很多 mem都不會用到
![[IMG-cs61b tries-20241003105221293.png]]




sparse arr
![[IMG-cs61b tries-20241003105224986.png]]


透過 hash-table 連到下一個 letter
![[IMG-cs61b tries-20241003105228727.png]]



透過 bst 連到下一個 letter
![[IMG-cs61b tries-20241003105232273.png]]



三種 trie impl 的比較
![[IMG-cs61b tries-20241003105235821.png]]



- bst or hash table 會用 less mem
- bst or hash table 會多花一點點時間
	- R: 一個 node 會連到到的 node, ascii will be 128 個 node
	- bst -> O(logR)
	- hash table -> O(R), worse case, 因為要一個一個
- 但是因為 R 會給定且固定, 128 -> 我們也可以說，全部都是 O(1)

![[IMG-cs61b tries-20241003105238511.png]]





要用那個 impl, 實際上，還是要自己的 app 壓測才知道
![[IMG-cs61b tries-20241003105241131.png]]


# Trie String Operations

trie 特別的地方還是在於可以處理 "特定的文字操作"

prefix matching, 找最長的prefix 等等文字操作
![[IMG-cs61b tries-20241003105243782.png]]


![[IMG-cs61b tries-20241003105246188.png|926]]



拿出所有的 key
![[IMG-cs61b tries-20241003105248299.png]]



一直呼叫下去
第一個 para 是 char, 然後一直 concat 下去
第二個是 result 
第三個是 Node

如果是 key, 就加入 result
然後每一層都 loop over `next.keys()`

![[IMG-cs61b tries-20241003105253361.png]]


![[IMG-cs61b tries-20241003105255641.png]]



algo for prefix match
![[IMG-cs61b tries-20241003105257863.png]]


先找到 sa
然後就開始一路走下去，組起來
![[IMG-cs61b tries-20241003105300067.png]]



# Autocomplete


trie 很適合
可以存百萬多個字，不影響速度，很多都共用
![[IMG-cs61b tries-20241003105301923.png]]


多一個 value 來決定要優先度
類似我們找到 s 開頭的，然後顯示前10名
![[IMG-cs61b tries-20241003105303578.png]]


問題是...
如果我們打 "s", 那我們要找出全部(a lot!)的 s 開頭的再顯示前10個?
![[IMG-cs61b tries-20241003105303728.png]]



一個解法
我們每一個 node 都會記錄 best substring 的 score
(因此下面如果是 key node, 就會記錄自己的 score 和上面傳下來的 best substring 的 score)

因此我們找 node, 就可以只拉分數前幾高的 best substring 的那些 node, 不用全部都下去找
這邊可以用 PQ 做!
![[IMG-cs61b tries-20241003105305644.png]]



另一個提高效率的方法, 如果一串上都沒有 key, 然後可以到最後有 key 的那個 node, 全部壓成一個node -> redix trie/tree
![[IMG-cs61b tries-20241003105305736.png]]

# Trie Summary
![[IMG-cs61b tries-20241003105307265.png]]



![[IMG-cs61b tries-20241003105308712.png]]
[Deterministic acyclic finite state automaton - Wikipedia](https://en.wikipedia.org/wiki/Deterministic_acyclic_finite_state_automaton)
[Suffix tree - Wikipedia](https://en.wikipedia.org/wiki/Suffix_tree)





Tries, K-d Trees, Tree Traversals Exam Prep: 
Q: 給定 node找第k個祖先node
```java fold

import java.util.ArrayList;  
import java.util.LinkedList;  
import java.util.List;  
  
public class Tree<T> {  
    private Node root;  
  
    private static class Node<T> {  
        public T item;  
        public ArrayList<Node> children;  
  
        public Node(T item) {  
            this.item = item;  
            this.children = new ArrayList<>();  
        }  
    }  
  
    public Tree(T rootItem) {  
        root = new Node(rootItem);  
    }  
  
    public Node getRoot() {  
        return root;  
    }  
  
    public Node addChild(Node parent, T item) {  
        Node child = new Node(item);  
        parent.children.add(child);  
        return child;  
    }  
  
    public void printTree() {  
        printTreeHelper(root, 0);  
    }  
  
    private void printTreeHelper(Node node, int depth) {  
        // 每多一層，往右邊多一個 indent, print 要橫著看  
        for (int i = 0; i < depth; i++) {  
            System.out.print("  ");  
        }  
        System.out.println(node.item);  
        for (Object child : node.children) {  
            printTreeHelper((Node) child, depth + 1);  
        }  
    }  
  
  
    /**  
     * This method takes in an integer k and a Node target, and returns the k’th ancestor of target in our tree     * (you may assume such an ancestor exists).     */    public Node getAncestor(int k, Node target) {  
        List<Node> path = new LinkedList<>();  
        ancestorHelper(target, root, path); // 從根節點開始，遍歷樹以尋找目標節點  
        return path.get(path.size() - 1 - k);  
    }  
  
  
    /**  
     * 這是一個遞迴輔助方法  
     * 它從根節點開始，遍歷樹以尋找目標節點  
     */  
    private boolean ancestorHelper(Node target, Node cur, List<Node> path) {  
        path.add(cur); // 在遍歷過程中，它將訪問的節點添加到一個列表中  
        if (cur == target) {  
            return true;  // 如果找到目標節點，它會返回 true，保留整個路徑  
        }  
  
        for (Object child : cur.children) {  
            if (ancestorHelper(target, (Node) child, path))  
                return true;  
        }  
  
        // 如果這一條路徑沒有找到目標節點，目標節點就不是在這個 path 中  
        // 不需要在path記錄，當前node會從列表中移除當前節點並返回 false        path.remove(cur);  
        return false;    }  
  
  
    // 測試方法  
    public static void main(String[] args) {  
        Tree<String> tree = new Tree<>("A");  
        Node root = tree.getRoot();  
        Node b = tree.addChild(root, "B");  
        Node c = tree.addChild(root, "C");  
        Node d = tree.addChild(b, "D");  
        Node e = tree.addChild(b, "E");  
        Node f = tree.addChild(c, "F");  
  
        System.out.println("樹結構：");  
        tree.printTree();  
//        樹結構： 這個要橫著看  
//        A  
//          B  
//            D  
//            E  
//          C  
//            F  
  
        System.out.println("\n測試 getAncestor：");  
        System.out.println("E 的 1 級祖先：" + tree.getAncestor(1, e).item);  
        System.out.println("E 的 2 級祖先：" + tree.getAncestor(2, e).item);  
        System.out.println("F 的 1 級祖先：" + tree.getAncestor(1, f).item);  
        System.out.println("D 的 2 級祖先：" + tree.getAncestor(2, d).item);  
    }  
}

```



# trie drawing
[[trie-08-23 10.25.51.excalidraw]]




# trie recall  at 2024_08_24-09_48_40
- runtime
	- get a string: O(L), L is the length of string
	- 重點在於 L is not related to N, the number of string in the Trie. so 如果你有1000萬筆, 還是100萬筆，不影響
	- insert a string also O of L
- a tree but want to go faster then ln of N, since we want to store char in each node and string, a 特別用法
- data structure is more complicated
	- a node, 裡面有 array, each idx is the ascii for char, and ele is a node, this node, also have a array, each idx is the ascii for char..一層一層下去, a sparse array
- use recursive helper to get and put
	- 3 arg needed
		- Node, stringIdx, and String itself
			- we need to have Node to access its arr
			- we need to stringIdx to covert to ascii and use ascii as idx to access arr ele
			- we need to use string to know its length => to know if we reach the end
	- each recusive call, go down one level to create a new node(or no need to create)
	- when ret, we also need to the retured node to its upper level pos -> `Node.arrIdxMap[ascii]`
- good at collect prefix