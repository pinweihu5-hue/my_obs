

參考
- 這個文章 [[Core] Vehicle Management - Phase 0](https://docs.google.com/document/d/1Lffmf5Xgn4r-KPYc7X1lSSzgmOvdcev81VWUDFVmPsY/edit?hl=zh-tw&tab=t.0#heading=h.igv93zj4p7cz)
- see code, `async getPricingAndRentalInfo` in VS

# 主邏輯
- tag 可以綁在車上，系統就可以控制車輛是否可以租借和套用哪一個prcingID。
	- 每一個pricingID 會進去 PS，根據使用者的身分去找到對應的 計價算法 + pricingConig參數 -> get 最後價格的結果物件
- tag 可以控制車子的價格，標示車子在那一個 營運區下的特定行為，標示車子的租界方式，標示車子的顯示style, 車子是否可以租界等等
- tag 有分階層，上面優先決定於下面, 系統  > 每一台車子 > 車種
- 規則：
	- 只要上層"資料備齊"，就優先選擇(code-wise, early ret)
		- 資料備齊: 表示有 pricingId and rentalInfo 都要設定
	- 如果上層資料沒備齊，就看下面有誰有設定, 就用
	- 如果上層資料有無法租借 -> early ret w/ 無法租借，其他都是 null
	- 如果最後價格都沒有設定 (null) -> 租借要 reset 為 "無法租借"
- 情境:
	- 類似我們可以用一個系統 tag 表示無法租界，這樣就算這台車有車種tag, 車子tag, 都會被 overwrite
	- vehicle tag 優先 -> when 2 data exist -> just use it
	- 行銷可以只有設定價格，沒有設定是否可以租借，是否可以租借透過堆疊讓車種決定 -> 只要資料最後堆疊後備齊，就是答案。
	- 如果全部都沒有設定 pricing, 那就無法租借。
	- 如果沒有任何行銷設定(可租借和價格)，那就是看車種是否有設定價格和是否可租借


# 關連的表

tag 主表
另外有兩個 子表
vehicle tag and vm_tag
![[IMG-stack calculation mechnsim 主要邏輯-20250810201324016.png]]

tag 主要透過子表 tag action 控制行為
在那個 rentFlow do what action for what tenentId and in what Zone

每一個 action 會對應到不同的properties (pricingid and styleId..etc)
類似 tagAction enum is pricing, pricingid 就一定要有 val

![[IMG-stack calculation mechnsim 主要邏輯-20250810201327978.png]]



透過 v_tag 子表，我們可以知道這個 tag 綁在那個車上
一個 tag 可以綁在很多車上嗎? 可以, 下面 tag -> v_tag 是 一對多
![[IMG-stack calculation mechnsim 主要邏輯-20250810201329031.png]]


