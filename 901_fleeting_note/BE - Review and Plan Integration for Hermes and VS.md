[[_wemo_tickets]]

---

# Doc


- [ ] open card
- [ ] link to figma


# goal

- plan 下一階段我們要在 vehcile svc and price svc 的 ticket 和進一步如何和 hermes 的現有價格功能進行 migrate
	- scope is limit to VS 和 PS 的交互而已
		- 先切開是是否可以租借的 tag, zone 等其他 tag
		- 先切開 rentour那邊
		- 先處理 標價樣式 的 tag
- 盤點目前 vehicle svc 功能(VS)
	- 是否可以租借 and 樣式與標價
- 盤點目前 pricing svc 功能(PS)
	- 租車標價顯示 / 預約金額計算 / 還車結算
- 盤點目前 hermes 計價和設定價格的功能
- mapping 出 hermes 功能和目前 vehicle / pricing svc 的功能
	- hermes call VS -> get all active tags
	- hermes call PS -> use tags to get price info
- 開出 ticket



# Question

there are some original tickets we don't know what's the purpose?
- [Feature] Vehicle service -move sensitive data to env
- BE - [Core Pricing] 12- Migrate Scooter create order logical


# meeting min

- can be in hermes or indi micro service
- say we put into hermes
  - hermes call vehicle_svc -> get tag array -> hermes call price_svc -> get all price info -> return to users
    - say the tag only have normal and wemo\_員工
  - how and when to handle filter

![[IMG-BE - Review and Plan Integration for Hermes and VS-20241003104933698.png]]






- when exteral make grpc call, price
![[IMG-BE - Review and Plan Integration for Hermes and VS-20241003104948758.png]]



- [ ] to plan:
	- [ ] apollo -> proto or no proto is fine, but make spec more clear
	- [ ] create task



design note:
- redis cache, record ts for scooter/:id -> also need to ask app side not to reload every round to save traffic
- reserve need to record p_id, style_id and ts into rentConfig table









![[IMG-BE - Review and Plan Integration for Hermes and VS-20241003105000713.png]]