
- 產 promotion 券會發生 撞到 pg prepare statement 6 萬個 slot 上限
	- code level
	- 一個 chunk 是 5000筆 user
	- 然後 insert 的表 join 後共有 1x 個 col
	- 一個user 發兩個券就1萬了
	- 1萬 * 1x 個 col -> 就撞到了
- 解法:
	- 在 code level 就把東西都展開，然後再 chunk
