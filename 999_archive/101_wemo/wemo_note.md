- [[ceres]]
- [[item 權限, 觸(Accessible)和視(visible)]]
- [[處理 genPaywaysForOrderItems]]
- [[ops_excel_對應_db]]
- [[租還車流程 api, rent, return process]]
- [[wemo 登入流程]]
- [[potential perf issue when get details pricing group]]
- [[wemo service app login ]]
- [[wemo pass lastPayAt and nextChargeAt]]






# other 

# Promotion Code Next Gen 巨量序號處理 2024 08 22
[- YouTube](https://www.youtube.com/watch?v=9TVePd5l6Ic)
- 以前 2 million, only 0.5 % 會被 use -> 一堆資料，只有少量data team 需要，但 backup process 卻全部都要 dump, 不合理
- 這邊的關鍵是，以前透過 db 存這些million level的資料，現在透過算法來解決


# pricing UI sync 2024 08 23
[YouTube](https://www.youtube.com/watch?v=AGFaauVZ6e8)



# issue retro
- bb 開機會打一個 batteries API, 裡面會做很多事情，包括算 towyaing 的 hash, 因此裡面就涉及會去 reasd towyaing 的 data
那個拖吊廠的 zone 是 text, 因此我們塞一個錯誤的 josonb格式進去，一樣吃，但是跑起來會錯
- 一開始最好設定 josonb，不要用 text
- 塞進去前，可以用 {...}::jsonb 來讓 db editor 幫你確認 {} 裡面是否是合法的jsonb
- ![[IMG-wemo_note-20250810201310937.png|1196]]

沒有去測試相關的 api 是否可以正常運作 (類似 get 拖吊廠)


# zod vs class
[docs.google.com/document/d/18-WR8dwjVisSt0eOjfyubQTaOBCJMLl\_NvMDgVpWwWs/edit](https://docs.google.com/document/d/18-WR8dwjVisSt0eOjfyubQTaOBCJMLl_NvMDgVpWwWs/edit)

