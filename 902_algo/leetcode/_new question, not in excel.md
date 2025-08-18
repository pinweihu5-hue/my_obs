




==


### [Binary Tree Zigzag Level Order Traversal](https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/description/)
```js fold

var zigzagLevelOrder = function (root) {
    if(root===null) return []
    let res = []
    let q = [root]
    let levelCount = 0
    while (q.length !== 0) {
        let qlen = q.length
        let level = []
        for (let i = 0; i < qlen; i++) {
            let n = q.shift()
            if (n) {
                let val = n.val
                if (levelCount % 2 === 0) {
                    level.push(val)
                } else {
                    level.unshift(val)
                }
                let left = n.left
                let right = n.right
                if (left) {
                    q.push(n.left)
                }
                if (right) {
                    q.push(n.right)
                }
            }
        }
        res.push(level)
        level = []
        levelCount++
    }

    return res
};

```
til:
- 就是用一個變數記錄目前的 bfs 的深度，然後不同的深度用 `push` or `unshift`




===


consider to try

[Next Permutation - LeetCode](https://leetcode.com/problems/next-permutation/description/)
then this 好像有關係 [Next Greater Element III - LeetCode](https://leetcode.com/problems/next-greater-element-iii/description/)



hard but it got ez solution -> [32. Longest Valid Parentheses](https://leetcode.com/problems/longest-valid-parentheses/solutions/5895691/simple-solution-with-explanation/)



### 彙整
- https://hackmd.io/@meyr543
- https://leetcode.com/discuss/general-discussion/460599/blind-75-leetcode-questions
- https://leetcode.com/studyplan/top-interview-150/
- https://neetcode.io/practice
- [grind169](https://www.techinterviewhandbook.org/grind75?hours=10&weeks=15)
- https://www.piratekingdom.com/leetcode/study-guide
- [Graph For Beginners \[Problems | Pattern | Sample Solutions\] - LeetCode Discuss](https://leetcode.com/discuss/general-discussion/655708/graph-for-beginners-problems-pattern-sample-solutions/562734)
- [**25-variations-of-two-sum-question](https://leetcode.com/discuss/interview-question/3685049/25-variations-of-two-sum-question)'**
- [**Binary-search-a-comprehensive-guide**](https://leetcode.com/discuss/study-guide/3726061/binary-search-a-comprehensive-guide)
- [**Sliding Window Technique: A Comprehensive Guide**](https://leetcode.com/discuss/study-guide/3722472/sliding-window-technique-a-comprehensive-guide)
- [**NumberTheroy part1**](https://leetcode.com/discuss/study-guide/3735417/topics-which-you-cant-skip-interview-preparation-part-1)
- [**Modulus Operator | NumberTheory Part 2**](https://leetcode.com/discuss/study-guide/3767785/topics-which-you-cant-skip-interview-preparation-part-2)
- [**All Types of Patterns for Bits Manipulations**](https://leetcode.com/discuss/career/5861551/all-types-of-patterns-for-bits-manipulations-and-how-to-use-it)



### grid
- bfs
	- [01 Matrix - LeetCode](https://leetcode.com/problems/01-matrix/)
### graph and island 問題
- 更多 MST → [https://leetcode.com/problem-list/minimum-spanning-tree/](https://leetcode.com/problem-list/minimum-spanning-tree/)
- 更多 dijkstra → [https://leetcode.com/problem-list/53js48ke/](https://leetcode.com/problem-list/53js48ke/)
1. [https://leetcode.com/problems/number-of-closed-islands/](https://leetcode.com/problems/number-of-closed-islands/)
2. [https://leetcode.com/problems/number-of-islands/](https://leetcode.com/problems/number-of-islands/)
3. [https://leetcode.com/problems/keys-and-rooms/](https://leetcode.com/problems/keys-and-rooms/)
4. [https://leetcode.com/problems/max-area-of-island/](https://leetcode.com/problems/max-area-of-island/)
5. [https://leetcode.com/problems/flood-fill/](https://leetcode.com/problems/flood-fill/)
6. [https://leetcode.com/problems/coloring-a-border/](https://leetcode.com/problems/coloring-a-border/)
- Flood Fill: [https://leetcode.com/problems/flood-fill/](https://leetcode.com/problems/flood-fill/)
- Evaluate Division: [https://leetcode.com/problems/evaluate-division/](https://leetcode.com/problems/evaluate-division/)
- Robot Room Cleaner: [https://leetcode.com/problems/robot-room-cleaner/](https://leetcode.com/problems/robot-room-cleaner/)
- Most Stones Removed with Same Row or Column: [https://leetcode.com/problems/most-stones-removed-with-same-row-or-column/](https://leetcode.com/problems/most-stones-removed-with-same-row-or-column/)
- Reconstruct Itinerary: [https://leetcode.com/problems/reconstruct-itinerary/](https://leetcode.com/problems/reconstruct-itinerary/)
- Tree Diameter: [https://leetcode.com/problems/tree-diameter/](https://leetcode.com/problems/tree-diameter/)
- Accounts Merge: [https://leetcode.com/problems/accounts-merge/](https://leetcode.com/problems/accounts-merge/)
- [Graph For Beginners [Problems | Pattern | Sample Solutions] - LeetCode Discuss](https://leetcode.com/discuss/general-discussion/655708/graph-for-beginners-problems-pattern-sample-solutions/562734)
- [Graph algorithms + problems to practice - LeetCode Discuss](https://leetcode.com/discuss/study-guide/1326900/graph-algorithms-problems-to-practice)
- **BFS problems**
    - Flood Fill: [https://leetcode.com/problems/flood-fill/](https://leetcode.com/problems/flood-fill/)
    - Evaluate Division: [https://leetcode.com/problems/evaluate-division/](https://leetcode.com/problems/evaluate-division/)
    - Get Watched Videos by Your Friends: [https://leetcode.com/problems/get-watched-videos-by-your-friends/](https://leetcode.com/problems/get-watched-videos-by-your-friends/)
    - Cut Off Trees for Golf Event: [https://leetcode.com/problems/cut-off-trees-for-golf-event/](https://leetcode.com/problems/cut-off-trees-for-golf-event/)
- **DFS problems**
    - Flood Fill: [https://leetcode.com/problems/flood-fill/](https://leetcode.com/problems/flood-fill/)
    - Evaluate Division: [https://leetcode.com/problems/evaluate-division/](https://leetcode.com/problems/evaluate-division/)
    - Robot Room Cleaner: [https://leetcode.com/problems/robot-room-cleaner/](https://leetcode.com/problems/robot-room-cleaner/)
    - Most Stones Removed with Same Row or Column: [https://leetcode.com/problems/most-stones-removed-with-same-row-or-column/](https://leetcode.com/problems/most-stones-removed-with-same-row-or-column/)
    - Reconstruct Itinerary: [https://leetcode.com/problems/reconstruct-itinerary/](https://leetcode.com/problems/reconstruct-itinerary/)
    - Tree Diameter: [https://leetcode.com/problems/tree-diameter/](https://leetcode.com/problems/tree-diameter/)
    - Accounts Merge: [https://leetcode.com/problems/accounts-merge/](https://leetcode.com/problems/accounts-merge/)
- **Connected components problems**
    - [https://leetcode.com/problems/critical-connections-in-a-network/](https://leetcode.com/problems/critical-connections-in-a-network/)
    - [https://leetcode.com/problems/number-of-provinces/](https://leetcode.com/problems/number-of-provinces/)
    - [https://leetcode.com/problems/number-of-operations-to-make-network-connected/](https://leetcode.com/problems/number-of-operations-to-make-network-connected/)
- **Dijkstra's problems**
    - Path With Maximum Minimum Valued: [https://leetcode.com/problems/path-with-maximum-minimum-value/](https://leetcode.com/problems/path-with-maximum-minimum-value/)
    - Path With Minimum Effort: [https://leetcode.com/problems/path-with-minimum-effort/](https://leetcode.com/problems/path-with-minimum-effort/)
- **Minimum Spanning Tree problems**
    - Connecting Cities With Minimum Cost: [https://leetcode.com/problems/connecting-cities-with-minimum-cost/](https://leetcode.com/problems/connecting-cities-with-minimum-cost/)
    - Min Cost to Connect All Points: [https://leetcode.com/problems/min-cost-to-connect-all-points/](https://leetcode.com/problems/min-cost-to-connect-all-points/)
- **Floyd Warshall problems**
    - Find the City With the Smallest Number of Neighbors at a Threshold Distance: [https://leetcode.com/problems/find-the-city-with-the-smallest-number-of-neighbors-at-a-threshold-distance/](https://leetcode.com/problems/find-the-city-with-the-smallest-number-of-neighbors-at-a-threshold-distance/)
    - Network delay time: [https://leetcode.com/problems/network-delay-time/](https://leetcode.com/problems/network-delay-time/)
- **Bellman-Ford**
	- 



### dp
- [Form Largest Integer With Digits That Add up to Target - LeetCode](https://leetcode.com/problems/form-largest-integer-with-digits-that-add-up-to-target/) hard
- [Range Sum Query - Immutable - LeetCode](https://leetcode.com/problems/range-sum-query-immutable/) ez
- [Maximum Profit in Job Scheduling - LeetCode](https://leetcode.com/problems/maximum-profit-in-job-scheduling/) hard
- [Filling Bookcase Shelves - LeetCode](https://leetcode.com/problems/filling-bookcase-shelves/) med
- [Minimum Number of Taps to Open to Water a Garden - LeetCode](https://leetcode.com/problems/minimum-number-of-taps-to-open-to-water-a-garden/) hard
- [Count All Valid Pickup and Delivery Options - LeetCode](https://leetcode.com/problems/count-all-valid-pickup-and-delivery-options/) hard
- [neet_code_play_list](https://www.youtube.com/c/NeetCode/playlists)
- https://leetcode.com/problems/the-skyline-problem/
- [Lily's Homework | HackerRank](https://www.hackerrank.com/challenges/lilys-homework/problem)
- https://leetcode.com/discuss/study-guide/458695/Dynamic-Programming-Patterns
- [Problem List - LeetCode](https://leetcode.com/list?selectedList=ejo4957m)
- Unbounded Knapsack:
    1. Rod cutting problem
    2. Coin Change 1
    3. Coin Change 2
    4. Maximum Ribbon Cut
    5. Number Partitioning
- 0/1 Knapsack:
    1. Subset Sum
    2. Equal Sum Partition
    3. Count of subset with given sum
    4. Minimum subset sum difference
    5. Target Sum
    6. No of susbet with given difference
    7. Count of subsets with given difference
    8. Last Stone Weight 2(LC 1049)



### backtracking
- [Backtracking algorithm + problems to practice - LeetCode Discuss](https://leetcode.com/discuss/study-guide/1405817/Backtracking-algorithm-%2B-problems-to-practice)
- https://leetcode.com/problems/sudoku-solver/
- https://leetcode.com/problems/n-queens/