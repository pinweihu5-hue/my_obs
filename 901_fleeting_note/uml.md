also can see [notion note](https://www.notion.so/nture4388/UML-Note-a2ca6cd21b9b497b8e3233cc696349ed?pvs=4)


- a <--- b          
	- 依賴關係， a 被 b 使用
- a 三角 ---   b    
	- impl， a 是 interface, b 是 實作
- a 三角 __ b   
	- 繼承, 箭頭,a 那邊是 parent
- a 菱形  __ b        
	- 注入, b 注入 a


# 例子, spring security 
![[IMG-uml-20250805201304171.png]]


---

PS
這個關係也是一個很常見的
就是 interface 依照 邏輯關係一層被一層使用
類似 userDetailsvc, 使用 userDetail, 然後 ueserDetail 會使用 grantedAuthority
這邊是抽象關係的分解

然後每一個 interface 也可以被實作出來 (像是 SimpleGrantedAuthority impl GrantedAuthority)
或是說需要被也可以先被 extend 出來 (賦予更多功能), 再被實作 (like UserDetailsService exntend 出來  UserDetailsManager and then 實作出 InMemoryUserDetailsManager)


---



這個 UML 圖展示了 Spring Security 中與使用者資訊和權限相關的核心介面和類別之間的關係。它描繪了一個簡化的使用者認證和授權模型。

**核心元素解釋：**
* **`GrantedAuthority` (介面):**  代表一個權限。一個使用者可以擁有多個權限。
    * `getAuthority(): String`: 返回權限的字串表示形式。
    * **`SimpleGrantedAuthority` (內建類別):** `GrantedAuthority` 介面的一個簡單實現，通常用於表示單個權限字串，例如 "ROLE_USER" 或 "PERMISSION_READ"。

* **`UserDetails` (介面):** 代表一個使用者，包含使用者的認證和授權資訊。
    * `getUsername(): String`: 返回使用者的名稱。
    * `getPassword(): String`: 返回使用者的密碼。
    * `getAuthorities(): Collection<? extends GrantedAuthority>`: 返回使用者所擁有的權限集合。
    * **`User` (內建類別):** `UserDetails` 介面的一個常見實現。通常在應用程式中，您會使用自己的使用者類別來實現 `UserDetails` 介面，而不是直接使用 `User` 類別。

* **`UserDetailsService` (介面):**  負責從資料來源載入使用者資訊。
    * `loadUserByUsername(username: String): UserDetails`: 根據使用者名稱載入 `UserDetails` 物件。
    * **`UserDetailsServiceImpl` (自定義類別):**  這是您需要自行實現的類別，用於從資料庫或其他資料來源載入使用者資訊。圖中的「(自定義)」表示這是應用程式開發者需要自行編寫的程式碼。

* **`UserDetailsManager` (介面):** 擴展了 `UserDetailsService` 介面，提供更豐富的使用者管理功能，例如創建、更新和刪除使用者。
    * **`InMemoryUserDetailsManager` (內建類別):**  `UserDetailsManager` 介面的一個簡單實現，將使用者資訊儲存在記憶體中。  適用於測試或簡單的應用程式。
        * `users: Map<String, MutableUserDetails>`:  使用 `Map` 儲存使用者資訊，key 為使用者名稱，value 為 `MutableUserDetails` 物件。

* **`MutableUserDetails` (介面):** 擴展了 `UserDetails` 介面，允許修改使用者資訊，例如密碼和權限。  `InMemoryUserDetailsManager` 使用這個介面來管理使用者。


**流程說明：**
當使用者嘗試登入時，Spring Security 會使用 `UserDetailsService` 根據使用者名稱載入 `UserDetails` 物件。然後，它會比較使用者輸入的密碼和 `UserDetails` 物件中儲存的密碼。如果密碼匹配，Spring Security 會使用 `UserDetails` 物件中的權限資訊來授權使用者訪問受保護的資源。



# 例子
```java

public class UserIdentity {
    private static final MemberUserDetails ANONYMOUS_USER = new MemberUserDetails(new Member());

    private MemberUserDetails getMemberUserDetails() {

        // 下面這邊的 SecurityContextHolder.getContext().getAuthentication().getPrincipal();
        // 就是三層 interface 的概念，分別都有對應的實作
        var principal = SecurityContextHolder.getContext()
                .getAuthentication()
                .getPrincipal();

        return "anonymousUser".equals(principal)
                ? ANONYMOUS_USER
                : (MemberUserDetails) principal; // 最後 cast 為 MemberUserDetails
    }
}

```

![[IMG-uml-20250805201304458.png]]

SecurityContextHolder 是一個 class
依序注入 SecurityContextHolderStrategy, SecurityContext and Authentication
每一層 interface 都可以有各種不同的實作


譬如 SecurityContextHolderStrategy的不同實作
![[IMG-uml-20250805201304875.png]]

SecurityContext的不同實作
![[IMG-uml-20250805201305696.png]]

Authentication的不同實作
![[IMG-uml-20250805201305851.png]]