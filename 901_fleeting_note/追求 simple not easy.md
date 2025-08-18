- 我們不要追求 easy, 我們要追求 simple
	- easy: **詞源：** 來自法語，據推測源自拉丁語，意為「靠近」
	    - easy → （容易獲取、安裝）、概念上的接近（熟悉、在現有知識範圍內）或能力上的接近（在自身技能範圍內）
	    - 要小心過度沉迷於easy，這損害了軟體品質。這種對熟悉性的追求阻礙了學習新事物（可以有更好軟體品質）
	- simple 來自拉丁語 "sim" 和 "plex"，意為「一折」或「一編」。引申為不交織、不纏繞。
- Hickey 引入了「complecting」這個詞（一個古老的詞，意為「編織在一起、纏繞」），用來描述軟體中不同概念和部分之間的**不必要且有害的交織**，這正是複雜性的來源。
    - 當事物纏繞在一起時，我們無法將它們孤立地思考。我們的思維能力有限，複雜性會以組合爆炸的方式增加我們需要同時考慮的事物數量，從而限制我們理解系統的能力
    - 理解是進行變更的前提。如果我們無法對程式進行推理，就無法做出關於變更影響的決策
    - 測試和重構工具不足以解決因複雜性引起的變更困難
    - 複雜的系統更難以推理，導致更容易產生錯誤，並且難以調試
    - 儘管追求「容易/easy」可能在短期內帶來速度（「短程賽跑」），但複雜性最終會在長期內導致開發速度下降，甚至停滯
- immutable state over mutable state
    - why? complects value and time
- use 多態 over switch/pattern matching
    - why? complects what and how
- use streamAPI(jave) over for-loop and reduce
    - why? complects what and how
- use rules over if-else
    - why? 將規則與程式結構纏繞在一起
- syntax 本身如果可以拆出data, do it (ex: IaaS, yaml, json)
    - why? complects meaning and order
- use VO instead of Object
    - why? 將狀態、身份和值纏繞在一起 (complects state, identity, value)
- don’t tangle domain logic into ORM(entity)
    - why? 將領域邏輯與數據表示纏繞
- use pure fn instead of methods w/ state
    - why? complects function and state
- prefer immutable var instead of mutating var
    - why?  將值與時間纏繞在一起
- use compose instead of inheritance
    - why? complects 2 class
- use MQ instead of Actor
    - why? complects who and what
    - 隊列是將「何時」和「何地」決策與調用者分開的有力工具
- pursuit consistentcy instead of Inconsistency
    - why? inconsistent make we hard to think them together


**五、 如何構建簡單的系統**

