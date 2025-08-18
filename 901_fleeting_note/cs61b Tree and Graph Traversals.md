# Trees and Traversals

what is tree
-> 有 cycle 就不是 tree
-> tree is subset of graph
![[IMG-cs61b Tree and Graph Traversals-20241003104933891.png]]

root, parent, child, leaf
![[IMG-cs61b Tree and Graph Traversals-20241003104948822.png]]

tree in many impl
![[IMG-cs61b Tree and Graph Traversals-20241003105004036.png]]

tree is a very common concept
![[IMG-cs61b Tree and Graph Traversals-20241003105004110.png]]

![[IMG-cs61b Tree and Graph Traversals-20241003105023356.png]]

![[IMG-cs61b Tree and Graph Traversals-20241003105023504.png]]

![[IMG-cs61b Tree and Graph Traversals-20241003105049610.png]]

preorder
![[IMG-cs61b Tree and Graph Traversals-20241003105115722.png]]

inorder
![[IMG-cs61b Tree and Graph Traversals-20241003105130851.png]]

post order
![[IMG-cs61b Tree and Graph Traversals-20241003105130947.png]]

眼睛 trick
preorder and inorder 上面我有用紅筆註記了
post order: 當你經過右邊才 visit

![[IMG-cs61b Tree and Graph Traversals-20241003105139724.png]]



![[IMG-cs61b Tree and Graph Traversals-20241003105139811.png|451]]

![[IMG-cs61b Tree and Graph Traversals-20241003105147669.png|656]]




也可以用 pegs, 多畫一個線
![[IMG-cs61b Tree and Graph Traversals-20241003105147794.png|927]]


![[IMG-cs61b Tree and Graph Traversals-20241003105154193.png]]


![[IMG-cs61b Tree and Graph Traversals-20241003105159852.png]]



列出檔案要用 preorder 才可以顯示下面那個 list, 這個才直覺
![[IMG-cs61b Tree and Graph Traversals-20241003105207873.png|740]]

計算檔案大小，要用 postorder, 因為你要全部都走過，回到上面，你才能全部都抓到每個 child 的檔案大小
![[IMG-cs61b Tree and Graph Traversals-20241003105212265.png|810]]

# Graphs

have cycle -> graph
有多個 path from a to b
![[IMG-cs61b Tree and Graph Traversals-20241003105212373.png|789]]

tree def
![[IMG-cs61b Tree and Graph Traversals-20241003105215881.png|757]]

graph def

graph is super set of tree, all tree are graphs
![[IMG-cs61b Tree and Graph Traversals-20241003105215989.png|877]]

have cyle
![[IMG-cs61b Tree and Graph Traversals-20241003105219278.png|927]]
![[IMG-cs61b Tree and Graph Traversals-20241003105219411.png|231]]

what is simple graph
- no cycle
- no parallel edges (下面中間)
![[IMG-cs61b Tree and Graph Traversals-20241003105223705.png]]

![[IMG-cs61b Tree and Graph Traversals-20241003105223806.png]]


![[IMG-cs61b Tree and Graph Traversals-20241003105227788.png]]

graph 分類

有方向性
是否有 loop (acycli vs cyclic)
是否有 label? (on edge or on vertex)
![[IMG-cs61b Tree and Graph Traversals-20241003105227875.png]]

more terminology
![[IMG-cs61b Tree and Graph Traversals-20241003105231409.png]]

例子
- 看不出有方向性  -> undirected
- 都連在一起   -> connected
- 有 loop -> cyclic  (not a tree)
- vertex 有標籤 (use color)
  ![[IMG-cs61b Tree and Graph Traversals-20241003105231514.png]]

下面是圖
像是 tree, but it's not
下面 group_action 有兩個 path 出去 -> 這邊就形成 cycle
![[IMG-cs61b Tree and Graph Traversals-20241003105235177.png]]

# Graph Problems

有 graph 有關的問題

- 兩點最短路徑
- 最長路徑 (without cycle)
- 有 cycle?
- 是否有 path 可以 vivist all vertices 剛好 一次
- 是否有 path 可以 vivist all edge 剛好 一次
  ![[IMG-cs61b Tree and Graph Traversals-20241003105235290.png]]

**一些常見的圖論問題及其名稱：**

