[[cache_idx]]

直接穿過 cache 打到 db
譬如有攻擊者，自己構造 req 去打我們的id, 想要獲得一些好處，或是探查一些資訊

- how to prevent?
    - validate cache if the key is inputed
    - rate limit
    - 當搜尋的 Key 重複率較低時：利用 Bloom filter（類似 hbase 判斷 key 否存在），若存在則去 Cache 或 Database 拿，反之則 return null。
	    - 但是引入 Bloom filter 可能會增加技術複雜度
		    - 要引入在哪一層？app,. redis db?
		    - bollm filter 的delete 也需要思考?
		    - need to rnsure bollom filer and db 資料一致? 有辦法讓他們原子性？