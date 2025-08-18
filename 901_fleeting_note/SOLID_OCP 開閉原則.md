open closed principle

體現在 GOF 設計模式上

# 預測變化點，封裝變化

- 妥善預測未來會發生變化的點去建立擴展點，因此發生變化後可以在擴展點上去增加新功能 or 盡量可以不用動的無關地方的code
- 例子：不要吧自己的程式依賴第三方軟體，要依賴自己建立的 “不太會變動” or “共用“ 的介面。
    - 客戶端的程式，只會 couple to “不太會變動” or “共用“ 的介面
    - 如果你有調整東西，或是新增東西，客戶端的程式不會需要改動
    - 例子：策略模式等等
    - 例子 see → /Users/re4388/project/personal/nodets/ts-playground-0/src/design_pattern

wemo promotion, 只 coule IPromotion, 我加了很多 promotion, 這段 code 都不會動到
![[IMG-SOLID_OCP 開閉原則-20250807202920975.png]]
also see:
/Users/re4388/project/personal/nodets/ts-playground-0/src/design_pattern/chainofResponsibilityV1.ts


配適器模式， client 依賴 穩定的抽象類
see: /Users/re4388/project/personal/nodets/ts-playground-0/src/design_pattern/Adapter0.ts

策略模式
- 結帳 method 可以吃各種 payment
- 可擴充各種 payment, like creditCard, LinePay, ApplePay…etc
- 要定義所有 payment 都穩定的介面 (OCP)
- see: /Users/re4388/project/personal/nodets/ts-playground-0/src/design_pattern/strategy1.ts


![[IMG-SOLID_OCP 開閉原則-20250807202922069.png|839]]

![[IMG-SOLID_OCP 開閉原則-20250807202923392.png|726]]


# 擴充點怎麼想？

- 要先想 force, 會怎麼變化? 會需要什麼新功能?
    - 譬如未來可能有其他付款方式
    - 譬如未來可能會有其他貨幣別
    - 其他車種?
    - 其他引擎?
    - 其他人會用這個功能？因此要模組化出來讓人家擴充?
- 如何達到？
    - 那個會變化的地方，要倚賴一個穩定的東西
        - 變化的地方實作那個穩定的東西
        - 如果有其他可能的變化，就另外實作一個
        - 例子：策略模式，state 模式， chain of command 模式, facade, adaptor (外接第三方接口)
    - 建立型的，動態產生（上面也都算是動態, like stgy），控制產生模式
        - factory pattern → runtime 才可以判斷再去建立出需要的類別
        - builder pattern → 依照一定模式去建造
        - task pattern → 需要時再跑起來
    - 不影響code架構下去增強功能，改變功能
        - proxy, decorator
    - 簡化特定邏輯
        - iterator for 特定結構上的移動
        - composition(組合) for tree
        - middleware 前處理後處理的簡單擴充, ex: Express.js
    - 分層
        - 乾淨架構四層: follow 分層原則、相依性原則、跨層原則 → 這樣不同層間就不會互相耦合


# Program to an interface by teddy

- 物件導向技術學得好的人就知道要符合 **program to an interface** 的精神來開出條件給公關公司找人。
    - 有酷似「豆花妹」甜美的笑容，媲美「遙遙」殺很大的身材，外加「林志玲」的娃娃音。
    - 這樣子應該很容易可以找到一票符合條件的 show girls。反之，如果 是 **program to an implementation**，就會變成: 我要「豆花妹」，「遙遙」，外加「林志玲」。都已經「指名購買」了，再怎麼找全台灣也就是這三個人。萬一當天有人臨時生病，可是連個替代人選都沒有
    - 因此，program to an interface 的好處就很明顯了:
        - 不管黑貓，白貓，只要能抓老鼠的(符合 interface)就是好貓。 也就是說，可選擇性變多了，不會被綁死在某一個人或是品牌 上面。
        - 有一天發現世界上有一種比黑貓和白貓更厲害的「熊貓」，只要 符合介面直接換掉程式都不用改。


- **Thinking about what you can do rather than who you are**


## more example
- [The problem with switch statements - Uncle Bob - YouTube](https://www.youtube.com/watch?v=2IotTzClOAQ&t=2s)
	- 如果很多地方都有 switch statement... like u have a shape, circle, triangle, rectangle.. do different thing
	- 你如果要新增一個 shape, 你要去很多地方改
	- 你可以不要用 switch, 你用多態 -> 這樣你要新增 shape, 只需要去 extend a new class 就可以，不需要去很多原本的地方改