- **s-t 路徑：**是否存在一條從頂點 s 到頂點 t 的路徑？
- **連通性：**圖是否連通？換句話說，是否所有頂點之間都存在路徑？
- **雙連通性：**是否存在一個頂點，移除它後會導致圖不連通？
- **最短 s-t 路徑：**頂點 s 和頂點 t 之間的最短路徑是什麼？
- **環偵測：**圖中是否包含環？
- **歐拉環遊：**是否存在一個環遊，恰好經過每條邊一次？
- **漢米爾頓環遊：**是否存在一個環遊，恰好經過每個頂點一次？
- **平面性：**你能在紙上畫出這個圖，並且邊沒有交叉嗎？
- **同構：**兩個圖是否同構（即本質上是同一個圖，只是呈現方式不同）？

通常，如果不深入思考，很難判斷一個圖論問題的難度。

**圖論**是數學和電腦科學中的一個重要分支，用於研究圖形結構以及它們之間的關係。這些問題在許多應用中都有廣泛的應用，例如：

- **網路分析：**例如，尋找社交網路中的社群或分析交通網路。
- **路徑規劃：**例如，尋找地圖上兩個地點之間的最短路徑或規劃機器人的移動路徑。
- **資料探勘：**例如，分析資料集中的模式或識別資料點之間的關係。

了解這些常見的圖論問題及其名稱，對於解決實際應用中的圖論問題至關重要。

![[IMG-cs61b Tree and Graph Traversals-20241003105237958.png]]

![[IMG-cs61b Tree and Graph Traversals-20241003105238067.png|730]]

# Depth-First Traversal

## s-t connectivity probelm

確認 s and t 兩個 vertex 是否連接
![[IMG-cs61b Tree and Graph Traversals-20241003105240499.png|817]]

