[[CS186 Introduction to Database System]]


很多層，每一層都各司其職, 獨立
每一層都依賴於下一層提供的抽象
這是複雜度管理必要的, rdbms 有上百萬行
也有效能假設


like OSI model
1. Query Parsing & Optimization: Parse, check, and verify the SQL
2. Relational Operators: Execute a dataflow by operating on records and files, like idx_join, index scan 等等
3. Files and Index Management: Organize tables and Records as groups of pages in a logical file
4. Buffer Management: page in ram <-> disk 的交互, Provide the illusion of operating in memory
5. Disk Space Management: Translate page requests into physical bytes on one or more device(s)


最後還有 cocurrent control and recovery

![[IMG-186_04_Files and Buffers-20241003104933636.png]]


下面討論 disk and SSD

disk 主要得減少 seek time and rotation delay
盡量得 seq read/wrtie

SSD, flash 
價格是10x, 速度是 10x~100x
一樣 seq weite 比較有效能


disk 的 unit 是block
- _Sequentially_: “Next” disk block is fastest
- Predict future behavior
	- Cache popular blocks 
	- Pre-fetch likely-to-be-accessed blocks
	- Buffer writes to sequential blocks 
	- More on these as we go


Block = Unit of transfer for disk read/write
64KB – 128KB is a good number today

Page: a common synonym for “block”
In some texts, “page” = a block-sized chunk of RAM

We’ll treat “block” and “page” as synonyms

為了portable, 基本上都是 build on top of FS, 不會 directly impl on device
![[IMG-186_04_Files and Buffers-20241003104948744.png|587]]


Summary: Disk Space Management
- Provide API to read and write pages to device
- Pages: block level organization of bytes on disk
- Provides “next” locality and abstracts FS/device details


Disks and Files: Summary
- Magnetic (hard) disks and SSDs
	- Basic HDD mechanics
	- SSD write amplification
	- Concept of “near” pages and how it relates to cost of access
	- Relative cost of 
		- Random vs. sequential disk access (10x)
		- Disk (pluto) vs RAM (sacramento) vs. registers (your head)
		- Big, big differences!

- DB File storage
	- Typically over FS file(s)
- Disk space manager loads and stores pages
- Block level reasoning
- Abstracts device and file system; 
- provides fast “next”