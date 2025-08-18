[[CS186 Introduction to Database System]]


why sort?


- when need it?
	- Eliminating duplicates (DISTINCT)
	- Grouping for summarization (GROUP BY)
	- Upcoming sort-merge join algorithm
- ordering
	- Sometimes, output must be ordered (ORDER BY)
	- First step in bulk-loading tree indexes


Problem: sort 100GB of data with 1GB of RAM


ram 不夠大怎半??


- Two themes
1. Single-pass streaming data through RAM
2. Divide (into RAM-sized chunks) and Conquer



stream with double buffer
![[IMG-186_10_sorting and hashing-20241003104933653.png|595]]





merge sort 補充資訊
- [(612) Merge Sort Animation - YouTube](https://www.youtube.com/watch?v=dxuIwsPKRts)
- [(612) Merge Sort Visualized and Recursion Explained - YouTube](https://www.youtube.com/watch?v=4acikH5mXwk)




sort and hash 正式規範
B 是 block of mem
R 是 record
一個 F 有 N 個 block or page
![[IMG-186_10_sorting and hashing-20241003104948746.png|703]]



merge sort 成本算法
![[IMG-186_10_sorting and hashing-20241003105000461.png|783]]
**雙向外部合併排序 (Two-Way External Merge Sort)** 的概念，這是一種用於排序大量資料的演算法，特別適用於資料量太大無法一次載入記憶體的情況。

**演算法步驟：**
1. **初始階段 (PASS 0):** 將輸入檔案 (Input file) 分成多個大小為一頁 (page) 的區塊，並將每個區塊排序。
2. **合併階段 (PASS 1, 2, 3...):**
    - **征服與合併 (Conquer and Merge):** 每次合併階段，將兩個已排序的子檔案合併成一個更大的已排序檔案。
    - **讀寫操作：** 每個合併階段，需要讀取和寫入檔案中的每一頁資料，因此成本為 2N (N 是檔案中的頁數)。
3. **重複合併：** 重複合併階段，直到所有資料都合併到一個已排序的檔案中。
    

**成本分析：**
- **合併次數：** 檔案中有 N 頁，則需要進行 ⌈log₂N⌉ + 1 次合併階段 (⌈⌉ 表示向上取整)。
- **總成本：** 每次合併階段的成本為 2N，因此總成本為 2N (⌈log₂N⌉ + 1)。

雙向外部合併排序是一種高效的外部排序演算法，其時間複雜度為 O(N log N)，適用於處理大量資料。




![[IMG-186_10_sorting and hashing-20241003105017033.png|647]]



![[IMG-186_10_sorting and hashing-20241003105043320.png|553]]



另一個方案：**雜湊 (Hashing)**

- **排序並非必要：** 許多資料庫操作並不需要資料保持排序，例如：
    - **移除重複資料：** 只需要辨識並刪除重複的資料，順序不重要。
    - **分組：** 將相同特性的資料歸類，順序也不影響結果。
- **重點在於匹配：** 這些操作的重點在於快速找到匹配的資料，雜湊法可以有效達成此目標。
- **外部儲存的挑戰：** 簡報最後提出一個問題：如何在外部儲存 (out-of-core) 的情況下使用雜湊法？

**外部儲存 (Out-of-core)** 指的是資料量太大，無法一次全部載入記憶體的情況。這時需要將資料分段儲存在硬碟等外部儲存設備，並分批讀取處理。



**Divide**
- Streaming Partition (divide):  
    Use a hash function hp to stream records to disk partitions
	- All matches rendezvous in the same partition.
	- Each partition a mix of values
	- Streaming alg to create partitions on disk: 
	- “Spill” partitions to disk via output buffers
	    
![[IMG-186_10_sorting and hashing-20241003105105577.png|432]]


**Conquer**
- ReHash (conquer):  
    Read partitions into RAM hash table one at a time, using hash f’n hr
	- Each bucket contains a small number of distinct values
- Then read out the RAM hash table buckets and write to disk
	- Ensuring that duplicate values are contiguous

![[IMG-186_10_sorting and hashing-20241003105125884.png]]


![[IMG-186_10_sorting and hashing-20241003105135582.png|598]]



![[IMG-186_10_sorting and hashing-20241003105144123.png|673]]




**Parallelize hasing**!

![[IMG-186_10_sorting and hashing-20241003105151342.png|577]]


**Parallelize sorting**!

![[IMG-186_10_sorting and hashing-20241003105151426.png|608]]




**雜湊法的優點 (Hashing pros):**
- **移除重複資料效率高：**
    - 在資料分區 (partitioning) 的過程中，可以在第一階段就刪除重複資料。
    - 當資料量很大時，排序法的效率的去重效率會降低。
- **易於平行處理：** 輕鬆地將資料平均分配到不同的處理單元，方便進行平行處理，提升效率。
    

**排序法的優點 (Sorting pros):**
- **輸出結果已排序：** 如果最終需要輸出排序後的資料，那麼排序法就是最佳選擇，因為它本身就產生排序後的結果。
- **不受重複資料和雜湊函數影響：** 排序法不受資料中重複值或雜湊函數好壞的影響，穩定性較高。
    

**總結：**
- 雜湊法適合處理需要移除重複資料或需要平行處理的大量資料。
- 排序法適合處理需要輸出排序結果的資料，且不受重複資料和雜湊函數影響。






**Summary**
- Sort/Hash Duality
	- Sorting is Conquer & Merge
	- Hashing is Divide & Conquer
- Sorting is overkill sometimes
	- But sometimes a win anyhow
- Don’t forget one pass streaming and double buffering
	- Can “hide” the latency of I/O behind CPU work
