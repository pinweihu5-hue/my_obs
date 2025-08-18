[[CS186 Introduction to Database System]]



![[IMG-186-13-Relational Query Optimization II Costing and Searching-20241003104933564.png|676]]

這張投影片在討論"單資料庫索引"的成本估算，特別是針對以下 SQL 查詢：

```sql
SELECT S.sid
FROM Sailors S
WHERE S.rating=8
```

它分析了在 `rating` 欄位上有索引的情況下，使用叢集索引 (clustered index) 和非叢集索引 (unclustered index) 的成本差異。

**為什麼非叢集索引需要檢索 4005 頁？**
* **基數 (Cardinality):**  首先，計算出 `rating=8` 的選擇性 (selectivity)，也就是符合條件的資料佔總資料的比例。假設基數為 1/10，表示有 1/10 的資料符合 `rating=8`。
* **非叢集索引的特性：** 非叢集索引不儲存實際資料，而是儲存指向資料頁的指標。因此，當使用非叢集索引時，需要先透過索引找到符合條件的資料指標，然後再根據指標去資料頁讀取實際資料。
* **成本計算：**
    * 索引頁：假設索引本身佔用 50 頁。
    * 資料頁：由於基數為 1/10，符合條件的資料約有 40000 個 tuples (40000 tuples * 1/10 = 4000 tuples)。假設每個資料頁可以儲存 10 個 tuples，則需要 4000 頁來儲存這些資料 (4000 tuples / 10 tuples/page = 4000 pages)。
    * 總成本：需要檢索的總頁數為索引頁加上資料頁，也就是 50 + 4000 = 4050 頁。

PS: 簡報中的 4005 頁應該是計算錯誤，正確的結果應該是 4050 頁

**總結：**
非叢集索引需要檢索大量資料頁的原因是它只儲存資料指標，而不儲存實際資料。因此，每次查詢都需要額外讀取資料頁，導致成本增加。相比之下，叢集索引直接儲存資料，可以減少資料頁的讀取次數，因此成本較低。

簡報中並沒有明確說明叢集索引為何佔用 50 頁，這只是一個假設的數字，用於說明成本計算的概念。

**簡報中假設叢集索引佔用 50 頁，只是為了簡化計算，並突顯叢集索引和非叢集索引在成本上的差異。** 實際上，叢集索引的大小可能遠大於 50 頁，也可能小於 50 頁，需要根據實際情況進行估算。



![[IMG-186-13-Relational Query Optimization II Costing and Searching-20241003104948736.png|730]]
這張投影片在討論關聯式查詢優化中的 **左深層計畫 (Left-deep plans)** 和 **有趣的順序 (Interesting Orders)**。

**左深層計畫 (Left-deep plans):**

* 左深層計畫是一種常見的查詢執行計畫，其中所有連接操作都以左連接 (left join) 的方式進行，形成一個樹狀結構，最左邊的關係作為根節點。
* 投影片中提到了左深層計畫的差異性：
    * **關係順序：** 不同的左深層計畫可能使用不同的關係連接順序。
    * **存取方法：** 每個葉節點操作 (leaf operator) 可能使用不同的存取方法，例如索引掃描或全表掃描。
    * **連接方法：** 每個連接操作 (join operator) 可能使用不同的連接方法，例如巢狀迴圈連接 (nested loop join) 或雜湊連接 (hash join)。

**列舉左深層計畫：**

* 假設要連接 N 個關係，可以使用 N 個遍歷 (pass) 來列舉所有可能的左深層計畫：
    * **遍歷 1：**  為每個關係找到最佳的單一關係計畫 (1-relation plan)。
    * **遍歷 i：**  找到將第 i 個關係 (作為外部關係) 連接到 (i-1) 個關係計畫 (作為內部關係) 的最佳方法。 (i 的範圍是 2 到 N)

**有趣的順序 (Interesting Orders):**

* 對於每個關係子集，只保留以下兩種計畫：
    * **整體成本最低的計畫：**  考慮所有可能的連接順序、存取方法和連接方法，選擇成本最低的計畫。
    * **每個有趣順序成本最低的計畫：**  有些查詢可能需要輸出特定順序的結果，例如排序後的結果。對於每個這樣的「有趣順序」，需要保留成本最低的計畫。

**圖示說明：**

投影片中的圖示展示了兩個不同的左深層計畫，它們連接了相同的關係 (A、B、C、D)，但連接順序不同。

**總結：**

* 左深層計畫是一種常見的查詢執行計畫，但不同的左深層計畫可能具有不同的成本。
* 查詢優化器需要考慮所有可能的左深層計畫，並選擇成本最低的計畫。
* 對於需要特定輸出順序的查詢，需要保留每個「有趣順序」成本最低的計畫。




![[IMG-186-13-Relational Query Optimization II Costing and Searching-20241003104959886.png|782]]
討論關聯式查詢優化中如何列舉執行計畫 (Enumeration of Plans)，特別是 System R 資料庫系統中使用的動態規劃 (Dynamic Programming, D.P.) 演算法。

