[[CS186 Introduction to Database System]]


one word to take away for schema normal form design:
如果有兩個欄位有關連，但其中一個又不是 key, 或有唯一性-> 暗示這個表有冗於 -> 這兩個其實就可以抽出去當做另一個 entity

---


The Evil to Avoid: Redundancy in your schema
- i.e. replicated values
- leads to wasted storage 
- Far more important: insert/delete/update **anomalies**
- Replicated data + change = Trouble.


解法?

- Solution: Functional Dependencies
	- a form of integrity constraints
	- help identify redundancy in schemas
	- help suggest refinements
	- [[資料庫設計中的 Functional Dependencies]]
- Main refinement technique:  Decomposition 
	- split the columns of one table into two tables
	- often good, but need to do this judiciously


各種 Key -> [Notion – The all-in-one workspace for your notes, tasks, wikis, and databases.](https://www.notion.so/nture4388/what-is-super-key-candidate-key-70815ad911f4440d86b985afd3388f87?pvs=4)


[[利用 Functional Dependencies 找出 冗餘]]


FD 例子

- Consider relation Hourly_Emps:
   Hourly_Emps (ssn, name, lot, rating, wage_per_hr, hrs_per_wk) or 屬性縮寫 SNLRWH
- What are some FDs on Hourly_Emps?
	- **_ssn_** is the primary key:  S → SNLRWH 
	- **_rating_** determines _wage_per_hr_:    R → W



R->W 會重複，因此有潛在問題
![[IMG-186-17-Functional Dependencies and Schema Refinement-20241102165833823.png|680]]


![[IMG-186-17-Functional Dependencies and Schema Refinement-20241102165833951.png|688]]


The Notion of Normal Forms

- If relation is in a _normal form_ (e.g. **BCNF**):
	- we know certain problems are avoided/minimized.  
	- helps decide whether decomposing relation is useful.
- Consider a relation R with 3 attributes, ABC.  
	- _No (non-trivial) FDs hold_:   No redundancy here.
	- _Given A_ → _B:_   **If A is not a key,** then several tuples could have the same A value, and if so, they'll all have the same B value! -> redundancy!


 **如果 一個 table 中有兩個欄位關連，但其中一個又沒有唯一性 (PK 就有唯一性) -> 那可能就有 redundancy !**


1st ⊃ 2nd (of historical interest)  ⊃ 3rd (of historical interest)    ⊃ Boyce-Codd
=> 上面就是說，後面會包括前面

Boyce-Codd Normal Form  (BCNF)
- Rela R with FDs F is in BCNF if, for all X → A  in F+
	- A ⊆ X   (called a trivial FD), or
	- X is a superkey for R.
- In other words: “R is in BCNF if  
    the only non-trivial FDs over R are key constraints.”
- 翻譯：表中的所有欄位中，FD的關係 (X -> 其他欄位) 的 X 一定得是 PK, FK, Candidate key
- **換個角度說: 如果有兩個欄位有關連，但其中一個又不是 key -> 暗示這兩個其實就可以抽出去當做另一個 entity辣!!**

一般來說，符合 BCNF 就很夠了


![[IMG-186-17-Functional Dependencies and Schema Refinement-20241102165834131.png|617]]


Problems with Decompositions
- There are three potential problems to consider:
	1) May be **_impossible_** to reconstruct the original relation!  (Lossiness) 
		-  [[loseless vs lossy decomposition]]
		
- Fortunately, not in the SNLRWH example.
	1) Dependency checking may require joins.
- Fortunately, not in the SNLRWH example.
	1) Some queries become more expensive.  
- **_Tradeoff_****:   Must consider these 3 vs. redundancy.**



- **Dependency preserving decomposition** (Intuitive):
- A decomposition where the following is true:
	- If R is decomposed into X, Y and Z, 
	- and  we enforce FDs individually on each of X, Y and Z, 
	- then  all FDs that held on R must also hold on result.  
	- (Avoids Problem #2 on our list.)
	- [[Dependency Preserving Decomposition]]


- Consider relation R with FDs F.
	-  If X → Y violates BCNF, decompose R into  R - Y and XY (guaranteed to be loss-less).
	- Repeated application of this idea will give us a collection of relations that are in BCNF
	- Lossless join decomposition, and guaranteed to terminate.



例子：CSJDPQV,  key C,  JP → C,  SD → P,   J → S
 {contractid, supplierid, projectid,deptid,partid, qty, value}
To deal with SD → P, decompose into  SDP(so, keep SD -> P 的 FD), CSJDQV( P移掉了).
To deal with J → S, decompose CSJDQV,  把 S 拆掉，into JS (既有 FD)  and C(c 是 key) JDQV (S移掉了)
So we end up with: SDP, JS, and CJDQV


# Summary of Schema Refinement
- BCNF: each field contains data that cannot be inferred via FDs.
	- ensuring BCNF is a good heuristic.
- Not in BCNF?  Try decomposing into BCNF relations.
	- Must consider whether all FDs are preserved!
- What to do when a lossless-join, dependency preserving decomposition into BCNF is impossible?  
	- There is a more _permissive_ Third Normal Form (3NF) 
		- In the hidden slides of slide deck, or book, or Wikipedia
	- But you’ll have redundancy. Beware. You will need to keep it from being a problem in your application code.
- Note: even more _restrictive_ Normal Forms exist
	- we don't cover them in this course, but some are in the book

