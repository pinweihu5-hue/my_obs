

see: [馬克的整理，寫的很棒](https://www.notion.so/nture4388/30-11-B-iT-IT-c8648ddb73c54c638ba3f79237dcd854?pvs=4)



[How does B-tree make your queries fast? · allegro.tech](https://blog.allegro.tech/2023/11/how-does-btree-make-your-queries-fast.html)
- B-tree vs Binary search tree(BST)
    - B-tree  一個 node 可以放很多 value
        - like PG, 1 page/node 的大小是  8 kb, 扣掉 meta data and ptr, 大概也還有一半
        - say 每一個資料是 8 bytes, 3 kb / 8 byte → 375, 一個 node 可以放 375 個值 (BST 只有 2 個)
        - so, 通常 one billion 只需要 depth of 4
        - this is 4 random access
	        - 跳層 or jump node 就是 random access
        - 每一個 node/page 內的讀 都是 seq read
            - disk seq read >>> disk randon access,
            - 差了百倍或千倍
            - 可以用 hdd 的轉盤和磁頭去思考
            - 甚至有時候比 mem access 快
        - BST 的話，1 bill 需要30層，this is 30 個 random access
- 上面都是 read, so let’s talk about write
    - 每一次 write 都會自平衡 → 才不會太多層




ref
- [30-11 之資料庫層的核心 - 索引結構演化論 B + 樹 - iT 邦幫忙：：一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10221111)


