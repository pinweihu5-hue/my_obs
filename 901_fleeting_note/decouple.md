解耦

- 思考降低耦合，也可以反過來想，就是要提昇正交性。
    - 讓這個功能盡量不要讓其他功能有關係，邏輯關係和抽象關係都是正交更好！不要你這邊是一個 auth, 有其他地方也是一個 auth XD
- 盡量不要增加偶然複雜度 ref [对抗软件复杂度的战争](https://mp.weixin.qq.com/s/f82GBadLcQJCiFHcGWzkCA)
- [軟體設計](https://www.notion.so/nture4388/ce6bd9d3ad1f438580e150e98b831292?pvs=4)


- 什麼時候值得多寫一層抽象? -> 跨 XX 的支援
    - ODBC就是一個例子 → client 都可以透過 ODBC 去吃不同 DB 的 連接
        - 一邊是 pg, mysql, moogo db 等等等
        - 另外一邊是 app
        - app 可以透過 ODBC 連到 各種 db
    - libuv 也是一個例子 ref: [(276) LXJS 2012 - Bert Belder - libuv - YouTube](https://www.youtube.com/watch?v=nGn60vDSxQ4)
        - livuv 的功能就是非同步IO 的多平台轉換
        - 可以透過 libuv去接上不同平台的 非同步 I/O 實作