ans:
also see this demo: [cs61b s-t connectivity demo - Google 簡報](https://docs.google.com/presentation/d/1OHRI7Q_f8hlwjRJc8NPBUc1cMu5KhINH1xGXWDfs_dA/edit#slide=id.g76e0dad85_2_380)
![[IMG-cs61b Tree and Graph Traversals-20241003105240598.png]]



開始
- mark visit node
- 如果 s == target(這裡是 7) -> ret True
- 如果 s != target  -> 遞迴呼叫 connected(unmarked_s_的鄰居, target) -> 如果這邊有找到，最後會 ret True
- 如果上面 return 結束，這邊 ret False
![[IMG-cs61b Tree and Graph Traversals-20241003105243026.png]]


first step
mark 0
0 != 7 
且 1 還沒被 mark
遞迴呼叫 1, connected(1, 7)

PS: 有需要也可以用 connectedHelper
![[IMG-cs61b Tree and Graph Traversals-20241003105243110.png]]

到1
mark 1
not equal 7
看鄰居, 0 and 2
0 is marked -> no go
2 no marked -> 可以呼叫 -> connected(2, 7)
![[IMG-cs61b Tree and Graph Traversals-20241003105245590.png]]


mark2
2 != 7 
1 is mared -> 不走
5 還沒 mark -> 遞迴呼叫connected(5,7)
![[IMG-cs61b Tree and Graph Traversals-20241003105245677.png]]


mark 5
2 已經mark , 不走
另外有4,6,7 都可以走，要走那個，都可以, 這邊選擇4, connected (4,7)
![[IMG-cs61b Tree and Graph Traversals-20241003105247742.png]]



mark 4
4 != 7
1,5 都已經marked 不走
呼叫 connected(3,7)
![[IMG-cs61b Tree and Graph Traversals-20241003105247834.png]]


mark 3
3!= 7
4 is marked 不走
全部鄰居都沒有 往下走
return False
![[IMG-cs61b Tree and Graph Traversals-20241003105250313.png]]


回到上一層, vertex 4
vertex 4 一樣 all 鄰居都走過
ret false
![[IMG-cs61b Tree and Graph Traversals-20241003105255066.png]]


繼續回到上一層到 vertex 5
2 已經走了
6,8 還沒走，選一個, 選那個都沒差，這邊選 6, connected(6,7)
![[IMG-cs61b Tree and Graph Traversals-20241003105255163.png]]



到7 了， mark7,
7 == 7 -> ret true!
![[IMG-cs61b Tree and Graph Traversals-20241003105257320.png]]


ret true 傳到 回到上一層 vertex 6
![[IMG-cs61b Tree and Graph Traversals-20241003105257412.png]]


ret true 傳到 回到上一層 vertex 5
![[IMG-cs61b Tree and Graph Traversals-20241003105259383.png]]


ret true 傳到 回到上一層 vertex 2
(PS: 這裡根本不需要往8找了，因為找到了)
![[IMG-cs61b Tree and Graph Traversals-20241003105259520.png]]


ret true 傳到 回到上一層 vertex 1
![[IMG-cs61b Tree and Graph Traversals-20241003105301537.png]]

ret true 傳到 回到上一層 vertex 0
![[IMG-cs61b Tree and Graph Traversals-20241003105301629.png]]



最後return true

done!
![[IMG-cs61b Tree and Graph Traversals-20241003105303262.png]]


把一整個子樹找到底，ret 回去，然後再去看另一個 -> 這就是 DFS
![[IMG-cs61b Tree and Graph Traversals-20241003105303344.png]]





搞笑漫畫...約會規劃不適合用 dfs...
![[IMG-cs61b Tree and Graph Traversals-20241003105305390.png]]



DFS 的應用非常廣
用 DFS 來找從 a 點到所有 點的 path

## Depth First path
Goal: Find a path from s to every other reachable vertex, visiting each vertex at most once

demo: [61B depth first paths demo - Google 簡報](https://docs.google.com/presentation/d/1lTo8LZUGi3XQ1VlOmBUF9KkJTW_JWsw_DOPq8VBiI3A/edit#slide=id.g76e0dad85_2_380)
![[IMG-cs61b Tree and Graph Traversals-20241003105307004.png]]



demo

init state
![[IMG-cs61b Tree and Graph Traversals-20241003105307127.png]]


PS: 也可以有一個 dfsHelper 幫忙

一開始呼叫 dfs(0)
- marked 0 to True
- 往下走, 鄰居是 1, is 1 marked, 沒有
- edgeTo[1] = 0 (表示 到1 的上一個 vertex 是 0)
- then 呼叫 dfs(1)

這邊也用 color 表示是否有被 mark
![[IMG-cs61b Tree and Graph Traversals-20241003105308498.png]]

- marked 1 to True
- 往下走, 鄰居是, 0, 2, 
- 0 is marked -> 不管
- 2 is not mark
	- edgeTo[2] = 1  and dfs(2)
![[IMG-cs61b Tree and Graph Traversals-20241003105308597.png]]

- marked 2 to True
- 往下走, 鄰居是, 1, 5, 
- 1 is marked -> 不管
- 5 is not mark
	- edgeTo[5] = 2  and dfs(5)

edgeTo 也表示一個往 vertex 5 的路 (0->1->2)
![[IMG-cs61b Tree and Graph Traversals-20241003105309895.png]]


- marked 5 to True
- 往下走, 鄰居是, 2, 4, 8 
- 2 is marked -> 不管
- 隨便挑一個 unamrk to go, 4 is not mark
	- edgeTo[4] = 5  and dfs(4)
![[IMG-cs61b Tree and Graph Traversals-20241003105309972.png]]


- marked 4 to True
- 往下走, 鄰居是, 1, 5, 3 
- 1,5 is marked -> 不管
- 3 is not mark
	- edgeTo[3] = 4  and dfs(3)
![[IMG-cs61b Tree and Graph Traversals-20241003105311188.png]]


mark 3
no child -> ret to vertex 4
![[IMG-cs61b Tree and Graph Traversals-20241003105311290.png]]


no child -> ret to vertex 5
![[IMG-cs61b Tree and Graph Traversals-20241003105313282.png]]


- vertx 5 的6,8 are not mark yet, pick 6
	- edgeTo[6] = 5  and dfs(6)
![[IMG-cs61b Tree and Graph Traversals-20241003105313379.png]]

- marked 6 to True
- 往下走, 鄰居是 5,7
- 5 is marked -> 不管
- 7 is not mark
	- edgeTo[7] = 6  and dfs(7)
![[IMG-cs61b Tree and Graph Traversals-20241003105315429.png]]



mark 7
no child -> ret to 6
![[IMG-cs61b Tree and Graph Traversals-20241003105317092.png]]


conti ret to 5
![[IMG-cs61b Tree and Graph Traversals-20241003105317940.png]]



- vertx 5 的 8 are not mark yet
	- edgeTo[8] = 5  and dfs(8)
![[IMG-cs61b Tree and Graph Traversals-20241003105318058.png]]


mark 8
鄰居is 5 -> marked
no more child -> ret to 5
![[IMG-cs61b Tree and Graph Traversals-20241003105319032.png]]


no child, ret to 2
![[IMG-cs61b Tree and Graph Traversals-20241003105319115.png]]

no child, ret to 1
PS: 可以留意，右下也有記錄 dfs return
![[IMG-cs61b Tree and Graph Traversals-20241003105320090.png]]


no child, ret to 0
![[IMG-cs61b Tree and Graph Traversals-20241003105320204.png]]


return to caller! done
![[IMG-cs61b Tree and Graph Traversals-20241003105321328.png]]

# Tree Vs. Graph Traversals

graph 上面介紹的 dfs 叫做 dfs preorder 可以對應到 tree 的 order
(下面是優先選 val 小的鄰居的結果，但怎麼選都可以)
![[IMG-cs61b Tree and Graph Traversals-20241003105321453.png]]

graph 也有另一種走法叫做 dfs postorder , 也對應到tree 的 postorder
![[IMG-cs61b Tree and Graph Traversals-20241003105322537.png]]




另外tree 的level order 對應到的 graph 叫做 bfs (breadth first search) 廣度 search, 不是深度
![[IMG-cs61b Tree and Graph Traversals-20241003105322657.png|893]]


下一堂課會教
![[IMG-cs61b Tree and Graph Traversals-20241003105326994.png|828]]


# Summary

- 樹和圖的差異
- 圖的種類
- 如何走(traversal) 是解決樹/圖問題的關鍵
	- tree: pre, post, in, level
	- graph: dfs pre, dfs post, bfs

![[IMG-cs61b Tree and Graph Traversals-20241003105327132.png|789]]



# Bipartite
ref: [Bipartite Graphs - Georgia Tech - Computability, Complexity, Theory: Algorithms - YouTube](https://www.youtube.com/watch?v=Zg6UAnAzGGs)

就是你的每一個 egge, 都是一邊紅色，一邊黑色

why bipartite 等價於 no odd-length cycle?
因為你如果想要分兩邊，但是奇數cycle如下，你一定會有 edge 讓你兩邊的 color 一樣£
![[IMG-cs61b Tree and Graph Traversals-20241003105328610.png|837]]



# 二分圖問題
clab9
[Challenge Lab 9: Graphs | CS 61B Spring 2019](https://sp19.datastructur.es/materials/clab/clab9/clab9)
```java fold

import java.io.File;  
import java.io.FileNotFoundException;  
import java.util.*;  
  
public class SeparableEnemySolver {  
  
    Graph g;  
  
    /**  
     * Creates a SeparableEnemySolver for a file with name filename. Enemy     * relationships are biderectional (if A is an enemy of B, B is an enemy of A).     */    SeparableEnemySolver(String filename) throws java.io.FileNotFoundException {  
        this.g = graphFromFile(filename);  
    }  
  
    /**  
     * Alterntive constructor that requires a Graph object.     */    SeparableEnemySolver(Graph g) {  
        this.g = g;  
    }  
  
  
    /**  
     * You’re hosting a party. 
     * Unfortunately, many of your invitees are sworn enemies of each other. As the organizer, to ensure aggregate happiness, 
     * you’d like to separate the party-goers into two groups such that no two members of the same group are enemies. 
     * You’d like to know if this is possible for your party.       
     * Your friend Euler gives you a graph representing enmity-relationships among the invitees: 
     * Each invitee is mapped to a vertex, and an edge ((u, v)) means invitee (u) and invitee (v) are enemies.  
     * Implement the function isSeparable() in the SeparableEnemySolver class, which returns true if the invitees represented by g are separable, 
     * and false otherwise. Your solution should run in (O(V+E)) time where (V) is the number of people, and (E) is the number of enemy relationships..  
     * Returns true if input is separable, false otherwise.  
     * 
     * solution take from: // https://github.com/FlyNeopolitan/CS61B/blob/master/clab9/SeparableEnemySolver.java  
     * 這個方法用來判斷是否可以將一群人（派對邀請者）分成兩組，使得每組內部沒有互為敵人的人。這個問題在圖論中被稱為「二分圖」問題。  
     */  
    public boolean isSeparable() {  
  
        // 創建一個 partitionMap，用來記錄每個人屬於哪個組（1 或 2）。初始時，所有人都被標記為 0（未分組）。  
        Map<String, Integer> partitionMap = new HashMap<>();  
  
        // 創建一個 BFSQueue 用於廣度優先搜索。  
        Queue<String> BFSQueue = new ArrayDeque<>();  
  
        // 遍歷所有人 to get 圖的所有節點  
        for (String i : g.labels()) {  
            partitionMap.put(i, 0);  
        }  
        System.out.println(partitionMap);  
  
        /**  
         * 對於每個未分組的人，開始一次廣度優先搜索：  
         * a. 將這個人加入隊列，並標記為第 1 組。  
         * b. 當隊列不為空時，取出一個人（當前節點）。  
         * c. 遍歷當前節點的所有鄰居（敵人）：  
         *  
         * 如果鄰居未分組，將其加入隊列，並分到另一組。  
         * 如果鄰居已經分組，檢查是否與當前節點在同一組。若在同一組，返回 false（無法分離）。  
         *  
         * * 以第一個測試為例子：  
         * 第一輪 A 和 A 的鄰居跑完 A 分到第一組， B, C, D 都是第二組  
         * 第二輪 B 開始跑，第一個鄰居 A, 已經分組，不會進入 `if (partitionMap.get(m) == 0)` block, B 只有 A 這個連接。結束  
         * 第三輪 C 開始跑，第一個鄰居 A, 已經分組，同上，和 A 不同組，也不會 ret false, 第二個鄰居 D, 就會撞上同租， ret false  
         * 
         * 這個算法利用 BFS 來遍歷 all vertex (so use queue)  
         * 利用 hash map 來記錄(key is vertex in string and value is 組別)  
         * 
		 * 一開始就會讓 source vertex 是一組，然後讓其鄰居都不同組  
         * 如果後面撞到同組，表示無法無法達到(鄰居無法不同組) -> 因此返回 false  
         */  
        for (String i : g.labels()) {  
            if (partitionMap.get(i) == 0) { // 0 表示還沒分組  
                BFSQueue.add(i);  
                partitionMap.put(i, 1); // 加入第一組  
                while (!BFSQueue.isEmpty()) {  
                    String currentNode = BFSQueue.poll();  
                    for (String m : g.neighbors(currentNode)) { // get A 的鄰居  
                        if (partitionMap.get(m) == 0) { // 如果鄰居還沒分組，也加入 queue                            
                            BFSQueue.add(m);  
                            // 然後把鄰居加入另一組  
                            partitionMap.put(m, otherThanX(partitionMap.get(currentNode)));  
                        }  
                        if (partitionMap.get(m).equals(partitionMap.get(currentNode))) {  
                            return false;  
                        }  
                    }  
                }  
            }  
        }  
  
  
        return true;  
    }  
  
    private int otherThanX(int x) {  
        if (x == 1) {  
            return 2;  
        } else {  
            return 1;  
        }  
    }  
  
  
    /* HELPERS FOR READING IN CSV FILES. */  
  
    /**  
     * Creates graph from filename. File should be comma-separated. The first line     * contains comma-separated names of all people. Subsequent lines each have two     * comma-separated names of enemy pairs.     */    private Graph graphFromFile(String filename) throws FileNotFoundException {  
        List<List<String>> lines = readCSV(filename);  
        Graph input = new Graph();  
        for (int i = 0; i < lines.size(); i++) {  
            if (i == 0) {  
                for (String name : lines.get(i)) {  
                    input.addNode(name);  
                }  
                continue;  
            }  
            assert (lines.get(i).size() == 2);  
            input.connect(lines.get(i).get(0), lines.get(i).get(1));  
        }  
        return input;  
    }  
  
    /**  
     * Reads an entire CSV and returns a List of Lists. Each inner     * List represents a line of the CSV with each comma-seperated     * value as an entry. Assumes CSV file does not contain commas     * except as separators.     * Returns null if invalid filename.     *     * @source https://www.baeldung.com/java-csv-file-array  
     */    private List<List<String>> readCSV(String filename) throws java.io.FileNotFoundException {  
        List<List<String>> records = new ArrayList<>();  
        Scanner scanner = new Scanner(new File(filename));  
        while (scanner.hasNextLine()) {  
            records.add(getRecordFromLine(scanner.nextLine()));  
        }  
        return records;  
    }  
  
    /**  
     * Reads one line of a CSV.     *     * @source https://www.baeldung.com/java-csv-file-array  
     */    private List<String> getRecordFromLine(String line) {  
        List<String> values = new ArrayList<String>();  
        Scanner rowScanner = new Scanner(line);  
        rowScanner.useDelimiter(",");  
        while (rowScanner.hasNext()) {  
            values.add(rowScanner.next().trim());  
        }  
        return values;  
    }  
  
    /* END HELPERS  FOR READING IN CSV FILES. */  
  
}


```



[disc10.pdf](https://sp19.datastructur.es/materials/discussion/disc10.pdf)
[disc10sol.pdf](https://sp19.datastructur.es/materials/discussion/disc10sol.pdf)

sol 有提到
preOrder and postOrder 用 stack 可以一次解掉
BFS 用 queue 解

pre order
![[IMG-cs61b Tree and Graph Traversals-20241003105328724.png]]





- [[graph-adj-list.excalidraw]]