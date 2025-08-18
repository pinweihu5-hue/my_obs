[[db 依賴關係 dependency]]
[[db_idx]]

---

# core idea

 foreign 也可以看成是 pointer



資料面來看：

一對多的那個 crow foot 表示
table A 的一筆資料 is related to 多筆資料 in Table B

![[IMG-db fk 的 thinking, 一對多-20241003104933912.png]]

業務面來看
一個使用者會有多筆租界記錄

PS: 流程上，會先弄清楚業務面的 entity 關係再設計出 db 的資料關連
![[IMG-db fk 的 thinking, 一對多-20241003104948845.png]]


PS: 
- FK 也可以是一對一，要看你怎麼設計
- 也可以不要建立 FK,  no couple at DB level, only relate them in Applicaiton level

# more example






## how to 解讀?
![[IMG-db fk 的 thinking, 一對多-20241003105005068.png]]
 透過資料面的關係，可以去想像業務上，不同資料 entity 的關聯
support 一個 customer 有很多 order   (as client side)
support 一個 employee 有很多 order  (as sales side)



more example:
![[IMG-db fk 的 thinking, 一對多-20241003105005178.png]]
一個 scooter 有很多筆 maintenance record
一個 itemId 有很多筆 maintenance record
一個 creatorId 有很多筆 maintenance record
一個 verifiierId 有很多筆 maintenance record







## mapping 表
ManageGroupMappingOfficeAndRegionOprator
like mapping 表，把 regionalOperator, ManagerGroup and officeId 三個表連在一起

office_location, managerGroup and RegionOprator 的 一個 record 會關聯到 ManageGroupMappingOfficeAndRegionOprator 表的多筆 record
![[IMG-db fk 的 thinking, 一對多-20241003105026958.webp]]




## user and city
一個 city 有很多 user
user 多 <-> 1 City
![[IMG-db fk 的 thinking, 一對多-20241003105027132.png]]

user table

| id(pk) | city |
| ------ | ---- |
| 1      | 1    |
| 2      | 1    |
| 3      | 2    |



City table

| id(pk) | name   |
| ------ | ------ |
| 1      | Taipei |
| 2      | Tainan |

```ts

@Entity()
export class User {

@ManyToOne(() => City)          // ManyToOne on FK side, 也可以是 OneToOne
@JoinColumn({ name: 'id' })     // JoinColumn  -> FK
city: City;


```



PS:
dbear 沒有告訴你一對一還是多對一
只有看到你 FK 在哪一邊
![[CleanShot 2024-11-01 at 21.22.45.png]]
