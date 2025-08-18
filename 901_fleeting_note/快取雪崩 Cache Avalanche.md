[[cache_idx]]

若 Key 全部都設同一個 Expire time，則當時間到會同時全部失效，此時會全部像 Database 索取資料，Database 會瞬間壓力過大，解決方法是利用一定區間的隨機值當來當作 Expire time。
