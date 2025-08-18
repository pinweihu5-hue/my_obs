[mr](https://athena.wemoscooter.com/wemo/Hermes-Api/-/merge_requests/5241/diffs#2d87f348d33d47ede9076ec3fcb6ee15ef7993b4)







desc


- Tpass 串接台北悠遊卡公司 
	- use 台北悠遊卡公司  API 取得用戶購買 Tpass 資料
- 紀錄 Tpass 用戶輸入的卡號資料
- Upgrade SOAP package version



Q:
why idx at ['gateway', 'cardNumber'])?



NDEX tpass_verify_log_query_idx ("gateway", "cardNumber")
這個 index 應該預期會用
這邊 idx 的排序跟你 query 裡面的 `async getLastByCardInfo(cardInfo: { cardNumber: string; gateway: 'TP' | 'KH' }` align?。不知道是否有影響？不知道 typeorm 組起來會是 `WHERE gateway = 'TP' AND cardNumber = '1234'`還是 `WHERE cardNumber = '1234' AND gateway = 'TP'`