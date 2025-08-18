[\[面試\]\[資料庫\]設計資料庫時會考量哪些點？ - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10271178)


1. 
check ER Model 
整理資料間的關聯性 
is sitll make sense

2. (mySQL 限定)
不確定該欄位會填入的文字長度，選擇 VARCHAR

3. (mySQL 限定)
時常變更文字的欄位來說，會選擇 CHAR 
(因為 VARCHAR 的欄位在儲存時會需要額外的計算, 常常變化會有這個overhead)
 
4. (mySQL 限定)
盡量避免使用 TEXT/BLOB 這類的 Type (more compuatation needed)

5. 
根據實際業務需求設計欄位長度

6. 
use batch 減少 SQL 寫入次數

7. 
雖然設計table會想要正規化，
more complex join also make thing slower down, 
so, some reduencent can make trade some space for speed, 
用空間換時間

8. 
定期review下面項目
不確定實際業務需求，一開始設計時把欄位長度開到最大
 
系統版本的更迭中，沒有整理不符合實際業務需求的檢視表（View）
view is outdated?

review idex, is the cureent access pattern matching current idx?