
[(795) Software Architecture: The Hard Parts - Neal Ford - YouTube](https://www.youtube.com/watch?v=Q6RfMmMwhvM&list=WL&index=19)


# 一個微服務要怎麼切?
可以從下面幾點考慮

先看拆分的因素
- 是否不同功能, domain
- 這部份是否容易變動
- 這部份是否有不同的 operational architecture charateratic, like scake and throughput
- 這部份是否有不同的 fault tolerance 特性
- 這部份是否有不同的 access restrcition
![[IMG-Software Architecture The Hard Parts - Neal Ford-20250807202918903.png]]


看應該整合的因素
- 是否會出現 db 交易？ 是否可以放一起?
- 資料是否需要互相拿才可以？ 是否可以放一起?
- 跨服務的 work flow 不管是 指揮還是編舞，都很複雜，如果放一起呢?
![[IMG-Software Architecture The Hard Parts - Neal Ford-20250807202919785.png]]



下面講很多 service 共用一個 db 的問題
![[IMG-Software Architecture The Hard Parts - Neal Ford-20250807202920808.png]]

# 分析技巧：trade-off分析一定要加上 biz ctx

![[IMG-Software Architecture The Hard Parts - Neal Ford-20250807202921968.png]]



![[IMG-Software Architecture The Hard Parts - Neal Ford-20250807202923284.png|770]]



不是看哪個勾比較多就選那個，還要看脈絡
![[IMG-Software Architecture The Hard Parts - Neal Ford-20250807202924829.png|816]]


要考慮脈絡
我們要用很多不同的語言，因此要管理很多不同語言都共用同一個 shared lib -> 不容易
![[IMG-Software Architecture The Hard Parts - Neal Ford-20250807202926352.png|821]]




# 分析技巧：trade-off分析要結合 biz care 的 use case
![[IMG-Software Architecture The Hard Parts - Neal Ford-20250807202928746.png|613]]




![[IMG-Software Architecture The Hard Parts - Neal Ford-20250807202929863.png|694]]





列出 use case 來分析 trade off
1 -> favor 拆分不同 svc, 你只需要改一個 svc
2 -> favor 拆分不同 svc, 你只需要加一個 svc
3 -> favor 放一起，不然你有跨 svc tx 要處理
![[IMG-Software Architecture The Hard Parts - Neal Ford-20250807202930628.png]]



那個 case 至關重要?
這需要去跟 PM 討論 
btw, 用 pm 的語言，不是用架構特性去他們聊
![[IMG-Software Architecture The Hard Parts - Neal Ford-20250807202931394.png]]



# 分析技巧：要比較類似的東西
![[IMG-Software Architecture The Hard Parts - Neal Ford-20250807202932154.png|491]]



列出 solution 可以考慮列出來的 list 要 mece
![[IMG-Software Architecture The Hard Parts - Neal Ford-20250807202932946.png|522]]


message queue 是 esb 的一部分，這樣比不對
![[IMG-Software Architecture The Hard Parts - Neal Ford-20250807202933505.png|781]]


這樣比還比較合理
![[IMG-Software Architecture The Hard Parts - Neal Ford-20250807202934036.png|989]]


# 分析技巧：不要過度偏好某一個技術解法
![[IMG-Software Architecture The Hard Parts - Neal Ford-20250807202934633.png]]



我想要都用 broadcast msg 來處理 message
![[IMG-Software Architecture The Hard Parts - Neal Ford-20250807202935074.png]]


因為如果不用broadcast msg, 我如果多一個接受者，我要改很多東西
我要建立新的 contract, queue, 寫 new receiver code
![[IMG-Software Architecture The Hard Parts - Neal Ford-20250807202935520.png]]


但是如果你"只用broadcast msg" 那...那下面情況你無法處理
![[IMG-Software Architecture The Hard Parts - Neal Ford-20250807202936014.png|699]]


比較
![[IMG-Software Architecture The Hard Parts - Neal Ford-20250807202936408.png]]


哪一個重要
![[IMG-Software Architecture The Hard Parts - Neal Ford-20250807202936918.png]]


# 分析技巧：找出 bottom line 溝通
![[IMG-Software Architecture The Hard Parts - Neal Ford-20250807202937366.png]]



![[IMG-Software Architecture The Hard Parts - Neal Ford-20250807202937805.png]]


![[IMG-Software Architecture The Hard Parts - Neal Ford-20250807202938476.png]]

![[IMG-Software Architecture The Hard Parts - Neal Ford-20250807202942675.png]]




# 分析技巧：定性分析
![[IMG-Software Architecture The Hard Parts - Neal Ford-20250807202944525.png|618]]



## tx saga
saga這三個要一起看
![[IMG-Software Architecture The Hard Parts - Neal Ford-20250807202945871.png|418]]

共有2的三次方，八種可能
![[IMG-Software Architecture The Hard Parts - Neal Ford-20250807202946799.png|695]]

![[IMG-Software Architecture The Hard Parts - Neal Ford-20250807202947985.png]]

都有不同的屬性
![[IMG-Software Architecture The Hard Parts - Neal Ford-20250807202952991.png]]


![[IMG-Software Architecture The Hard Parts - Neal Ford-20250807202953829.png]]


不是指揮造成，是指揮 + atomic 一致 導致那些紅點
![[IMG-Software Architecture The Hard Parts - Neal Ford-20250807202954550.png]]




## saga 小節
- 如果本身的業務邏輯就很複雜，指揮至少可以幫你集中這些複雜性在同一個地方
- 編舞適合在你的業務邏輯本身就是分屬於不同的 部屬單元 上
![[IMG-Software Architecture The Hard Parts - Neal Ford-20250807202955195.png|685]]




