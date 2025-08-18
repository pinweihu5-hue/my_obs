

- [[小時租里程費退款和調整訂單#thoughts_on_code|mileage_feat]]
	- refactor lead to sys 問題
		- refactor 的地方是小地方，因此不太在意 -> 錯在 R.isEmpty(a1) 寫成  R.isEmpty(a1.length)
		- 這樣 lead to a1 有沒有值都會是 false -> 後面邏輯會錯誤
		- 你 refactor 的地方不是我改的核心地方，因此會導致那邊我沒有測試的很細，而且那邊的邏輯也怪怪的（不是我寫的，也不想要調整既有行為），怪怪的在於會去 catch and return `[]`
