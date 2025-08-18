- 如果資料變動不 freq (yes, you better know the access pattern) → 類似 setting or config -> no need to join
    - even 如果資料變動真的很低，甚至可以放 cache at db 前面, 不需要一直打 db
- 那你根本沒有資料 consist 的問題
- 你就算打 scooter qat-001 拿其 model, candy, 你這兩個 query 就算，誇張點，隔了10秒，請問會變成 ione嗎? XD
- 這時候你連在一起，如果資料量很大，那就是 perf hit
- 大包資料需要過網路傳回來 VS 輕量資料小包小包過，那個快？
- 另外 join 還有 n+1 問題，一個 user 可能有 10個 event, 一個 event 有 2000 個 log, 你拿 10 個 user, 變成有 10 * 2000  *10 = 2 * 10^5 的資料，你確定你全部都要？如果你無腦用join 的話
    - 這時候是否需要考慮要分開 query, 不需要用join
- why? 你不需要保證 db 的資料一致性?
    - 如果你這兩個 query 打起來還算快，且資料不怎麼變，為何需要保證，資料都沒變的話
    - 你反而因為 query + join increase db loading → 資料量大的會，甚至會卡系統
    - 加上其他的影響，甚至會成為壓倒db的最後一跟稻草
- jack though
    - 還是要依照情境
    - 以 scooter and box and vm 的情境，分開 join 的 perf gain is 幾乎看不太出來吧
    - 這邊沒有 n+1 sql 問題（就是用 join 會拉到不需要的資料）
    - 從寫 lib, util 的角度來說，perf 很重要，分開寫
    - 但是從使用這一層，jack建議要看情境，這樣寫反而code是比較沒有整合在一起的。
    - josh 的建議比較是從整體來說，是對的，要 care, 但是這個情境卻反而會讓程式沒有是最好的情況


### join is not default
無腦 join 很容易有 perf issue
因為join 很容易就是表的 m * n query (如果你的表示一對多，如果確定是 1 對 1 就不會)

m ＊n 的不好是因為
- 現在是多 app 時代，但是都用一個 cloud db, 因此 db 資源是比較 稀缺的，我們這樣是把 loading 放在 db 上
- 資料量大，網路傳的慢
- 資料量超級大，甚至有會 socket 底層無法 handle 的 error XDD

如果資料不會一直變動，或是你 query的當下不會變動
基本上可以分開撈，不需要 join 一次撈出來