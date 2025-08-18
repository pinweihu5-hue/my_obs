---  
alias:  
creation_date: 2024-06-29 10:44  
tags: 
related:
- [[low_level_idx]]
- [[learn_low_level]]

---  
[(583) Win 系統舊代碼導致 CPU 干冒煙？谷歌程序員慘背鍋 | 谷歌 | 微軟 | Chrome | 負優化 | 內存 | 系統 | Windows | 程序員 | CPU - YouTube](https://www.youtube.com/watch?v=9RjZxB1M1P0&list=WL&index=17)


summary:
api doc is not noted on how to use this api
and chrome dev use this api
this api only use 32 mib as cache (in currently widespread 64 bit machine)
and then use chorm kick off its dl bg process, this cache will quickly fill up and always throw page fault err (can not find in current vir memory)
the freq of this error cause cpu hang!




- 64GB vs 64GiB
	- GiB 用 binary calculate
	- 64 GB us decimal
	- so 64 GB is only 
	- memory need to strcitly talk in G"i"B term since it talk to CPU, which is all abt binary op
	- disk no need to do that, so use lousy term
- window working set have a API which will use 32 mib working set in 64 gb machine
	- and api doc not mention this
	- google chrom background update use this API
	- and this api, use too samll 32 mib working set to do work (in 64 gb mahcine)
	- and also hit page fault (since can not fund data in real mem and need to go virtual mem fund, which trigger this page fault) thousand times and this detour cause CPU run and CPU become buttonneck



PS:

GB（Gigabyte，千兆字節）
定義：GB 是基於十進位制（decimal）的單位。
換算：1 GB = 1,000,000,000 字節（bytes）（**10^9** 字節）。
使用場景：硬體製造商常用的計算方法。
例子：一個標稱容量為 64GB 的硬碟實際上是 64,000,000,000 字節。

GiB（Gibibyte，吉比字節）
定義：GiB 是基於二進位制（binary）的單位。
換算：1 GiB = 1,073,741,824 字節（**2^30** 字節）。
使用場景：通常用於操作系統、記憶體（RAM）、固態硬碟（SSD）等領域。這是計算機科學家和操作系統常用的計算方法。
例子：**一個標稱容量為 64GiB 的記憶體實際上是 64 x 1,073,741,824 = 68,719,476,736 字節**。

在實際應用中，這種差異可能會導致用戶的困惑。例如：
當你購買標稱容量為 64GB 的硬碟時，操作系統可能會顯示容量為約 59.6GiB。
當你購買標稱容量為 64GiB 的記憶體時，操作系統會顯示容量為 64GiB，與標稱容量一致。


換算 GB to GiB can use 
GiB = byte in GB/2^30
so,  64000000000/(2^30) 約等於  59.604644775390625 GiB