**主要步驟：**
1. **先找出掃描和連接操作：**  使用動態規劃找出最佳的掃描 (scan) 和連接 (join) 操作，涵蓋查詢中的選擇 (select)、投影 (project) 和連接操作。
    * **避免笛卡爾積：**  在動態規劃過程中，要避免產生笛卡爾積 (Cartesian Products)，也就是兩個關係不做任何條件限制的連接，會產生大量無意義的結果。
        * 只有在以下情況才考慮將一個 i-1 路 (way) 子計畫與另一個資料表連接：
            * 它們之間存在連接條件。
            * 或者，WHERE 子句中的所有謂詞 (predicate) 都已經在 i-1 路子計畫中使用過。
2. **後處理排序、分組和聚合：**  將排序 (ORDER BY)、分組 (GROUP BY)、聚合 (aggregates) 等操作作為後處理步驟。
    * **利用「有趣順序」計畫：**  如果選擇了「有趣順序」計畫，則這些操作可以免費執行。
    * **或者，使用額外的排序/雜湊操作：**  如果沒有選擇「有趣順序」計畫，則需要額外的排序或雜湊操作來完成這些後處理步驟。

**演算法複雜度：**
* 儘管進行了剪枝 (pruning) 優化，System R 的動態規劃演算法仍然是 **指數級** 的，複雜度隨著資料表數量的增加而呈指數增長。

**總結：**
* System R 使用動態規劃來列舉查詢執行計畫，並透過避免笛卡爾積來優化效率。
* 排序、分組和聚合等操作作為後處理步驟，可以利用「有趣順序」計畫來避免額外成本。
* 儘管經過優化，動態規劃演算法的複雜度仍然很高，特別是當資料表數量很多時。








![[IMG-186-13-Relational Query Optimization II Costing and Searching-20241003105016054.png|706]]
![[IMG-186-13-Relational Query Optimization II Costing and Searching-20241003105042256.png|458]]
![[IMG-186-13-Relational Query Optimization II Costing and Searching-20241003105105003.png|685]]
這個例子展示了關聯式查詢優化中，如何使用動態規劃 (Dynamic Programming) 來列舉和選擇最佳的查詢執行計畫。

**查詢：**

```sql
SELECT S.sid, COUNT(*) AS number
FROM Sailors S, Reserves R, Boats B
WHERE S.sid = R.sid
AND R.bid = B.bid
AND B.color = "red"
GROUP BY S.sid
```

**資料表索引：**
* Sailors: 雜湊索引 (Hash) 和 B+ 樹索引 (B+ tree) 在 sid 欄位上。
* Reserves: 叢集 B+ 樹索引 (Clustered B+ tree) 在 bid 欄位上，以及 B+ 樹索引在 sid 欄位上。
* Boats: B+ 樹索引在 color 欄位上。

**Pass 1: 為每個關係找到最佳計畫：**
* 針對每個資料表，考慮所有可能的存取方法 (例如，全表掃描、索引掃描)，並選擇成本最低的計畫。
* 此外，也考慮「有趣順序」(Interesting order)，也就是查詢中需要排序的欄位，並找出針對這些欄位排序的最佳計畫。

**結果：**

| 資料表子集 | 有趣順序欄位 | 最佳計畫 | 成本 |
|---|---|---|---|
| {Sailors} | -- | 全表掃描 (filescan) |  |
| {Reserves} | -- | 全表掃描 (Filescan) |  |
| {Boats} | -- | B-tree on color |  |
| {Reserves} | (bid) | B-tree on bid |  |
| {Sailors} | (sid) | B-tree on sid |  |

**Pass 2:  為每對關係找到最佳計畫：**

* 針對每個左深層邏輯計畫 (left-deep logical plan)，也就是將兩個關係以左連接的方式連接起來的計畫，考慮所有可能的實體計畫 (physical plan)，包括：
    * 遍歷 Pass 1 中的每個計畫。
    * 遍歷每個資料表的每個存取方法。
    * 遍歷每個連接方法。
* 產生所有可能的計畫組合，例如：
    * 全表掃描 Reserves (外部) 與 Sailors (內部) 連接。
    * Reserves 的 B+ 樹索引在 bid 上 (外部) 與 Boats (內部) 連接。
    * ...
* 針對每對關係和每個順序，保留成本最低的計畫。

**結果：**

| 資料表子集 | 有趣順序欄位 | 最佳計畫 | 成本 |
|---|---|---|---|
| {Sailors} | -- | 全表掃描 (filescan) |  |
| {Reserves} | -- | 全表掃描 (Filescan) |  |
| {Boats} | -- | B-tree on color |  |
| {Reserves} | (bid) | B-tree on bid |  |
| {Sailors} | (sid) | B-tree on sid |  |
| {Boats, Reserves} | (B.bid, R.bid) | 排序合併連接 (SortMerge) - (B-tree on Boats.color, 全表掃描 Reserves) |  |
| Etc... |  |  |  |

