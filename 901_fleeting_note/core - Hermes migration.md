

- rent config need to have role to 決定價格
- user_role 算是 mapping 表，map user category 和 vipPlan to a userRole
	- 兩個 fk 都是 many to one
	- 一個 userCateogoru  可以對應到多個 userRole
	- 一個 vipPlan  可以對應到多個 userRole


![[IMG-core - Hermes migration-20241003104933836.png]]


![[IMG-core - Hermes migration-20241003104948778.png]]