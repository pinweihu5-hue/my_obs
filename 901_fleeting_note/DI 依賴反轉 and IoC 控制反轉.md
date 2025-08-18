related:
- [[SOLID_DIP 依賴反轉]]

# how to DI

1. 封裝變動之處 Encapsulate what varies
2. 萃取共同行為 Abstract common behavior
3. 委派/複合 Delegation / Composition


例子
- 封裝變動之處
	- 三種變化
	- 付款週期: 周、雙週、月 -> PaymentSchedule
	- 付款方式: mail, bank and hold -> PaymentMethod
	- 付款類別：薪水、拿commisson的、小時工 -> PaymentClassification
- 萃取共同行為
	- 就是找出 baseClass 裡面的共有 fileld, methods
	- 類似 PaymentMethod 裡面會有 pay
- 委派/複合(composition)
	- 用 composition 的方式去注入到 Employee 去使用
	- 或是換個角度說: Employee 委派這三個 class 去做事情
![[IMG-DI 依賴反轉 and IoC 控制反轉-20250807202917210.png]]





# DI 優點

- 好抽換其他實作
- 好擴展
- 並行開發互相不影響
- 好維護 → 達到 SRP
- 好測試





# DI 缺點

- 因為沒有直接呼叫，因此需要仰賴 IDE 對於實作和抽象的幫助跳轉
- 會需要花時間思考是否需要額外的介面，介面要如何設計，介面這部份的邏輯也是額外的程式（因為直接耦合就直接呼叫下去）
    - 因為不是所有的東西都要解耦，只有遇到不穩定/預期變化的依賴才需要。




# 其他



## DI 跟 IoC (控制反轉)的關係

在軟體設計中，依賴注入（Dependency Injection，DI）和控制反轉（Inversion of Control，IoC）是兩個密切相關但又有所區別的概念。IoC 是一種更廣泛的設計原則，而 DI 是一種實現 IoC 的具體機制。可以這麼理解：**DI 是 IoC 的一種特定形式。**

**控制反轉 (IoC):**

IoC 的核心思想是將控制權從應用程式程式碼轉移到框架或容器。在傳統的程式設計中，物件的創建和依賴關係的管理通常由物件本身負責。而 IoC 則將這些責任轉移給了外部容器。這意味著物件不再需要自己創建和管理依賴關係，而是由容器將依賴關係注入到物件中。

IoC 的好處包括：

* **降低耦合度：** 物件之間的依賴關係由容器管理，降低了物件之間的耦合度，使程式碼更易於維護和測試。
* **提高靈活性：** 可以通過配置容器來改變物件的依賴關係，而無需修改程式碼。
* **更好的程式碼重用性：** 物件可以更容易地在不同的上下文中重用。
* **更容易進行單元測試：** 可以通過模擬依賴關係來更容易地進行單元測試。


**依賴注入 (DI):**

DI 是一種實現 IoC 的具體技術。它通過將依賴關係作為參數傳遞給物件的建構函式、屬性或方法來實現控制反轉。DI 的三種主要形式：

* **建構函式注入：**  依賴關係通過物件的建構函式傳遞。這是最常見的 DI 形式。
* **屬性注入 (Setter 注入)：**  依賴關係通過物件的 setter 方法傳遞。
* **介面注入：**  依賴關係通過實現特定介面來傳遞。


**總結：**

IoC 是一種設計原則，它提倡將控制權從應用程式程式碼轉移到框架或容器。
DI 是一種實現 IoC 的具體技術，它通過將依賴關係作為參數傳遞給物件來實現控制反轉。
DI 是 IoC 的一種實現方式，但 IoC 不僅限於 DI，還有其他實現方式，例如服務定位器（Service Locator）模式，但 DI 是更推薦的做法，因為它能更好地降低耦合度。


### why 你叫你另外一半寶貝嗎? -> 體現DI思維
- 名字，你這樣就不可以了，面相實體
- 如果你叫他寶貝，你就是面相抽象
    - 換了你也不會叫錯XDDDD

note: 
- 這個也像是 alias 概念， cname (DNS)
- 你可以換掉 alias/cname 指向的東西，但是使用 alias/cname 的那邊(client side)不用換


## 真實世界的插頭就 -> 體現DI思維
- 插頭，你可以插”各種”電器, 都可以用!!
- DIP 里示替換原則: 介面的實作可以隨你換，但客戶端都不需要調整！
- 只要有插頭，各種新發明的電器都可以用 → OCP 開閉原則
- DI 依賴注入，把物件的建立 和 使用 這兩個邏輯，拆開。
    - 因此使用物件的人，不需要關注物件是如何建立的
- 很多軟體設計就是多一層抽象來處理這個




## Martin Fowler 的 [DIP in the Wild 的 You mean Dependency Inversion, Right?](https://martinfowler.com/articles/dipInTheWild.html#YouMeanDependencyInversionRight)

> *DI 是關於一個對像如何獲取依賴關係的。
> 
> 當外部提供依賴項時，系統正在使用 DI。 
> 
> IoC 是關於誰發起調用的。如果您的代碼發起調用，則它不是 IoC，如果容器 / 系統 / 庫回調到您提供的代碼，則它是 IoC。
> 
   另一方面，DIP 涉及從代碼發送到它所調用的對象的消息的抽象級別。可以肯定的是，將 DI 或 IoC 與 DIP 結合使用往往更具表現力、更強大且與領域一致，但它們在整體問題中涉及不同的維度或力量。 DI 是關於佈線的，IoC 是關於方向的，DIP 是關於形狀的。*




ref:
- [淺入淺出 Dependency Injection. DI 有什麼好？如何實作 DI？DI 到底是什麼？ | by Wenchin | Wenchin Rolls Around | Medium](https://medium.com/wenchin-rolls-around/%E6%B7%BA%E5%85%A5%E6%B7%BA%E5%87%BA-dependency-injection-ea672ba033ca)