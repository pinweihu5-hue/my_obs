---
id: c93a7733-ad5f-40cb-9ca2-dc3f37a601f7
---

# Engineering Principles for Building Financial Systems
#Omnivore

[Read on Omnivore](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf)
[Read Original](https://substack.wasteman.codes/p/engineering-principles-and-best-practices)

## Highlights

> The three main goals of your accounting system are to be **(1) Accurate, (2) Auditable and (3) Timely.** [⤴️](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf#324d7dde-3f94-4bdd-9ff4-a82503f961ef)  ^324d7dde

> If we sell a 10 units of a product that costs $9.99, the corresponding financial records must add up to $99.90\. This seems obvious but when you are aggregating thousands (in a lot of cases millions) of transactions, simple summation or rounding errors between systems can cause material inaccuracies. [⤴️](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf#d7bf0da0-d9d2-49b4-a589-0063b8599074)  ^d7bf0da0

> Also don’t get me started on summations over floats. I learned the hard way why you should always use integers. [⤴️](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf#b689928f-13ae-4fde-87af-fa7048812a57)  ^b689928f

> The financial record also needs to be **complete**. More specifically, both the sub-ledger and the general ledger are a complete representation of all business activities that occurred at a specific time. If there is an event that occurred but is not in the financial record, than the system is not complete. Note, that this doesn’t imply eventual consistency not acceptable. You just need to know when your data will become complete, to notify stakeholders that data has settled. [⤴️](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf#3282444b-41d6-4ac1-bf18-1ee6306fe6e2)  ^3282444b

> Very related to accuracy, your financial record must be easily auditable so that stakeholders can detect errors and accurately measure performance of your business. [⤴️](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf#29b61894-e7d0-44b8-8ea2-2b7f27d0db40)  ^29b61894

> our accounting system should meet the needs of your business, and whatever **timely** means to them. [⤴️](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf#4744cd2d-34bb-443f-877b-4d31af3be94e)  ^4744cd2d

> But you would be surprised at how often I hear people arguing about which to do, when the consumer doesn’t need to see updates more than a couple times a day. Just because they asked for it doesn’t mean they need it. [⤴️](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf#1957d239-8533-4147-8b21-45b76526a794)  ^1957d239

> The three main engineering principles your accounting system should abide by are
> 
> 1. Immutability and Durability of data
> 2. Data should be represented at the smallest grain
> 3. Code should be Idempotent [⤴️](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf#8631cc1c-1e05-4067-8674-074ce2a9fab3)  ^8631cc1c

> When data is immutable, you have a record of what the state of the system was at any given time. This makes it really easy to recompute the world from previous states, because no state is every lost. [⤴️](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf#13d2dc9d-b644-45cb-b4cf-f74cab525c80)  ^13d2dc9d

> once data is stated in the financial record it cannot be deleted. Any corrections to the system must be represented as a new financial transaction. [⤴️](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf#185752a5-3bae-48d5-997f-3470d99afc30)  ^185752a5

> For example let’s say your system had a bug and accidentally reported that a service was sold for $1000, when it should have been $900\. To correct this mistake, you should first reverse the accounting entries corresponding to the mistake, and restate the accounting entry for the correct amount. [⤴️](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf#9e512492-357a-4681-87dd-6ef137e3b5fe)  ^9e512492

> If a single immutable dataset is the core source of truth for all views of that data, to correct the view all you need to do is rerun the pipeline that creates that view after fixing your data. [⤴️](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf#2f4e4316-4fd1-4224-aad4-b90490e9d66f)  ^2f4e4316

> Every financial event can only be processed once, duplicates in the financial record will cause obvious inaccuracies. For that reason, all code that produces financial records should be idempotent. [⤴️](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf#1f80107c-81fb-44af-b7e8-fd11c68d7f7a)  ^1f80107c

> **Prefer integers to represent financial amounts**. Makes arithmetic much easier. Certain decimal representations are okay, avoid floats at all cost. [⤴️](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf#24213889-439c-404c-8728-76a54c71265a)  ^24213889

> Granularity of your financial amounts should support currency conversions with minimal loss of precision [⤴️](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf#eeb3ee92-6d88-400d-b0e9-b6bd2410bd7b)  ^eeb3ee92

> Use consistent rounding methodologies [⤴️](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf#00b5d5e6-08ef-47c1-9e61-7515f7f13d28)  ^00b5d5e6

> **Delay currency conversion as long as you can**. Preemptively converting currencies can cause loss of precision. Delay currency conversions until after aggregations occur in their local currency. [⤴️](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf#ef6487d1-3fc4-414f-a06c-5de20b01e091)  ^ef6487d1

> **Use integer representations of time**. This one is a little controversial but I stand by it. There are so many libraries in different technologies that parse timestamps into objects, and they all do them differently. Avoid this headache and just use integers. Unix timestamp, or even integer based UTC datetimes work perfectly fine. [⤴️](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf#d7e4d54d-34de-4f38-9fb1-e1154081214e)  ^d7e4d54d




# 翻譯


# 建構金融系統的工程原則

#Omnivore

[於 Omnivore 閱讀](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf)  
[閱讀原文](https://substack.wasteman.codes/p/engineering-principles-and-best-practices)

## 重點

> 您的會計系統的三個主要目標是 **(1) 準確性、(2) 可審核性和 (3) 及時性**。 [⤴️](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf#324d7dde-3f94-4bdd-9ff4-a82503f961ef) ^324d7dde

> 如果我們銷售 10 件產品,每件售價 $9.99,相應的財務記錄必須合計為 $99.90。這似乎很簡單,但當您彙總成千上萬(很多情況下是數百萬)筆交易時,系統之間的簡單求和或四捨五入誤差都可能造成重大不準確。[⤴️](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf#d7bf0da0-d9d2-49b4-a589-0063b8599074) ^d7bf0da0

> 另外,別讓我開始談浮點數的求和問題。我以痛苦的方式學到了為什麼應該始終使用整數。[⤴️](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf#b689928f-13ae-4fde-87af-fa7048812a57) ^b689928f

> 財務記錄也必須是**完整**的。更具體地說,分類帳和總帳都是在特定時間發生的所有業務活動的完整表示。如果有某個事件發生但未記錄在財務記錄中,那麼該系統就不完整。請注意,這並不意味著最終一致性是不可接受的。你只需要知道什麼時候你的數據將變為完整,以通知利益相關方數據已經穩定下來。[⤴️](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf#3282444b-41d6-4ac1-bf18-1ee6306fe6e2) ^3282444b

> 與準確性密切相關的是,您的財務記錄必須易於審核,這樣利益相關方才能檢測錯誤並準確衡量您業務的績效。[⤴️](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf#29b61894-e7d0-44b8-8ea2-2b7f27d0db40) ^29b61894

> 您的會計系統應該滿足您的業務需求,無論什麼意味著**及時性**。[⤴️](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf#4744cd2d-34bb-443f-877b-4d31af3be94e) ^4744cd2d

> 但您會很驚訝地發現,我經常聽到人們爭論應該做哪一個,而消費者每天最多只需要看更新一兩次。僅僅因為他們要求了,並不意味著他們需要。[⤴️](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf#1957d239-8533-4147-8b21-45b76526a794) ^1957d239

> 您的會計系統應該遵循的三大工程原則是:
> 
> 1. 數據的不可變性和持久性
> 2. 數據應表示為最小粒度
> 3. 代碼應該是幂等的[⤴️](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf#8631cc1c-1e05-4067-8674-074ce2a9fab3) ^8631cc1c

> 當數據是不可變的時,您就可以知道系統在任何給定時間的狀態。這使得從之前的狀態重新計算整個世界變得非常容易,因為沒有任何狀態會丟失。[⤴️](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf#13d2dc9d-b644-45cb-b4cf-f74cab525c80) ^13d2dc9d

> 一旦數據記錄在財務記錄中,它就不能被刪除。對系統的任何更正都必須表示為新的財務交易。[⤴️](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf#185752a5-3bae-48d5-997f-3470d99afc30) ^185752a5

> 例如,假設您的系統出現了一個錯誤,誤報某項服務的售價為 $1000,但實際應該是 $900。要修正這個錯誤,您應該先撤銷與錯誤相對應的會計分錄,然後重新記錄正確的金額。[⤴️](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf#9e512492-357a-4681-87dd-6ef137e3b5fe) ^9e512492

> 如果一個不可變的數據集是該數據所有視圖的核心真理來源,那麼要更正視圖,您所需要做的就是在修復數據後重新運行創建該視圖的管線。[⤴️](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf#2f4e4316-4fd1-4224-aad4-b90490e9d66f) ^2f4e4316

> 每個財務事件只能被處理一次,財務記錄中的重複項會造成明顯的不準確。因此,所有產生財務記錄的代碼都應該是幂等的。[⤴️](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf#1f80107c-81fb-44af-b7e8-fd11c68d7f7a) ^1f80107c

> **優先使用整數表示財務金額**。這樣可以大大簡化運算。某些小數表示也可以,但絕對要避免使用浮點數。[⤴️](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf#24213889-439c-404c-8728-76a54c71265a) ^24213889

> 財務金額的粒度應支持貨幣兌換,並盡可能減少精度損失[⤴️](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf#eeb3ee92-6d88-400d-b0e9-b6bd2410bd7b) ^eeb3ee92

> 使用一致的四捨五入方法[⤴️](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf#00b5d5e6-08ef-47c1-9e61-7515f7f13d28) ^00b5d5e6

> **盡可能延遲貨幣兌換**。預先兌換貨幣可能會導致精度損失。在當地貨幣中進行彙總後再進行貨幣兌換。[⤴️](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf#ef6487d1-3fc4-414f-a06c-5de20b01e091) ^ef6487d1

> **使用時間的整數表示**。這一點可能有些爭議,但我堅持這樣做。在不同技術中,有很多庫都會將時間戳解析為對象,而且它們都以不同的方式進行。避免這種麻煩,直接使用整數就可以了。Unix時間戳或基於整數的UTC日期時間都可以完美地滿足需求。[⤴️](https://omnivore.app/me/engineering-principles-for-building-financial-systems-190aa513ddf#d7e4d54d-34de-4f38-9fb1-e1154081214e) ^d7e4d54d