**Pass 3 及之後：**

* 使用 Pass 2 的計畫作為外部關係，以相同的方式產生下一個連接的計畫。
    * 例如，將 `{SortMerge(B-tree on Boats.color, 全表掃描 Reserves)}` (外部) 與 `Sailors (B-tree sid)` (內部) 連接。
* 然後，加上分組/聚合的成本：
    * 這是將結果依 sid 排序的成本，除非它已經被之前的操作排序過。
* 最後，選擇成本最低的計畫。

**總結：**

動態規劃演算法透過迭代的方式，逐步找出最佳的查詢執行計畫。它從單一資料表的最佳計畫開始，然後逐步擴展到多個資料表的連接，最終找到整體成本最低的計畫。





![[IMG-186-13-Relational Query Optimization II Costing and Searching-20241003105125568.png|738]]

D.dname 已經有 b tree idx, 已經會去 idx 拿出 toy
然後就剩下一個
因此Dno上的 idx 的 join 的效果也沒差了





![[IMG-186-13-Relational Query Optimization II Costing and Searching-20241003105135185.png|731]]

如果 e.age 有 idx
那撈出來後，剩下 age 25 的那些 emp record
剩下這個 record 去和 dept join

可能剩下的量也不多了
多一個  Emp.dno 的 idx (幫助 join) 的效果，可能不明顯




---


![[IMG-186-13-Relational Query Optimization II Costing and Searching-20241003105143884.png]]

這張投影片在討論關聯式查詢優化中，如何選擇合適的索引來加速查詢執行，特別是在使用索引巢狀迴圈連接 (Index Nested Loop Join) 的情況下。

**查詢：**

```sql
SELECT E.ename, D.mgr
FROM Emp E, Dept D
WHERE E.sal BETWEEN 10000 AND 20000
AND E.hobby='Stamps'
AND E.dno=D.dno
```

**分析：**
* **所有選擇條件都在 `Emp` 資料表上：**  查詢中的三個 WHERE 條件都針對 `Emp` 資料表，這表示在任何索引巢狀迴圈連接中，`Emp` 應該作為外部關係 (outer relation)。
* **建議在 `Dept` 資料表的 `D.dno` 欄位上建立 B+ 樹索引：**  因為連接條件是 `E.dno=D.dno`，所以可以在 `D.dno` 上建立索引，以便快速找到匹配的部門資料。

**在 `Emp` 資料表上應該建立什麼索引？**
* **可以在 `E.sal` 上建立 B+ 樹索引，或者在 `E.hobby` 上建立索引：**  這兩個索引都可以用於加速查詢，但哪個更好取決於條件的選擇性 (selectivity)。
* **只需要其中一個索引：**  因為兩個索引都針對 `Emp` 資料表，所以只需要建立其中一個即可。
* **經驗法則：等值選擇的選擇性通常高於範圍選擇：**  也就是說，`E.hobby='Stamps'` 這個等值選擇條件可能會比 `E.sal BETWEEN 10000 AND 20000` 這個範圍選擇條件過濾掉更多資料。

**總結：**
* 理解查詢優化器的運作方式有助於選擇合適的索引。
* 選擇索引時，需要考慮查詢的條件和資料的選擇性。
* 等值選擇的選擇性通常高於範圍選擇。


**如果建立兩個索引 (E.sal 和 E.hobby)，會發生以下情況：**
- 查詢優化器會選擇其中一個索引來掃描 Emp 資料表，而另一個索引則不會被使用。
- 額外的索引會增加資料庫的儲存空間和維護成本，但卻沒有帶來任何效能提升。


查詢優化器在面對多個可用索引時，只會選擇其中一個來執行查詢，主要基於以下幾個原因：
1. **成本考量:** 查詢優化器的目標是找到執行查詢的最低成本方案。使用多個索引通常會增加成本，因為需要額外的索引查找和資料讀取操作。
2. **索引重疊:** 如果多個索引包含相同的欄位，或者索引的覆蓋範圍高度重疊，那麼使用其中一個索引就能滿足查詢需求，使用多個索引並不會帶來額外的效益。
3. **索引類型:** 不同的索引類型 (例如，B+ 樹索引、雜湊索引) 適用於不同的查詢場景。查詢優化器會根據查詢條件和資料特性選擇最適合的索引類型，而不會同時使用多種類型的索引。
4. **查詢計畫的複雜度:** 使用多個索引會增加查詢計畫的複雜度，這可能會導致優化器需要花費更多時間來評估不同的計畫，並選擇最佳方案。


---


這邊列出了你需要 create 的 idx, 如果你不想要 hit heap file, 只想要透過 idx 就可以查詢
![[IMG-186-13-Relational Query Optimization II Costing and Searching-20241003105151117.png|696]]


- **索引必須包含所有需要的欄位：** 如果查詢需要獲取的欄位不在索引中，則無法使用索引僅計畫。
- **索引必須覆蓋查詢條件：** 如果查詢條件無法使用索引來過濾資料，則無法使用索引僅計畫。