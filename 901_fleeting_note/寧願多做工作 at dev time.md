
- 不想要 prod error 發生時的那種羞恥感和遇到逼迫你權衡work life balance的時刻
- 你要review 不夠確實然後導致需要加班處理prod issue嗎？
- 重點不是學習。而是把工作做好所需要的專業
- 對工程師來說，工作做的好與壞直接影響你最重視的生活品質和工作時的幸福度
- 對工程師來說，重點不是時限內把東西做完，而是要把重點放在系統要穩定，而時限調整要主動提出
- 相容性問題
- 改變 contract 要溝通到位 不清楚要問清楚 除非你想要prod error cuz of you!
- 留意到all possible clients
- 有畫面的都要確認pm有測試

- Pm 沒有測試到。prod error 是你修。不是 pm 修
- 低標：不是工最少。不是最快。不是最好實做。是最安全 （prod error 的可能最低）

- Prod 不會爆
- 寫code的方式要可以early fail
- 有backup 機制 或 rollback 機制

- 設計要有一致性 不然就分開處理

- 例子
	- Type 0 or 1 or 2
	- 只有0的 service Id  clouds be null -> 不一致
	- 2年後去建立假資料測試也忘記
	- 假資料是錯的 測試也不會測試到

 - old code with old DB schema (shall already work) 但是你改掉old code 就不 work了(除非你有做相容處理. Like. Keep old code execution path intact）
- new code with new DB schema (shall work after you write the new code and have test set), unless u misunderstood the spec
- 重點是…
    - old code with new DB schema
        - Old code 會存在是因為如果沒有要強更或是客戶端那邊有舊客戶端（in bb case ota時間導致）
        - 這樣就要做相容處理，情境很多，總之就是沒有 prod error 和 舊客戶端的規格一樣要達到
        - 類似你改pg db schema, 那舊code 要怎麼存db? (一個簡單例子，加欄位，舊的本來就沒有，那沒有存到也是合理的）
        - 如果改欄位名字，就比較麻煩了， 會避免這樣處理，這樣你斷了rollback的後路
    - new code with old DB schema，這邊是說如果你還有保留舊資料結構的情境
- 回到 BE and BB 的API context
- 回到 Kafka context
    - old/new producer ↔ old/new consumer