
# 問題
我做撈很多車的價格資料遇到的問題

# 哪裡卡關
兩個設計決策 我有點無法下決策
- 一次撈一個 or 撈多個
- 計算順序要 sql 處理還是 app 處理


# 結論

一次撈一個 or 撈多個
- josh 說一次撈一個即可
- 我擔心會把一個req 會打很多次 db?
	- 可以上 cache解，而且目前 josh 有搞cach code
- 你一次撈多個，每次req幾乎都會打，因為你的條件 where in(v1, v2, v3..) 很容易都不太一樣
- sql也更簡單



計算順序要 sql 處理還是 app 處理
- josh 觀念是 app 處理
- 程式碼簡單
	- sql簡單
	- app 處理這些排序的 code 也更好看懂，更好處理
- 資源考慮
	- 讓 app 負擔 loading
	- 因為 app 可以開 pod, ez, 可以開30個以上，還自動伸縮，但 db 只有一個，cloud sql 貴
