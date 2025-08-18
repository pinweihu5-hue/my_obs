
# 大方向
planning phrase
- 準備 migration plan, script
	- 通常就是 sql statement
	- 可以先上的就先上
- 準備測試內容
- 確定執行時間
- 判斷是否需要蓋版


staging/qat 測試
- 執行 migration plan on staging env
- integration test on staging env
- 如果有 perf 疑慮，且不會蓋版，需要先在 backup_data 測試 sql 速度


生產環境
- 先跑 migration plan on prod 可以先上的部份
	- 類似一些 config 可以先上，然後執行時只需要把 isActive trun to true
	- 類似不影響運營的可以慢慢 migrate

執行 phrase
- 執行 migration plan on prod
- 要回補時間落差上的資料
	- 類似星期1準備資料，但是星期2執行，這兩天內是否又有新資料
- 測試


# example

- [BE 小時租加收里程費用 Migration](https://docs.google.com/document/d/1kbqtBhSf631pqIbpfMnuIJZIb37IhD-FPk69r6K0O5c/edit?tab=t.0#heading=h.nt9jkcq3o06t)
	- 建立新方案，然後再用時間去切
	- 包括 data backup
	- 撈出資料需要手動矯正的
- [VS release plan 2024 Oct](https://docs.google.com/document/d/1_LfCN64fv20Am025oxuxKq3SZz2-LAcF8hGQiSaILsY/edit?tab=t.0)
- [註冊流程優化 Registration 2023 Release Plan](https://docs.google.com/document/d/1IsAvWvHU6K3r8xX8ymc5Wxbxjl-de2SQAqnIM7UKzho/edit?tab=t.0#heading=h.7o9yhi7n9p8n)
- [[wm-1078-core pricing migration]]




# migration 技巧

欄位的設計就已經會 order by 拿最新的 isActive = true
因此要上新的 record 只要新增就可以 and isActive = true

release 前，可以先把資料送入prod了，isActive = false,
跟 PM 約定好時間，時間到，把 isActive 改為true 即可



# 其他
- [蓋版系統上去後的服務情況](https://docs.google.com/document/d/1rpUxXI8-b09EcNiRkxCBJdoNVssY73G6POXTced7nO4/edit?tab=t.0#heading=h.wep7aap6h5ai)
