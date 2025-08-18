---
id: f74d6be4-dff7-451e-ac35-9b91dc707d7a
---

# Vim：有效文本編輯的七個習慣 --- Vim: Seven habits of effective text editing
#Omnivore

[Read on Omnivore](https://omnivore.app/me/vim-vim-seven-habits-of-effective-text-editing-18fdb3d865c)
[Read Original](https://www.moolenaar.net/habits.html)

## Highlights

> 大多數時間都花在閱讀、檢查錯誤和尋找正確的工作位置上，而不是插入新文字或更改它。瀏覽文字的操作非常頻繁，因此您應該學習如何快速完成此操作 [⤴️](https://omnivore.app/me/vim-vim-seven-habits-of-effective-text-editing-18fdb3d865c#ab9aa8d4-65b6-4395-91c8-9a6baa77e60c)  ^ab9aa8d4

> 有數百種不同的運動命令，有些簡單，有些非常聰明 - 並且需要數週的訓練才能學習所有這些命令。好吧，你不需要這樣做；相反，要了解您的特定編輯方式是什麼，並只學習那些使您的編輯更有效的命令。 [⤴️](https://omnivore.app/me/vim-vim-seven-habits-of-effective-text-editing-18fdb3d865c#39616c7f-1ab8-4f7e-b7d9-effcedc31042)  ^39616c7f

> * While you are editing, keep an eye out for actions you repeat and/or spend quite a bit of time on.  
> 編輯時，請留意您重複和/或花費大量時間的操作。
> * Find out if there is an editor command that will do this action quicker. Read the documentation, ask a friend, or look at how others do this.  
> 查明是否有編輯器命令可以更快地執行此操作。閱讀文件、詢問朋友或看看其他人是如何做到這一點的。
> * Train using the command. Do this until your fingers type it without thinking.  
> 使用命令進行訓練。這樣做直到你的手指不假思索地打字 [⤴️](https://omnivore.app/me/vim-vim-seven-habits-of-effective-text-editing-18fdb3d865c#eb9cf38f-0a16-41d0-8050-70e2e1728493)  ^eb9cf38f

>  time...   
> 「我想完成工作，我沒有時間查看文件來查找一些新命令」。如果你這樣想，你就會陷入計算的石器時代。有 [⤴️](https://omnivore.app/me/vim-vim-seven-habits-of-effective-text-editing-18fdb3d865c#c98f5396-d26f-4c36-8920-53851e77f00c)  ^c98f5396

> Don't overdo it. If you always try to find the perfect command for every little thing you do, your mind will have no time left to think about the work you were actually doing. Just pick out those actions that take more time than necessary, and train the commands until you don't need to think about it when using them. Then you can concentrate on the text.   
> 不要做得太過分。如果你總是試圖為你所做的每件小事找到完美的命令，你的大腦將沒有時間思考你實際上正在做的工作。只需挑選出那些花費比必要時間更多的操作，並訓練命令，直到您在使用它們時不需要考慮它為止。然後你就可以專注於文字。 [⤴️](https://omnivore.app/me/vim-vim-seven-habits-of-effective-text-editing-18fdb3d865c#d780bf38-4966-47ec-b64b-1450429d6531)  ^d780bf38

> 一種快速方法是使用 `*` 命令查找該單字的下一個出現位置，然後使用 `cw` 更改該單字。然後鍵入 `n` 以查找下一個單詞，並鍵入 `.` （點）以重複 `cw` 命令。 [⤴️](https://omnivore.app/me/vim-vim-seven-habits-of-effective-text-editing-18fdb3d865c#63d0b684-d472-46d7-bb22-eafbf8360e83)  ^63d0b684

> later.  
> `.` 指令重複上次更改。在這種情況下，變更是指插入、刪除或取代文字。能夠重複這一點是一個非常強大的機制。如果您圍繞它組織編輯，許多變更將只需按一下 `.` 鍵即可。請注意在其間進行其他更改，因為它將取代您重複的更改。相反，您可能想要使用 `m` 命令標記該位置，繼續重複變更並稍後返回該位置。 [⤴️](https://omnivore.app/me/vim-vim-seven-habits-of-effective-text-editing-18fdb3d865c#ce7e85d4-8f7a-4f69-b4a5-463b456ee7b9)  ^ce7e85d4

>  [⤴️](https://omnivore.app/me/vim-vim-seven-habits-of-effective-text-editing-18fdb3d865c#9c9c53b6-42a2-4107-aac5-88b03298d7e2)  ^9c9c53b6

> 當您輸入多次短語或句子時，還有更快的方法。 Vim 有一個記錄巨集的機制。您輸入 `qa` 開始記錄到暫存器「a」。然後像往常一樣輸入命令，最後再次點擊 `q` 停止錄製。當您想要重複記錄的指令時，請輸入 `@a` 。有 26 個暫存器可用於此 [⤴️](https://omnivore.app/me/vim-vim-seven-habits-of-effective-text-editing-18fdb3d865c#b8f1b5c4-07b6-43a5-a7aa-8fb02e68e0ed)  ^b8f1b5c4

> 很多時候你會一次又一次地犯下同樣的錯誤。你的手指並沒有照你的意圖去做。這可以用縮寫來修正。幾個例子：
> 
> > :abbr Lunix Linux
> > :abbr accross across
> > :abbr hte the
> 
> The words will be automatically corrected just after you typed them.   
> 輸入後，這些單字就會自動更正。 [⤴️](https://omnivore.app/me/vim-vim-seven-habits-of-effective-text-editing-18fdb3d865c#88a98052-6fbb-4e56-8ba4-dffb18712629)  ^88a98052

> you could look around in newsgroups or on the Internet to see if somebody already solved it for you.  
> 另一方面，您不應該嘗試學習編輯器提供的每個命令。那完全是浪費時間。大多數人只需要學習 10% 到 20% 的工作命令。但這對每個人來說都是一組不同的指令。它要求你不時地向後靠，想知道是否有一些重複性的任務可以自動化。如果您只執行一次任務，並且不需要再次執行該任務，請不要嘗試優化它。 [⤴️](https://omnivore.app/me/vim-vim-seven-habits-of-effective-text-editing-18fdb3d865c#0568d76b-7ccc-40ba-beee-7b4b0b880470)  ^0568d76b

> 但您可能意識到在過去一個小時內您已經重複某件事多次。然後在文件中搜尋可以更快完成此操作的命令。或寫一個宏來實作。 當這是一項較大的任務時，例如列出特定類型的文本，您可以在新聞群組或互聯網上查找是否有人已經為您解決了該問題。 [⤴️](https://omnivore.app/me/vim-vim-seven-habits-of-effective-text-editing-18fdb3d865c#fa16fa0c-1648-4946-bf8b-a2d8396171f8)  ^fa16fa0c

> 最重要的基本步驟是最後一步。你可以想到一個重複的任務，找到一個很好的解決方案，但週末過後你就忘記了你是怎麼做的。那是行不通的。您必須重複該解決方案，直到您的手指自動完成為止。只有這樣您才能達到所需的效率。不要試圖一次學習太多東西。但同時做幾件事效果會很好。對於您不經常使用而無法掌握的技巧，您可能需要將它們寫下來以便以後查找。不管怎樣，如果你牢記目標，你就會找到讓你的編輯越來越有效的方法 [⤴️](https://omnivore.app/me/vim-vim-seven-habits-of-effective-text-editing-18fdb3d865c#adb99275-3095-4605-a10f-6268527f03a8)  ^adb99275

> 我仍然看到有人在 VDU 後面花費半天時間抬頭看著屏幕，然後向下看兩根手指，然後向上看屏幕，等等 - 並且然後想知道為什麼他們這麼累......用十個手指打字！它不僅更快，而且更不那麼令人厭煩。每天使用一小時的電腦程序，只需要幾週的時間就能學會盲打 [⤴️](https://omnivore.app/me/vim-vim-seven-habits-of-effective-text-editing-18fdb3d865c#89b7a776-1b78-48bf-a2de-fe5a4028e1a2)  ^89b7a776