Hickey 提出了構建簡單系統的幾個策略：
- **選擇具有簡單工件的構造：** 從源頭避免複雜性。選擇那些設計上就傾向於簡單性的工具和語言特性（例如，默認不可變性、好的值類型、獨立的命名空間、支持數據即數據）。
- **抽象以促進簡單性：區分抽象與隱藏：** 抽象是「抽離事物的物理本質」，而不是簡單地隱藏細節。
- **問「誰、什麼、何時、何地、為何、如何」：** 5W1H 分析問題的不同維度，幫助將事物分開。
- **「我不想知道」原則：** 盡量讓組件只知道完成其任務所需的最小信息。
- **小的接口/規範：** 將抽象定義為小的函數集合或規範集合。避免大型的、因語言限制而被迫捆綁的接口。
- **使用值和其他抽象定義接口：** 接口的定義不應依賴具體實現。
- **嚴格分離「什麼」與「如何」：** 這是將實現細節委託給他人（如數據庫引擎、邏輯引擎）的關鍵。
- **使用組件注入：** 避免硬編碼子組件，將它們作為參數傳入，增加靈活性。
- **更多、更小的組件：** 簡單性不等於組件少，而是組件之間沒有纏繞。擁有更多獨立的、不纏繞的組件更好。
- **嚴格避免時間和位置的 complecting：使用隊列 (Queues)：** 隊列是將「何時」和「何地」決策與調用者分開的有力工具（「如果你不廣泛使用隊列，你應該立刻開始」）。
- **將策略和規則外部化：** 將策略和業務規則從程式結構中分離出來，使用聲明式系統或規則系統來表達，使之更易於理解和修改。
- **將數據視為數據：** 避免用對象模型化純粹的信息。使用基本的數據結構（map、set、sequence），以便能夠應用通用的數據處理函數。對象適用於建模具有狀態和行為的設備，而不是純粹的信息。
    - ex
        這句話的核心思想是，在程式設計中，我們應該區分兩種不同的東西：
        1. **純粹的資訊 (Pure Information)：** 這些是描述性的數據，它們本身不「做」任何事情，也沒有複雜的內部狀態變化邏輯。例如，一個人的姓名、年齡、地址；一個產品的價格、描述；一個設定值；一個日誌條目。這些是**事實或描述**。
        2. **具有狀態和行為的實體 (Entities with State and Behavior)：** 這些是系統中具有生命週期、內部狀態，並且通過方法來管理這些狀態變化或執行特定動作的「活的」單元。例如，一個銀行帳戶（有餘額狀態，可以存款/提款）、一個印表機（有墨水狀態，可以列印）、一個遊戲角色（有生命值、位置狀態，可以移動/攻擊）。這些是**執行者或設備**。
        這句話認為，我們應該：
        - 對於**純粹的資訊**，使用**基本的數據結構**（如 Map/字典、List/序列、Set/集合）來表示。
        - 對於**具有狀態和行為的實體**，使用**物件 (Object)** 來模型化。
        
        **為什麼要這樣做？**
        問題在於，當我們用**物件**來模型化**純粹的資訊**時，我們常常會引入物件本身帶來的複雜性：
        - **身份 (Identity)：** 每個物件在記憶體中都有一個獨特的身份。但對於純粹的資訊，我們通常只關心它的**值**。例如，兩個包含相同姓名、年齡、地址的「人」物件，它們代表的是同一個「人」這個資訊，但它們是兩個不同的物件實例（身份不同）。這會導致比較和管理上的混淆（需要區分 == 和 `equals()`）。
        - **可變狀態 (Mutable State)：** 傳統物件的屬性通常是可變的。修改物件的屬性會改變同一個物件的狀態。對於純粹的資訊，我們可能更關心它的不可變性，或者將變化視為產生一個新的資訊版本，而不是修改舊的。
        - **方法與狀態纏繞：** 即使是表示資訊的物件，有時也會被加入一些簡單的方法（如格式化輸出）。但這些方法與物件的數據緊密綁定。
        
        將純粹的資訊用基本的數據結構表示的好處是：
        - **專注於值：** Map、List 等數據結構的核心就是它們包含的**值**。比較兩個 Map 或 List 通常是比較它們的內容（值）。
        - **不可變性更自然：** 在許多語言中，Map、List 可以很容易地創建為不可變的。表示變化就是創建一個新的數據結構。
        - **通用性：** 許多程式語言提供了強大且通用的函數，可以直接操作 Map、List、Set 等數據結構，例如過濾、映射、摺疊、排序、分組等。如果您的數據是這些基本結構，您可以直接應用這些通用函數，而無需為每種類型的物件編寫特定的處理方法。
        
        **總結：**
        這句話的意思是，不要濫用物件來包裝那些只是簡單數據的東西。物件的真正價值在於封裝狀態和行為。對於那些只是描述性、靜態的資訊，使用更簡單、更通用的數據結構（如 Map、List）就足夠了，並且能讓您更容易地應用通用的數據處理工具。
        
        **程式碼範例：**
        我們用一個表示「用戶資料」的例子來對比。用戶資料（姓名、電子郵件、年齡）是典型的「純粹的資訊」。
        **範例 1：用物件模型化純粹的資訊 (可能過度設計)**
        ```java fold
        // 複雜的構造: UserProfile 物件
        class UserProfileObject {
            private String name; // 狀態
            private String email; // 狀態
            private int age;     // 狀態
        
            // Constructor
            public UserProfileObject(String name, String email, int age) {
                this.name = name;
                this.email = email;
                this.age = age;
            }
        
            // Getters (通常需要手動寫)
            public String getName() { return name; }
            public String getEmail() { return email; }
            public int getAge() { return age; }
        
            // Setters (如果允許修改狀態)
            public void setEmail(String email) { this.email = email; }
            public void setAge(int age) { this.age = age; }
        
            // 可能需要手動實現 equals() 和 hashCode() 來基於值比較
            @Override
            public boolean equals(Object o) {
                if (this == o) return true;
                if (o == null || getClass() != o.getClass()) return false;
                UserProfileObject that = (UserProfileObject) o;
                return age == that.age &&
                       Objects.equals(name, that.name) &&
                       Objects.equals(email, that.email);
            }
        
            @Override
            public int hashCode() {
                return Objects.hash(name, email, age);
            }
        
            @Override
            public String toString() {
                return "UserProfileObject{" +
                       "name='" + name + '\\'' +
                       ", email='" + email + '\\'' +
                       ", age=" + age +
                       '}';
            }
        
            public static void main(String[] args) {
                List<UserProfileObject> users = new ArrayList<>();
                users.add(new UserProfileObject("Alice", "alice@example.com", 30));
                users.add(new UserProfileObject("Bob", "bob@example.com", 25));
                users.add(new UserProfileObject("Charlie", "charlie@example.com", 35));
        
                // 應用通用的數據處理函數 (例如: 過濾年齡大於 30 的用戶)
                // 需要使用 Stream API 並訪問物件的 getter
                List<UserProfileObject> usersOver30 = users.stream()
                                                           .filter(user -> user.getAge() > 30) // 依賴物件的 getter
                                                           .collect(Collectors.toList());
        
                System.out.println("年齡大於 30 的用戶 (物件): " + usersOver30);
        
                // 問題：
                // 1. 需要編寫一個完整的類別，包括屬性、建構函數、getter、setter (如果需要)、equals/hashCode。
                // 2. 每個 UserProfileObject 實例都有一個身份，即使它們的數據完全相同。
                // 3. 應用通用的數據處理函數時，需要知道物件的內部結構 (例如呼叫 getAge())。
            }
        }
        
        ```
        
        在這個範例中，我們創建了一個 `UserProfileObject` 類別來表示用戶資料。它包含了屬性、建構函數、getter 等。雖然這是常見的做法，但對於僅僅表示數據而言，它引入了類別定義、物件身份、getter/setter 等額外的概念和程式碼。
        
        **範例 2：將數據視為數據 (使用基本數據結構)**
        ```java fold
        import java.util.List;
        import java.util.Map;
        import java.util.HashMap;
        import java.util.Arrays;
        import java.util.stream.Collectors;
        
        // 簡單的替代方案: 使用 Map 來表示用戶資料 (純粹的數據)
        // Map 的鍵是屬性名 (String)，值是屬性值 (Object 或特定類型)
        // List 是一個序列，用於存放多個用戶資料 Map
        public class DataAsDataExample {
        
            public static void main(String[] args) {
                // 使用 List<Map<String, Object>> 來表示用戶列表
                List<Map<String, Object>> users = Arrays.asList(
                    new HashMap<String, Object>() {{
                        put("name", "Alice");
                        put("email", "alice@example.com");
                        put("age", 30);
                    }},
                    new HashMap<String, Object>() {{
                        put("name", "Bob");
                        put("email", "bob@example.com");
                        put("age", 25);
                    }},
                    new HashMap<String, Object>() {{
                        put("name", "Charlie");
                        put("email", "charlie@example.com");
                        put("age", 35);
                    }}
                );
        
                // 應用通用的數據處理函數 (例如: 過濾年齡大於 30 的用戶)
                // 直接操作 Map 的鍵值
                List<Map<String, Object>> usersOver30 = users.stream()
                                                           .filter(userData -> (int) userData.get("age") > 30) // 直接訪問 Map 的鍵
                                                           .collect(Collectors.toList());
        
                System.out.println("年齡大於 30 的用戶 (數據結構): " + usersOver30);
        
                // 應用另一個通用數據處理函數 (例如: 提取所有用戶的姓名)
                List<String> userNames = users.stream()
                                              .map(userData -> (String) userData.get("name")) // 直接訪問 Map 的鍵
                                              .collect(Collectors.toList());
                System.out.println("所有用戶姓名: " + userNames);
        
                // 好處：
                // 1. 無需定義一個新的類別來表示這種簡單的數據結構。
                // 2. Map 和 List 是通用的數據結構，它們的行為是標準的。
                // 3. 可以直接應用 Java Stream API 等通用的數據處理函數，通過鍵名訪問數據，而無需依賴特定的 getter 方法。
                // 4. 更容易與其他使用通用數據格式 (如 JSON) 的系統集成。
                // 5. Map 的相等性通常基於其內容 (值)。
            }
        }
        
        ```
        
        在這個範例中，我們使用 `List<Map<String, Object>>` 來表示用戶列表。每個用戶是一個 `Map`，其中鍵是屬性名，值是屬性值。我們直接使用 Stream API 的通用函數（如 `filter` 和 `map`）來處理這個數據結構，通過 Map 的 `get()` 方法訪問數據。這展示了如何將「純粹的資訊」表示為數據，並應用通用的數據處理函數。
        
        **何時使用物件？**
        正如這句話的後半部分所說，「對象適用於建模具有狀態和行為的設備」。例如，一個 `BankAccount` 類別：
        ```java fold
        class BankAccount {
            private String accountNumber; // 狀態
            private double balance; // 狀態
        
            public BankAccount(String accountNumber, double initialBalance) {
                this.accountNumber = accountNumber;
                this.balance = initialBalance;
            }
        
            // 行為: 修改內部狀態的方法
            public void deposit(double amount) {
                if (amount > 0) {
                    this.balance += amount;
                }
            }
        
            // 行為: 修改內部狀態的方法 (包含業務規則)
            public void withdraw(double amount) {
                if (amount > 0 && this.balance >= amount) {
                    this.balance -= amount;
                } else {
                    throw new IllegalArgumentException("Insufficient funds or invalid amount");
                }
            }
        
            // Getters (通常需要)
            public String getAccountNumber() { return accountNumber; }
            public double getBalance() { return balance; }
        
            // equals/hashCode 可能需要基於 accountNumber (身份) 或組合
        }
        
        ```
        
        `BankAccount` 是一個典型的物件，它有明確的狀態 (`balance`) 和行為 (`deposit`, `withdraw`)，這些行為管理著狀態的變化，並且通常包含業務規則。在這種情況下，使用物件模型化是恰當的，因為物件封裝了數據和與之相關的行為。
        
        **總結：**
        這句話鼓勵我們在設計時思考：我正在模型化的是一個**有行為、有生命週期、狀態會通過方法改變的實體**，還是僅僅**一堆描述性的數據**？如果是後者，考慮使用 Map、List 等基本數據結構，而不是創建一個新的類別，這樣可以簡化程式碼，並更好地利用通用的數據處理工具。
        
