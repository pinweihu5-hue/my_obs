---


---












---


sutdy why I want to use vim but I always fallback to normal mode?
I want to use vim since it make me coding fast, but I don't want to use vim also since it make me coding slow
why?
- I didn't make vim to my muscl mem
- I suspect mouse curosr is still faster then vim motions





---

###  `OPTIMISTIC`
- **樂觀鎖**（基於 `@Version` 欄位）。
- 在交易提交時檢查版本號，若版本不同，表示該資料已被其他交易更新 → 拋出 `OptimisticLockException`。
- 適合 **讀多寫少** 的場景。
- why **樂觀鎖**不適合 **讀少寫多** 的場景?
	- 如果很多人都在寫～但是你卻常常的樂觀的認為資料沒有人修改～先改了再看看版本後
	- 那就會變成常常被樂觀鎖卡住
	- 所以**樂觀鎖**適合 **讀多寫少** 的場景

### `PESSIMISTIC_READ`
- **悲觀讀鎖**（通常對應 SQL: `SELECT ... FOR SHARE` 或 DB 特定實作）。
- 一讀就不讓其他寫可以進入了，但是其他讀可以進來
- 看看你的應用是否需要讀了就不讓寫的這種需求
- 如果寫多的情況下～會常常被讀卡住吧？

### `PESSIMISTIC_WRITE`
- **悲觀寫鎖**（通常對應 SQL: `SELECT ... FOR UPDATE`）。
- 一寫就不讓其他寫可以進入了，但是其他讀可以進來
- 適合 **寫多讀少**，且不能容忍衝突的場景。
- 因為如果寫很多～就很容易衝突了，除非衝突沒差～不然就要用這種鎖。
