
related:
- [microservice 微服務 at notion](https://www.notion.so/nture4388/microservice-867b02cb9bc4466f9e5fa7f9702bc4e5?pvs=4)



# 設計微服務架構的步驟
1. 決定服務邊界
	- 這可能是整個過程中最重要的部分。
1. 設定通訊模式 - 設計與每個服務通訊的方法。 
	- 同步的還是非同步的?
	- 設計 API。
	- wemo
		- 目前大概都用 grpc
1. 選擇技術堆疊
	- 決定每個服務的開發平台、資料庫、快取等。 
	- 請記住，每個服務都可以與其他服務在技術上相互獨立（「分散式治理」），而且它們不應該基於相同的堆疊。
	- ex
		- zod or class validator
			- [[compare Zod and class validator]]
			- [class validator is hard to use](https://docs.google.com/document/d/1LKUd4gOKDlSAZg41zBbnwvIJkEOunezVORYPMWOmbfI/edit?tab=t.0)
			- [Zod vs Class-Validator](https://docs.google.com/document/d/1_ePYiH7Nv4nuyxO89fLKWh9HvhucHlAGc11slaKlxN4/edit?tab=t.0#heading=h.10rz6an1p0q5)
2. 設計架構 
	- 設計每個服務的內部架構，通常使用分層模式。
	- 怎麼分層
	- 就像是 at wemo, 我們有討論過要用 nexus BE or use wemo current: [[分層架構 one repo per module]]



- [Microservice Guideline in WeMo](https://docs.google.com/document/d/1hJA3Prt9AfQuN2YQ-CfA_LNGngD70_-PGYNwmtH4MYc/edit?tab=t.0#heading=h.tp60jiufhbon)
# 拆成 microservices 的優缺點?
- 優點
	- 可以根據用量，獨立決定每個 microservice 是否要 scale up/down
	- 相較於 monolithic，microservice 負責範圍小，新接觸的人較容易理解
	- 只要不改變接口不用擔心影響到其他 microservice，提升實作速度
	- 獨立 deploy, 更快的 testing time、build time、deploy time
	- 可依照不同 microservice 的運作特性，獨立選擇使用的技術
- 缺點
	- 整體測試變得困難，測試單一 endpoint 可能必須同時跑起多個 microservices 以及 databases
	- 除錯可能變得困難，每個 service 都有自己的 log，找錯誤必須一路追蹤

# 為什麼每一個 database 要只能有一個 microservice 可以 access?

- 避免多個 microservices 用到不同版本的 table schema，造成運作上的問題
- 如果強制多個 microservices 使用同一個版本的 table schema，會變成一改 schema 所有 microservices 都要更新，失去 microservice 的好處
- 大家都用同一個 microservice 去 access database 可以把一些難以做在 database 層的 constraint 或 data transformation 做在 microservice 層，確保對資料操作的一致性
    

### 怎麼拆分 microservices，哪些應該放一起，哪些應該分開？
- 參考上面 microservice 以及多個 database 的優缺點
- 關聯性太高常常會需要 join table 可能就不適合拆開
- 需要 transaction 處理可能不適合拆開
- 做一件事需要從 endpoint -> microservice A -> microservice B -> microservice C，可能是設計有問題，可以考慮把一些 microservices 合併或重新調整流程
- 
參考資料：Bounded context [https://martinfowler.com/bliki/BoundedContext.html](https://martinfowler.com/bliki/BoundedContext.html)**




---

- 不是關於人數，而是關於系統的複雜度，你可以讓複雜的系統更容易管理
- 即使從單體式架構開始，你也需要做好模組化，並考慮未來如何將其解耦成微服務




# 我應該使用微服務嗎？
- 可能不太適合
    - 全新的產品或新創公司，業務領域可能仍然會有很多變化
    - 如果業務領域發生變化，我們就需要重新設計服務邊界，這成本很高
    - 新創公司也只有少數人，但微服務會產生新的複雜性，管理部署和微服務本身的額外工作，對於新創公司來說可能不值得做這些操作性工作
- 可能適合
    - 服務邊界比較穩定
    - 在大公司中，我們有很多人力/資源，我們希望分成許多團隊
        - 每個團隊都可以獨立有效地工作，而不會互相干擾
        - 每個服務也有自己的需求，自己的部署、構建、語言、資料庫方式



# micro service 決策圖
```plantuml


start

if (系統規模小嗎？（2-3 個服務）) then (是)
  #pink:不要使用微服務;
  goto end
else (否)
  if (可以將其拆分成自主的服務嗎？) then (否)
    #pink:不要使用微服務;
    goto end
  else (是)
    if (它對效能極度敏感嗎？) then (是)
      #pink:不要使用微服務;
      goto end
    else (否)
      if (有規劃未來的更新嗎？) then (否)
        #pink:不要使用微服務;
        goto end
      else (是)
        if (它應該是一個“快速且粗糙”的系統嗎？（POC 等）) then (是)
          #pink:不要使用微服務;
          goto end
        else (否)
          #lightgreen:使用微服務;
          goto end
        endif
      endif
    endif
  endif
endif

end



```

概念
- 如果系統還很小，還不需要切，用單體就好。
- 如果不好拆，代表不適合拆出來做 microsvc
- 微服務通常會增加更多 calls, round trip, 因此如果很敏感，那或許不適合。
- 如果未來這個服務還會調整， bounded ctx 和怎麼切因此還很難說，那也不適合。
- 如果只是要做一個 POC 也不適合。



# link
- [https://martinfowler.com/microservices/](https://martinfowler.com/microservices/)
- [A pattern language for microservices](https://microservices.io/patterns/index.html)