- **簡化現有程式碼或問題空間：** 這是一個獨立的過程，涉及解開現有的纏繞。


**六、 環境複雜性**
Hickey 承認存在一種「環境複雜性」(environmental complexity)，它不是程式設計師的錯，來自於程式運行環境（如資源爭用、垃圾回收）。雖然有一些緩解措施（如分割），但目前缺乏良好的方法來處理這種複雜性的策略層面的組合問題。


**七、 結論**
- **簡單性是一種選擇 (Simplicity is a choice)：** 它是程式設計師的責任，如果系統不簡單，那是開發者的錯 (it's your fault)。
- **需要警惕和敏感性：** 構建簡單系統需要持續的警惕和培養對「纏繞」(entanglement) 的敏感性。
- **測試和工具是次要的：** 測試、類型檢查器等可靠性工具只是安全網，它們本身不能帶來簡單性。
- **如何讓簡單性變得容易：**選擇具有更簡單工件的構造。
- 構建以簡單性為基礎的抽象。
- 花時間預先簡化（「ramp up」），即使短期內速度較慢。
- 擁抱更多的獨立組件。

**核心訊息：** 我們可以利用本質上更簡單的工具和設計原則，構建與當前複雜系統同樣複雜但內部更簡單的系統，從而提高長期可靠性、可理解性和可變更性。這需要改變我們對「簡單」和「容易」的看法，並有意識地選擇避免引入不必要的複雜性。





ref:
["Simple Made Easy" - Rich Hickey (2011) - YouTube](https://www.youtube.com/watch?v=SxdOUGdseq4)