---  
alias:  
creation_date: 2024-06-11 14:38  
tags: 
related:
- [[wemo_note]]


---

![[IMG-ops_excel_對應_db-20241003104934032.png]]

- apollo 的權限和 OPS 畫面上的操作權限是分開的
  - 問題就是，如果某個人在 apollo 上可以看到，他就有權限，這也是一開始的設計
  - 缺點是，這部份要跟 IT 要，BE 這部份要自己去 DB 確認
- 權限表上的東西，當初 water 溝通是說，讓他盡量把資訊都填上去，他會去一次看到，省去多溝通的成本。
  - 這是這樣的設計，也會 excel 上的填寫沒有定死 （類似限定那個欄位只能填那幾個），也會出現填寫錯誤，需要溝通的情況，而溝通也需要 domain knowledge, 因此會造成，其他 RD 會接卡會比較難處理。
- 目前權限上的設定基本上就卡兩層
	- 第一層
		- 這個 manager 對應到那個 managerGroup 
		- 然後managerGroup 會對應到某個officeId 和 regional operator
		- mnemosyne:: ManagerGroupMappingOfficeAndRegionalOperator
	- 第二層
		- 透過 mnenyemos::config::workOrder 的 key 裡面的 officeId 下的 type, 有 維修，檢測和驗收 這三種對應到 apollo 上的操作
- 目前流程有兩種，一般和意外
  - 一般就是：進場 ---------------->  維修 -> 整備 -> 出場
  - 意外就是:  進場 -> 檢測 -> 驗收 -> 維修 -> 整備 -> 出場
  - 上面的檢測和驗收就是對應到檢測員(type1)和驗收員(type3)的權限 -> 就是對應到 mnenyemos::config::workOrder 的 key 裡面的 officeId 下的 type 裡面要放上 managerId
  - 上面的維修，對應到維修人員(type2) -> 也對應到 mnenyemos::config::workOrder 的 key 裡面的 officeId 下的 type要放上 managerId

]]

![[IMG-ops_excel_對應_db-20241003104948921.png]]

![[IMG-ops_excel_對應_db-20241003105010075.webp]]
ref: [OPS 維修人員權限異動整操作記錄 2023-06-19 - Google 文件](https://docs.google.com/document/d/1Jn1tbzwsHHHmpmFKGrwZ0IHypsxg6PJu8bz2-e4jCAI/edit)
