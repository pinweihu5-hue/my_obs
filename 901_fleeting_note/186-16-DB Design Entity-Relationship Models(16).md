[[CS186 Introduction to Database System]]


需要考慮的面相
![[IMG-186-16-DB Design Entity-Relationship Models(16)-20241016191520047.png|605]]



 三個角度去看
 user 和的 view
 logic 上的
 和物理上的(比較實體上的idx和物理上資料怎麼放)
 ![[IMG-186-16-DB Design Entity-Relationship Models(16)-20241016191520395.png|511]]
 ![[IMG-186-16-DB Design Entity-Relationship Models(16)-20241016191520548.png|542]]





這邊是說， data 盡量要獨立
因為外部環境和情況變化很快
然後最後 程式設計要聲明，要解耦，可以下面變，但是用的那邊不會用到影響
![[IMG-186-16-DB Design Entity-Relationship Models(16)-20241016191520693.png]]

透過 view, 可以可以和邏輯層分開
- 有acl 功能
- 因為 view 和 logical layer 分開，因此也可以讓 logic layer 變了，但 query view 的人可能也不會受到影響

另外實體層可以分就分
- 類似 pricing 放 pricing
- auth 放 auth

你會覺得很麻煩，因為這種分層，分開的放，工作變多了
- 需要處理交互
- 需要處理彼此間的介接

但是這樣的設計會比較彈性
- 因為允許你去有不同的組合
- app 邏輯不會綁在一起，如果有改變，你動了下面，上面用的人可能不需要調整
- 或是你動了 A, 可能不會影響到 B, 如果你 A, B 本來就是分開的。但是如果你混在一起，那就有大機率A你改了, B也壞了。


＝
中間有說很多 ER 的東西
目前感覺用不到先跳過

==





![[IMG-186-16-DB Design Entity-Relationship Models(16)-20241016191520806.png|713]]


[[what is Participation Constraints means in ER relational modeling?]]
- capture participation constraints involving one entity set in a binary relationship
```sql

CREATE TABLE  Dept_Mgr(
   did  INTEGER,
   dname  CHAR(20),
   budget  REAL,
   ssn  CHAR(11) NOT NULL, -- total participation!
   since  DATE,
   PRIMARY KEY  (did),
   FOREIGN KEY  (ssn) REFERENCES Employees
      ON DELETE NO ACTION)

```

[[key constraints 和 participation constraints 的意義在 ER relational modeling?]]




[[ER entity 中的 Binary vs. Ternary Relationships]]



[[ Weak Entities in Database 的 ER modeling]]

![[IMG-186-16-DB Design Entity-Relationship Models(16)-20241016191520948.png|512]]
```sql

-- 員工家屬保險政策
CREATE TABLE  Dep_Policy (
   pname  CHAR(20),  -- 家屬 name
   age  INTEGER,
   cost  REAL,
   ssn  CHAR(11) NOT NULL, 
   PRIMARY KEY  (pname, ssn),
   FOREIGN KEY  (ssn) REFERENCES Employees   -- 員工 SSN
      ON DELETE CASCADE)

```



# Summary of Conceptual Design
- **Conceptual** **design** follows requirements analysis 
	- Yields a high-level description of data to be stored 
	- ER model popular for conceptual design
	- Constructs are expressive, close to the way we think about applications.
		- Note: There are many variations on ER model
- Both graphically and conceptually
- Basic constructs: **entities**, **relationships**,  and **attributes** (of entities and relationships).
- Some additional constructs**: weak entities**,  ISA hierarchies (see text if you’re curious),  and aggregation.
- Basic integrity constraints
	- **key constraints**
	- **participation constraints**
- Some **foreign key** constraints are also implicit in the definition of a relationship set.
- Many other constraints (notably, **functional dependencies**) cannot be expressed.
- Constraints play an important role in determining the best database design for an enterprise.
- ER design is **subjective**.  Many ways to model a given scenario!
- Analyzing alternatives can be tricky! Common choices include:
- Entity vs. attribute, entity vs. relationship, binary or n-ary relationship, whether or not to use aggregation
- For good DB design: resulting relational schema should be analyzed and refined further. 
- Functional Dependency information  
    + normalization coming in subsequent lecture.