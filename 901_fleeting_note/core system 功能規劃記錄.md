---  
alias:  
creation_date: 2024-07-12 12:54  
tags: 
related:
- [[_wemo_tickets]]

---  

經驗：
- 如果要規劃功能，你需要從 user-case 來盤點
- 類似下面這樣，從不同 client 的 user case, 一個一個列出來
- 然後 db migration 可以切為 要做功能需要做的 db，和 release 前的兩種 db migration
- 一個 user case 出來，慢慢開卡出來
- 用線條拉出依賴項
- 過程中發現有些東可以模組化，就可以順便抽出來


---



- begin with platfrom
	- e.g. userApp, Apollo
- then, list out all user case
	- e.g.   userApp -> rent, reserve, retrun ...etc
- then think if there's any DB stuff need to be done before dev?
	- e.g. need add col? need create new table?
- then think about what need to be done in the level of code logic, util...etc? 
- and then we can split out and begin to think about in "ticket level"
- if there's cross micro service, need to list out the req and response shape and key parameter need to pass in and get from return
- think about dependecy between each ticket


[Figma core system 規劃](https://www.figma.com/board/K7vENJvYjl1Mw0mULI0YFo/BE-benhu-draft-0?node-id=655-1923&t=QjkNx93xK5RqwQQK-0)
![[IMG-core system 功能規劃記錄-20241003104933838.png]]

thanks card


thanks Josh
since I don't gain enough understanding on core and kind of getting lost on planning core next-move-tickets, appreciate Josh help and lead my/us way to layout the detail map on what we shall do next.


thanks Jack
during the core sysrtem planning, Jack provide some guideline I think it's quite insightful to help me on "how to plan"
