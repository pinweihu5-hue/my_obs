


# 為何 redis 夠快

- 高速 cpu上可以每秒50萬次 ~100萬次
- data 存在 in-mem, us ~ ns
- 使用 c, low-level code and 有效率的資料結構來達到高效能
- 利用單線程好處
	- 通常 CPU 不是 redis 的 瓶頸, 然後 IO bound 靠 I/O multiplexing 去處理。
	- 少了以下 multi-thread 的 overhead
	    - 沒有建立和砍掉thread
	    - 不需要不同 thread 上下文切換
	    - 不需要任何鎖的機制
- 使用非阻塞 IO via 多路復用 (non-blocking, I/O Multiplexing)
	- for more, see [notion: I/O Multiplexing](https://www.notion.so/nture4388/I-O-Multiplexing-09abb445e68b4385a46359af544eda9d?pvs=4) 
- 但要小心
	- 要小心一些潛在會跑比較久的指令 `lrange` 、 `smembers` 、 `hgetall`  and Lua script → 小心 block single thread