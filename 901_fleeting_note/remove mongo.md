---  
alias:  
creation_date: 2024-05-28 11:03  
tags: 
related: [[_wemo_tickets]], [[租還車流程 api, rent, return process]]
---  


- [x] 確認還有人有 mongo 嗎？
- [ ] isActive is false 就是無法買嗎？
- [ ] 確認 app user history order 的影響



ask josh
- [ ] 需要把 tp 的 id 8 remove 掉嗎？
- [ ] 需要把 tpd 中的 mongo remove 嗎？
- [ ] 需要把 tpdh 中的mongo remove 嗎?



預約那邊有確認 tpd 是否存在
api/v2/users/service/rents/reserve.ts:308


租車patch 會 checkMengoPoints
api/v2/users/service/rents/rent.ts:192





tp
![[IMG-remove mongo-20241003104934043.png]]


![[IMG-remove mongo-20241003104948927.png]]



event config
type = 'subscription' and "isActive" = true and "endAt" > now()


![[IMG-remove mongo-20241003105010493.png]]

