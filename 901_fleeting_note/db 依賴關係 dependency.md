
[[db_idx]]
[[db fk 的 thinking, 一對多]]



==
heuristic:


- fk那邊是子表
- fk那邊可以砍且不會有side effect
- fk那邊可以砍是因為他表內有 ref 到其他表，但如果沒有表 refer 到他，他可以自己砍掉不影響他表
- fk 本身就是表示，他那個欄位是依賴於其他表才可以知道更多事情
- 因此fk本身就是一個對他人的依賴

==





資料庫的關係
有依賴關係
依賴關係是建立在一個表要倚賴另一個表的存在


類似下面的訂單表
裡面有客戶標號
因此訂單依賴於客戶存在
![[IMG-db 依賴關係 dependency-20241101212435249.png]]

另一種用語就是主表和子表
主表是客戶
子表是訂單

牽涉到砍表的話
因為依賴關係
砍子表不會有 side effect
砍主表，因為子表依賴主表，那就需要特殊處理

根據上面的例子
你砍掉訂單表，客戶表沒差
但是你如果砍掉客戶表，那訂單表裡面的客戶編號就找不到了





更多例子



你可以砍 mongo and no side effect?  YES
why?

mengo 依賴 pointOders
因為mengo裡面有 pointId 需要去查 pointOders

但是你不可以先砍 pointOders, 你砍了
Mongo 就查不到了


這邊要砍的順序就是

mengo/PointDetail -> PointOrders 
![[IMG-db 依賴關係 dependency-20241101212435738.png]]




另外有時候 db 的圖也可以用箭頭來畫，這就顯示了依賴關係
ref: https://mystery.knightlab.com/

![[IMG-db 依賴關係 dependency-20241101212435862.png]]