

- rr 太天真的，沒有考慮不同伺服器效能的變異和req的變異
- 動態 weighted RR 不錯
    - 會動態判斷平均延遲然後依照這個判斷每一輪要丟幾個
    - 缺點是比較複雜
- 一個簡單又有類似效果的是 least connection (將請求發送到連接數量最少的伺服器)
    - 還在 connection上表示 server 還在工作
    - 更有效地利用伺服器資源，避免某些伺服器過載而其他伺服器閒置的情況
- least connection  or 動態 RR?
    - least connection 更好的處理過載
        - 因為 least connection 就是根據是否還有人工作與否去決定發送的
    - 動態 RR 更好的 95 and 99 latency
        - 因為 動態 RR 就是根據 延遲決定發送 req 的
    - 看你的用例
- 衡量指標
    - 降低/ 500 drop req
    - 低延遲 latency(50,95,99)



ref: [Load Balancing](https://samwho.dev/load-balancing/)


