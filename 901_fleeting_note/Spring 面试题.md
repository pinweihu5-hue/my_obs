## Spring

### [#](https://xiaolincoding.com/interview/spring.html#%E8%AF%B4%E4%B8%80%E4%B8%8B%E4%BD%A0%E5%AF%B9-spring-%E7%9A%84%E7%90%86%E8%A7%A3)說一下你對 Spring 的理解

![[IMG-小林 Spring 面试题-20250222193719275.webp]]

Spring 框架核心特性包括：

- **IoC 容器**：Spring 通過控制反轉實現了對象的創建和對象間的依賴關系管理。開發者隻需要定義好 Bean 及其依賴關系，Spring 容器負責創建和組裝這些對象。
- **AOP**：面向切面編程，允許開發者定義橫切關注點，例如事務管理、安全控制等，獨立於業務邏輯的代碼。通過 AOP，可以將這些關注點模塊化，提高代碼的可維護性和可重用性。
- **事務管理**：Spring 提供了一致的事務管理接口，支持聲明式和編程式事務。開發者可以輕松地進行事務管理，而無需關心具體的事務 API。
- **MVC 框架**：Spring MVC 是一個基於 Servlet API 構建的 Web 框架，采用了模型 - 視圖 - 控制器（MVC）架構。它支持靈活的 URL 到頁面控制器的映射，以及多種視圖技術。

### [#](https://xiaolincoding.com/interview/spring.html#spring-ioc%E5%92%8Caop-%E4%BB%8B%E7%BB%8D%E4%B8%80%E4%B8%8B)Spring IoC 和 AOP 介紹一下

Spring IoC 和 AOP 區别：

- **IoC**：即控制反轉的意思，它是一種創建和獲取對象的技術思想，依賴注入 (DI) 是實現這種技術的一種方式。傳統開發過程中，我們需要通過 new 關鍵字來創建對象。使用 IoC 思想開發方式的話，我們不通過 new 關鍵字創建對象，而是通過 IoC 容器來幫我們實例化對象。 通過 IoC 的方式，可以大大降低對象之間的耦合度。
- **AOP**：是面向切面編程，能夠將那些與業務無關，卻為業務模塊所共同調用的邏輯封裝起來，以減少系統的重複代碼，降低模塊間的耦合度。Spring AOP 就是基於動态代理的，如果要代理的對象，實現了某個接口，那麽 Spring AOP 會使用 JDK Proxy，去創建代理對象，而對於沒有實現接口的對象，就無法使用 JDK Proxy 去進行代理了，這時候 Spring AOP 會使用 Cglib 生成一個被代理對象的子類來作為代理。

在 Spring 框架中，IOC 和 AOP 結合使用，可以更好地實現代碼的模塊化和分層管理。例如：

- 通過 IOC 容器管理對象的依賴關系，然後通過 AOP 將橫切關注點統一切入到需要的業務邏輯中。
- 使用 IOC 容器管理 Service 層和 DAO 層的依賴關系，然後通過 AOP 在 Service 層實現事務管理、日志記錄等橫切功能，使得業務邏輯更加清晰和可維護。

### [#](https://xiaolincoding.com/interview/spring.html#spring%E7%9A%84aop%E4%BB%8B%E7%BB%8D%E4%B8%80%E4%B8%8B)Spring 的 AOP 介紹一下

Spring AOP 是 Spring 框架中的一個重要模塊，用於實現面向切面編程。

我們知道，Java 就是一門面向對象編程的語言，在 OOP 中最小的單元就是 “Class 對象”，但是在 AOP 中最小的單元是 “切面”。一個 “切面” 可以包含很多種類型和對象，對它們進行模塊化管理，例如事務管理。

在面向切面編程的思想裏面，把功能分為兩種

- **核心業務**：登陸、注冊、增、删、改、查、都叫核心業務
- **周邊功能**：日志、事務管理這些次要的為周邊業務

在面向切面編程中，核心業務功能和周邊功能是分别獨立進行開發，兩者不是耦合的，然後把切面功能和核心業務功能 "編織" 在一起，這就叫 AOP。

AOP 能夠將那些與業務無關，**卻為業務模塊所共同調用的邏輯或責任（例如事務處理、日志管理、權限控制等）封裝起來**，便於**減少系統的重複代碼**，**降低模塊間的耦合度**，并**有利於未來的可拓展性和可維護性**。

在 AOP 中有以下幾個概念：

- **AspectJ**：切面，隻是一個概念，沒有具體的接口或類與之對應，是 Join point，Advice 和 Pointcut 的一個統稱。
- **Join point**：連接點，指程序執行過程中的一個點，例如方法調用、異常處理等。在 Spring AOP 中，僅支持方法級别的連接點。
- **Advice**：通知，即我們定義的一個切面中的橫切邏輯，有 “around”，“before” 和 “after” 三種類型。在很多的 AOP 實現框架中，Advice 通常作為一個攔截器，也可以包含許多個攔截器作為一條鏈路圍繞着 Join point 進行處理。
- **Pointcut**：切點，用於匹配連接點，一個 AspectJ 中包含哪些 Join point 需要由 Pointcut 進行篩選。
- **Introduction**：引介，讓一個切面可以聲明被通知的對象實現任何他們沒有真正實現的額外的接口。例如可以讓一個代理對象代理兩個目標類。
- **Weaving**：織入，在有了連接點、切點、通知以及切面，如何將它們應用到程序中呢？沒錯，就是織入，在切點的引導下，將通知邏輯插入到目標方法上，使得我們的通知邏輯在方法調用時得以執行。
- **AOP proxy**：AOP 代理，指在 AOP 實現框架中實現切面協議的對象。在 Spring AOP 中有兩種代理，分别是 JDK 動态代理和 CGLIB 動态代理。
- **Target object**：目標對象，就是被代理的對象。

Spring AOP 是基於 JDK 動态代理和 Cglib 提升實現的，兩種代理方式都屬於運行時的一個方式，所以它沒有編譯時的一個處理，那麽因此 Spring 是通過 Java 代碼實現的。

### [#](https://xiaolincoding.com/interview/spring.html#ioc%E5%92%8Caop%E6%98%AF%E9%80%9A%E8%BF%87%E4%BB%80%E4%B9%88%E6%9C%BA%E5%88%B6%E6%9D%A5%E5%AE%9E%E7%8E%B0%E7%9A%84)IOC 和 AOP 是通過什麽機制來實現的？

> Spring IOC 實現機制

- **反射**：Spring IOC 容器利用 Java 的反射機制動态地加載類、創建對象實例及調用對象方法，反射允許在運行時檢查類、方法、屬性等信息，從而實現靈活的對象實例化和管理。
- **依賴注入**：IOC 的核心概念是依賴注入，即容器負責管理應用程序組件之間的依賴關系。Spring 通過構造函數注入、屬性注入或方法注入，將組件之間的依賴關系描述在配置文件中或使用注解。
- **設計模式 - 工廠模式**：Spring IOC 容器通常采用工廠模式來管理對象的創建和生命周期。容器作為工廠負責實例化 Bean 并管理它們的生命周期，將 Bean 的實例化過程交給容器來管理。
- **容器實現**：Spring IOC 容器是實現 IOC 的核心，通常使用 BeanFactory 或 ApplicationContext 來管理 Bean。BeanFactory 是 IOC 容器的基本形式，提供基本的 IOC 功能；ApplicationContext 是 BeanFactory 的擴展，并提供更多企業級功能。

> Spring AOP 實現機制

Spring AOP 的實現依賴於**動态代理技術**。動态代理是在運行時動态生成代理對象，而不是在編譯時。它允許開發者在運行時指定要代理的接口和行為，從而實現在不修改源碼的情況下增強方法的功能。

Spring AOP 支持兩種動态代理：

- **基於 JDK 的動态代理**：使用 java.lang.reflect.Proxy 類和 java.lang.reflect.InvocationHandler 接口實現。這種方式需要代理的類實現一個或多個接口。
- **基於 CGLIB 的動态代理**：當被代理的類沒有實現接口時，Spring 會使用 CGLIB 庫生成一個被代理類的子類作為代理。CGLIB（Code Generation Library）是一個第三方代碼生成庫，通過繼承方式實現代理。

### [#](https://xiaolincoding.com/interview/spring.html#%E6%80%8E%E4%B9%88%E7%90%86%E8%A7%A3springioc)怎麽理解 Spring IOC？

**IOC**：Inversion Of Control，即控制反轉，是一種設計思想。在傳統的 Java SE 程序設計中，我們直接在對象内部通過 new 的方式來創建對象，是程序主動創建依賴對象；

![[IMG-Spring 面试题-20250222193846522.png]]

而在 Spring 程序設計中，IOC 是有專門的容器去控制對象。

![[IMG-Spring 面试题-20250807203900734.png]]

**所謂控制**就是對象的創建、初始化、銷毀。

- 創建對象：原來是 new 一個，現在是由 Spring 容器創建。
- 初始化對象：原來是對象自己通過構造器或者 setter 方法給依賴的對象賦值，現在是由 Spring 容器自動注入。
- 銷毀對象：原來是直接給對象賦值 null 或做一些銷毀操作，現在是 Spring 容器管理生命周期負責銷毀對象。

總結：IOC 解決了繁瑣的對象生命周期的操作，解耦了我們的代碼。**所謂反轉**：其實是反轉的控制權，前面提到是由 Spring 來控制對象的生命周期，那麽對象的控制就完全脫離了我們的控制，控制權交給了 Spring 。這個反轉是指：我們由對象的控制者變成了 IOC 的被動控制者。

### [#](https://xiaolincoding.com/interview/spring.html#%E4%BE%9D%E8%B5%96%E5%80%92%E7%BD%AE-%E4%BE%9D%E8%B5%96%E6%B3%A8%E5%85%A5-%E6%8E%A7%E5%88%B6%E5%8F%8D%E8%BD%AC%E5%88%86%E5%88%AB%E6%98%AF%E4%BB%80%E4%B9%88)依賴倒置，依賴注入，控制反轉分别是什麽？

- 控制反轉：“控制” 指的是對程序執行流程的控制，而 “反轉” 指的是在沒有使用框架之前，程序員自己控制整個程序的執行。在使用框架之後，整個程序的執行流程通過框架來控制。流程的控制權從程序員 “反轉” 給了框架。
- 依賴注入：依賴注入和控制反轉恰恰相反，它是一種具體的編碼技巧。我們不通過 new 的方式在類内部創建依賴類的對象，而是將依賴的類對象在外部創建好之後，通過構造函數、函數參數等方式傳遞（或注入）給類來使用。
- 依賴倒置：這條原則跟控制反轉有點類似，主要用來指導框架層面的設計。高層模塊不依賴低層模塊，它們共同依賴同一個抽象。抽象不要依賴具體實現細節，具體實現細節依賴抽象。

### [#](https://xiaolincoding.com/interview/spring.html#%E5%A6%82%E6%9E%9C%E8%AE%A9%E4%BD%A0%E8%AE%BE%E8%AE%A1%E4%B8%80%E4%B8%AAspringioc-%E4%BD%A0%E8%A7%89%E5%BE%97%E4%BC%9A%E4%BB%8E%E5%93%AA%E4%BA%9B%E6%96%B9%E9%9D%A2%E8%80%83%E8%99%91%E8%BF%99%E4%B8%AA%E8%AE%BE%E8%AE%A1)如果讓你設計一個 SpringIoc，你覺得會從哪些方面考慮這個設計？

- Bean 的生命周期管理：需要設計 Bean 的創建、初始化、銷毀等生命周期管理機制，可以考慮使用工廠模式和單例模式來實現。
- 依賴注入：需要實現依賴注入的功能，包括屬性注入、構造函數注入、方法注入等，可以考慮使用反射機制和 XML 配置文件來實現。
- Bean 的作用域：需要支持多種 Bean 作用域，比如單例、原型、會話、請求等，可以考慮使用 Map 來存儲不同作用域的 Bean 實例。
- AOP 功能的支持：需要支持 AOP 功能，可以考慮使用動态代理機制和切面編程來實現。
- 異常處理：需要考慮異常處理機制，包括 Bean 創建異常、依賴注入異常等，可以考慮使用 try-catch 機制來處理異常。
- 配置文件加載：需要支持從不同的配置文件中加載 Bean 的相關信息，可以考慮使用 XML、注解或者 Java 配置類來實現。

### [#](https://xiaolincoding.com/interview/spring.html#springaop%E4%B8%BB%E8%A6%81%E6%83%B3%E8%A7%A3%E5%86%B3%E4%BB%80%E4%B9%88%E9%97%AE%E9%A2%98)SpringAOP 主要想解決什麽問題

它的目的是對於面向對象思維的一種補充，而不是像引入命令式、函數式編程思維讓他順應另一種開發場景。在我個人的理解下 AOP 更像是一種對於不支持多繼承的彌補，除開對象的主要特征（我更喜歡叫 “強共性”）被抽象為了一條繼承鏈路，對於一些 “弱共性”，AOP 可以統一對他們進行抽象和集中處理。

舉一個簡單的例子，打印日志。需要打印日志可能是許多對象的一個共性，這在企業級開發中十分常見，但是日志的打印并不反應這個對象的主要共性。而日志的打印又是一個具體的内容，它并不抽象，所以它的工作也不可以用接口來完成。而如果利用繼承，打印日志的工作又橫跨繼承樹下面的多個同級子節點，強行侵入到繼承樹内進行歸納會幹擾這些強共性的區分。

這時候，我們就需要 AOP 了。AOP 首先在一個 Aspect（切面）裏定義了一些 Advice（增強），其中包含具體實現的代碼，同時整理了切入點，切入點的粒度是方法。最後，我們將這些 Advice 織入到對象的方法上，形成了最後執行方法時面對的完整方法。

![[IMG-Spring 面试题-20250807203902309.png]]

### [#](https://xiaolincoding.com/interview/spring.html#springaop%E7%9A%84%E5%8E%9F%E7%90%86%E4%BA%86%E8%A7%A3%E5%90%97)SpringAOP 的原理了解嗎

Spring AOP 的實現依賴於**動态代理技術**。動态代理是在運行時動态生成代理對象，而不是在編譯時。它允許開發者在運行時指定要代理的接口和行為，從而實現在不修改源碼的情況下增強方法的功能。

Spring AOP 支持兩種動态代理：

- **基於 JDK 的動态代理**：使用 java.lang.reflect.Proxy 類和 java.lang.reflect.InvocationHandler 接口實現。這種方式需要代理的類實現一個或多個接口。
- **基於 CGLIB 的動态代理**：當被代理的類沒有實現接口時，Spring 會使用 CGLIB 庫生成一個被代理類的子類作為代理。CGLIB（Code Generation Library）是一個第三方代碼生成庫，通過繼承方式實現代理。

### [#](https://xiaolincoding.com/interview/spring.html#%E5%8A%A8%E6%80%81%E4%BB%A3%E7%90%86%E6%98%AF%E4%BB%80%E4%B9%88)動态代理是什麽？

Java 的動态代理是一種在運行時動态創建代理對象的機制，主要用於在不修改原始類的情況下對方法調用進行攔截和增強。

Java 動态代理主要分為兩種類型：

- **基於接口的代理**（JDK 動态代理）： 這種類型的代理要求目標對象必須實現至少一個接口。Java 動态代理會創建一個實現了相同接口的代理類，然後在運行時動态生成該類的實例。這種代理的實現核心是 `java.lang.reflect.Proxy` 類和 `java.lang.reflect.InvocationHandler` 接口。每一個動态代理類都必須實現 `InvocationHandler` 接口，并且每個代理類的實例都關聯到一個 `handler`。當通過代理對象調用一個方法時，這個方法的調用會被轉發為由 `InvocationHandler` 接口的 `invoke()` 方法來進行調用。
    
- **基於類的代理**（CGLIB 動态代理）： CGLIB（Code Generation Library）是一個強大的高性能的代碼生成庫，它可以在運行時動态生成一個目標類的子類。CGLIB 代理不需要目標類實現接口，而是通過繼承的方式創建代理類。因此，如果目標對象沒有實現任何接口，可以使用 CGLIB 來創建動态代理。
    

### [#](https://xiaolincoding.com/interview/spring.html#%E5%8A%A8%E6%80%81%E4%BB%A3%E7%90%86%E5%92%8C%E9%9D%99%E6%80%81%E4%BB%A3%E7%90%86%E7%9A%84%E5%8C%BA%E5%88%AB)動态代理和靜态代理的區别

代理是一種常用的設計模式，目的是：為其他對象提供一個代理以控制對某個對象的訪問，將兩個類的關系解耦。代理類和委托類都要實現相同的接口，因為代理真正調用的是委托類的方法。

區别：

- 靜态代理：由程序員創建或者是由特定工具創建，在代碼編譯時就确定了被代理的類是一個靜态代理。靜态代理通常隻代理一個類；
- 動态代理：在代碼運行期間，運用反射機制動态創建生成。動态代理代理的是一個接口下的多個實現類。

### [#](https://xiaolincoding.com/interview/spring.html#aop%E5%AE%9E%E7%8E%B0%E6%9C%89%E5%93%AA%E4%BA%9B%E6%B3%A8%E8%A7%A3)AOP 實現有哪些注解？

常用的注解包括：

- @Aspect：用於定義切面，標注在切面類上。
- @Pointcut：定義切點，標注在方法上，用於指定連接點。
- @Before：在方法執行之前執行通知。
- @After：在方法執行之後執行通知。
- @Around：在方法執行前後都執行通知。
- @AfterReturning：在方法執行後返回結果後執行通知。
- @AfterThrowing：在方法抛出異常後執行通知。
- @Advice：通用的通知類型，可以替代 @Before、@After 等。

### [#](https://xiaolincoding.com/interview/spring.html#%E4%BB%80%E4%B9%88%E6%98%AF%E5%8F%8D%E5%B0%84-%E6%9C%89%E5%93%AA%E4%BA%9B%E4%BD%BF%E7%94%A8%E5%9C%BA%E6%99%AF)什麽是反射？有哪些使用場景？

反射具有以下特性：

1. **運行時類信息訪問**：反射機制允許程序在運行時獲取類的完整結構信息，包括類名、包名、父類、實現的接口、構造函數、方法和字段等。
2. **動态對象創建**：可以使用反射 API 動态地創建對象實例，即使在編譯時不知道具體的類名。這是通過 Class 類的 newInstance () 方法或 Constructor 對象的 newInstance () 方法實現的。
3. **動态方法調用**：可以在運行時動态地調用對象的方法，包括私有方法。這通過 Method 類的 invoke () 方法實現，允許你傳入對象實例和參數值來執行方法。
4. **訪問和修改字段值**：反射還允許程序在運行時訪問和修改對象的字段值，即使是私有的。這是通過 Field 類的 get () 和 set () 方法完成的。

![[IMG-Spring 面试题-20250807203903804.webp]]

Java 反射機制在 spring 框架中，很多地方都用到了反射，讓我們來看看 Spring 的 IoC 和 AOP 是如何使用反射技術的。

> 1、Spring 框架的依賴注入（DI）和控制反轉（IoC）

Spring 使用反射來實現其核心特性：依賴注入。

在 Spring 中，開發者可以通過 XML 配置文件或者基於注解的方式聲明組件之間的依賴關系。當應用程序啓動時，Spring 容器會掃描這些配置或注解，然後利用反射來實例化 Bean（即 Java 對象），并根據配置自動裝配它們的依賴。

例如，當一個 Service 類需要依賴另一個 DAO 類時，開發者可以在 Service 類中使用 @Autowired 注解，而無需自己編寫創建 DAO 實例的代碼。Spring 容器會在運行時解析這個注解，通過反射找到對應的 DAO 類，實例化它，并將其注入到 Service 類中。這樣不僅降低了組件之間的耦合度，也極大地增強了代碼的可維護性和可測試性。

> 2、動态代理的實現

在需要對現有類的方法調用進行攔截、記錄日志、權限控制或是事務管理等場景中，反射結合動态代理技術被廣泛應用。

一個典型的例子是 Spring AOP（面向切面編程）的實現。Spring AOP 允許開發者定義切面（Aspect），這些切面可以橫切關注點（如日志記錄、事務管理），并將其插入到業務邏輯中，而不需要修改業務邏輯代碼。

例如，為了給所有的服務層方法添加日志記錄功能，可以定義一個切面，在這個切面中，Spring 會使用 JDK 動态代理或 CGLIB（如果目標類沒有實現接口）來創建目標類的代理對象。這個代理對象在調用任何方法前或後，都會執行切面中定義的代碼邏輯（如記錄日志），而這一切都是在運行時通過反射來動态構建和執行的，無需硬編碼到每個方法調用中。

這兩個例子展示了反射機制如何在實際工程中促進**松耦合、高内聚**的設計，以及如何提供動态、靈活的編程能力，特别是在框架層面和解決跨切面問題時。

### [#](https://xiaolincoding.com/interview/spring.html#spring%E6%98%AF%E5%A6%82%E4%BD%95%E8%A7%A3%E5%86%B3%E5%BE%AA%E7%8E%AF%E4%BE%9D%E8%B5%96%E7%9A%84)**spring 是如何解決循環依賴的？**

循環依賴指的是兩個類中的屬性相互依賴對方：例如 A 類中有 B 屬性，B 類中有 A 屬性，從而形成了一個依賴閉環，如下圖。

![[IMG-Spring 面试题-20250807203905183.png]]

循環依賴問題在 Spring 中主要有三種情況：

- 第一種：通過構造方法進行依賴注入時産生的循環依賴問題。
- 第二種：通過 setter 方法進行依賴注入且是在多例（原型）模式下産生的循環依賴問題。
- 第三種：通過 setter 方法進行依賴注入且是在單例模式下産生的循環依賴問題。

隻有【第三種方式】的循環依賴問題被 Spring 解決了，其他兩種方式在遇到循環依賴問題時，Spring 都會産生異常。

Spring 解決單例模式下的 setter 循環依賴問題的主要方式是通過三級緩存解決循環依賴。三級緩存指的是 Spring 在創建 Bean 的過程中，通過三級緩存來緩存正在創建的 Bean，以及已經創建完成的 Bean 實例。具體步驟如下：

- **實例化 Bean**：Spring 在實例化 Bean 時，會先創建一個空的 Bean 對象，并將其放入一級緩存中。
- **屬性賦值**：Spring 開始對 Bean 進行屬性賦值，如果發現循環依賴，會將當前 Bean 對象提前暴露給後續需要依賴的 Bean（通過提前暴露的方式解決循環依賴）。
- **初始化 Bean**：完成屬性賦值後，Spring 將 Bean 進行初始化，并將其放入二級緩存中。
- **注入依賴**：Spring 繼續對 Bean 進行依賴注入，如果發現循環依賴，會從二級緩存中獲取已經完成初始化的 Bean 實例。

通過三級緩存的機制，Spring 能夠在處理循環依賴時，确保及時暴露正在創建的 Bean 對象，并能夠正确地注入已經初始化的 Bean 實例，從而解決循環依賴問題，保證應用程序的正常運行。

### [#](https://xiaolincoding.com/interview/spring.html#spring%E4%B8%89%E7%BA%A7%E7%BC%93%E5%AD%98%E7%9A%84%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E6%98%AF%E4%BB%80%E4%B9%88)spring 三級緩存的數據結構是什麽？

都是 Map 類型的緩存，比如 Map {k:name; v:bean}。

1. **一級緩存（Singleton Objects）**：這是一個 Map 類型的緩存，存儲的是已經完全初始化好的 bean，即完全準備好可以使用的 bean 實例。鍵是 bean 的名稱，值是 bean 的實例。這個緩存在 `DefaultSingletonBeanRegistry` 類中的 `singletonObjects` 屬性中。
2. **二級緩存（Early Singleton Objects）**：這同樣是一個 Map 類型的緩存，存儲的是早期的 bean 引用，即已經實例化但還未完全初始化的 bean。這些 bean 已經被實例化，但是可能還沒有進行屬性注入等操作。這個緩存在 `DefaultSingletonBeanRegistry` 類中的 `earlySingletonObjects` 屬性中。
3. **三級緩存（Singleton Factories）**：這也是一個 Map 類型的緩存，存儲的是 ObjectFactory 對象，這些對象可以生成早期的 bean 引用。當一個 bean 正在創建過程中，如果它被其他 bean 依賴，那麽這個正在創建的 bean 就會通過這個 ObjectFactory 來創建一個早期引用，從而解決循環依賴的問題。這個緩存在 `DefaultSingletonBeanRegistry` 類中的 `singletonFactories` 屬性中。

### [#](https://xiaolincoding.com/interview/spring.html#spring%E6%A1%86%E6%9E%B6%E4%B8%AD%E9%83%BD%E7%94%A8%E5%88%B0%E4%BA%86%E5%93%AA%E4%BA%9B%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)spring 框架中都用到了哪些設計模式

- **工廠設計模式** : Spring 使用工廠模式通過 BeanFactory、ApplicationContext 創建 bean 對象。
- **代理設計模式** : Spring AOP 功能的實現。
- **單例設計模式** : Spring 中的 Bean 默認都是單例的。
- **模闆方法模式** : Spring 中 jdbcTemplate、hibernateTemplate 等以 Template 結尾的對數據庫操作的類，它們就使用到了模闆模式。
- **包裝器設計模式** : 我們的項目需要連接多個數據庫，而且不同的客戶在每次訪問中根據需要會去訪問不同的數據庫。這種模式讓我們可以根據客戶的需求能夠動态切換不同的數據源。
- **觀察者模式:** Spring 事件驅動模型就是觀察者模式很經典的一個應用。
- **适配器模式** :Spring AOP 的增強或通知 (Advice) 使用到了适配器模式、spring MVC 中也是用到了适配器模式适配 Controller。

### [#](https://xiaolincoding.com/interview/spring.html#spring-%E5%B8%B8%E7%94%A8%E6%B3%A8%E8%A7%A3%E6%9C%89%E4%BB%80%E4%B9%88)spring **常用注解有什麽？**

> @Autowired 注解

@Autowired：主要用於自動裝配 bean。當 Spring [容器 (opens new window)](https://cloud.tencent.com/product/tke?from_column=20065&from=20065)中存在與要注入的屬性類型匹配的 bean 時，它會自動將 bean 注入到屬性中。就跟我們 new 對象一樣。

用法很簡單，如下示例代碼：

```
@Component
public class MyService {
}

@Component
public class MyController {

    @Autowired
    private MyService myService;

}
```

在上面的示例代碼中，MyController 類中的 myService 屬性被 @Autowired 注解標記，Spring 會自動將 MyService 類型的 bean 注入到 myService 屬性中。

> @Component

這個注解用於標記一個類作為 Spring 的 bean。當一個類被 @Component 注解標記時，Spring 會將其實例化為一個 bean，并將其添加到 Spring 容器中。在上面講解 @Autowired 的時候也看到了，示例代碼：

```
@Component
public class MyComponent {
}
```

在上面的示例代碼中，MyComponent 類被 @Component 注解標記，Spring 會將其實例化為一個 bean，并將其添加到 Spring 容器中。

> @Configuration

@Configuration，注解用於標記一個類作為 Spring 的配置類。配置類可以包含 @Bean 注解的方法，用於定義和配置 bean，作為全局配置。示例代碼：

```
@Configuration
public class MyConfiguration {

    @Bean
    public MyBean myBean() {
        return new MyBean();
    }

}
```

> @Bean

@Bean 注解用於標記一個方法作為 Spring 的 bean 工廠方法。當一個方法被 @Bean 注解標記時，Spring 會將該方法的返回值作為一個 bean，并將其添加到 Spring 容器中，如果自定義配置，經常用到這個注解。

```
@Configuration
public class MyConfiguration {

    @Bean
    public MyBean myBean() {
        return new MyBean();

    }

}
```

> @Service

@Service，這個注解用於標記一個類作為服務層的組件。它是 @Component 注解的特例，用於標記服務層的 bean，一般標記在業務 service 的實現類。

```
@Service
public class MyServiceImpl {

}
```

> @Repository

@Repository 注解用於標記一個類作為數據訪問層的組件。它也是 @Component 注解的特例，用於標記數據訪問層的 bean。這個注解很容易被忽略，導致數據庫無法訪問。

```
@Repository
public class MyRepository {

}
```

在上面的示例代碼中，MyRepository 類被 @Repository 注解標記，Spring 會將其實例化為一個 bean，并將其添加到 Spring 容器中。

> @Controller

@Controller 注解用於標記一個類作為控制層的組件。它也是 @Component 注解的特例，用於標記控制層的 bean。這是 MVC 結構的另一個部分，加在控制層

```
@Controller
public class MyController {

}
```

在上面的示例代碼中，MyController 類被 @Controller 注解標記，Spring 會將其實例化為一個 bean，并將其添加到 Spring 容器中。

### [#](https://xiaolincoding.com/interview/spring.html#spring%E7%9A%84%E4%BA%8B%E5%8A%A1%E4%BB%80%E4%B9%88%E6%83%85%E5%86%B5%E4%B8%8B%E4%BC%9A%E5%A4%B1%E6%95%88)Spring 的事務什麽情況下會失效？

Spring Boot 通過 Spring 框架的事務管理模塊來支持事務操作。事務管理在 Spring Boot 中通常是通過 @Transactional 注解來實現的。事務可能會失效的一些常見情況包括:

1. **未捕獲異常**: 如果一個事務方法中發生了未捕獲的異常，并且異常未被處理或傳播到事務邊界之外，那麽事務會失效，所有的數據庫操作會回滾。
2. **非受檢異常**: 默認情況下，Spring 對非受檢異常（RuntimeException 或其子類）進行回滾處理，這意味着當事務方法中抛出這些異常時，事務會回滾。
3. **事務傳播屬性設置不當**: 如果在多個事務之間存在事務嵌套，且事務傳播屬性配置不正确，可能導致事務失效。特别是在方法内部調用有 @Transactional 注解的方法時要特别注意。
4. **多數據源的事務管理**: 如果在使用多數據源時，事務管理沒有正确配置或者存在多個 @Transactional 注解時，可能會導致事務失效。
5. **跨方法調用事務問題**: 如果一個事務方法内部調用另一個方法，而這個被調用的方法沒有 @Transactional 注解，這種情況下外層事務可能會失效。
6. **事務在非公開方法中失效**: 如果 @Transactional 注解標注在私有方法上或者非 public 方法上，事務也會失效。

### [#](https://xiaolincoding.com/interview/spring.html#spring%E7%9A%84%E4%BA%8B%E5%8A%A1-%E4%BD%BF%E7%94%A8this%E8%B0%83%E7%94%A8%E6%98%AF%E5%90%A6%E7%94%9F%E6%95%88)Spring 的事務，使用 this 調用是否生效？

不能生效。

因為 Spring 事務是通過代理對象來控制的，隻有通過代理對象的方法調用才會應用事務管理的相關規則。當使用 `this` 直接調用時，是繞過了 Spring 的代理機制，因此不會應用事務設置。

### [#](https://xiaolincoding.com/interview/spring.html#bean%E7%9A%84%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E8%AF%B4%E4%B8%80%E4%B8%8B)Bean 的生命周期說一下？

![[IMG-Spring 面试题-20250807203906799.png]]

1. Spring 啓動，查找并加載需要被 Spring 管理的 bean，進行 Bean 的實例化
2. Bean 實例化後對將 Bean 的引入和值注入到 Bean 的屬性中
3. 如果 Bean 實現了 BeanNameAware 接口的話，Spring 將 Bean 的 Id 傳遞給 setBeanName () 方法
4. 如果 Bean 實現了 BeanFactoryAware 接口的話，Spring 將調用 setBeanFactory () 方法，將 BeanFactory 容器實例傳入
5. 如果 Bean 實現了 ApplicationContextAware 接口的話，Spring 將調用 Bean 的 setApplicationContext () 方法，將 bean 所在應用上下文引用傳入進來。
6. 如果 Bean 實現了 BeanPostProcessor 接口，Spring 就將調用他們的 postProcessBeforeInitialization () 方法。
7. 如果 Bean 實現了 InitializingBean 接口，Spring 將調用他們的 afterPropertiesSet () 方法。類似的，如果 bean 使用 init-method 聲明了初始化方法，該方法也會被調用
8. 如果 Bean 實現了 BeanPostProcessor 接口，Spring 就將調用他們的 postProcessAfterInitialization () 方法。
9. 此時，Bean 已經準備就緒，可以被應用程序使用了。他們將一直駐留在應用上下文中，直到應用上下文被銷毀。
10. 如果 bean 實現了 DisposableBean 接口，Spring 將調用它的 destory () 接口方法，同樣，如果 bean 使用了 destory-method 聲明銷毀方法，該方法也會被調用。

### [#](https://xiaolincoding.com/interview/spring.html#bean%E6%98%AF%E5%90%A6%E5%8D%95%E4%BE%8B)Bean 是否單例？

Spring 中的 Bean 默認都是單例的。

就是說，每個 Bean 的實例隻會被創建一次，并且會被存儲在 Spring 容器的緩存中，以便在後續的請求中重複使用。這種單例模式可以提高應用程序的性能和内存效率。

但是，Spring 也支持將 Bean 設置為多例模式，即每次請求都會創建一個新的 Bean 實例。要將 Bean 設置為多例模式，可以在 Bean 定義中通過設置 scope 屬性為 "prototype" 來實現。

需要注意的是，雖然 Spring 的默認行為是將 Bean 設置為單例模式，但在一些情況下，使用多例模式是更為合适的，例如在創建狀态不可變的 Bean 或有狀态 Bean 時。此外，需要注意的是，如果 Bean 單例是有狀态的，那麽在使用時需要考慮線程安全性問題。

### [#](https://xiaolincoding.com/interview/spring.html#bean%E7%9A%84%E5%8D%95%E4%BE%8B%E5%92%8C%E9%9D%9E%E5%8D%95%E4%BE%8B-%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E6%98%AF%E5%90%A6%E4%B8%80%E6%A0%B7)Bean 的單例和非單例，生命周期是否一樣

不一樣的，Spring Bean 的生命周期完全由 IoC 容器控制。Spring 隻幫我們管理單例模式 Bean 的完整生命周期，對於 `prototype` 的 Bean，Spring 在創建好交給使用者之後，則不會再管理後續的生命周期。

### [#](https://xiaolincoding.com/interview/spring.html#spring-bean%E7%9A%84%E4%BD%9C%E7%94%A8%E5%9F%9F%E6%9C%89%E5%93%AA%E4%BA%9B)Spring bean 的作用域有哪些？

Spring 框架中的 Bean 作用域（Scope）定義了 Bean 的生命周期和可見性。不同的作用域影響着 Spring 容器如何管理這些 Bean 的實例，包括它們如何被創建、如何被銷毀以及它們是否可以被多個用戶共享。

Spring 支持幾種不同的作用域，以滿足不同的應用場景需求。以下是一些主要的 Bean 作用域：

- **Singleton（單例）**：在整個應用程序中隻存在一個 Bean 實例。默認作用域，Spring 容器中隻會創建一個 Bean 實例，并在容器的整個生命周期中共享該實例。
- **Prototype（原型）**：每次請求時都會創建一個新的 Bean 實例。次從容器中獲取該 Bean 時都會創建一個新實例，适用於狀态非常瞬時的 Bean。
- **Request（請求）**：每個 HTTP 請求都會創建一個新的 Bean 實例。僅在 Spring Web 應用程序中有效，每個 HTTP 請求都會創建一個新的 Bean 實例，适用於 Web 應用中需求局部性的 Bean。
- **Session（會話）**：Session 範圍内隻會創建一個 Bean 實例。該 Bean 實例在用戶會話範圍内共享，僅在 Spring Web 應用程序中有效，适用於與用戶會話相關的 Bean。
- **Application**：當前 ServletContext 中隻存在一個 Bean 實例。僅在 Spring Web 應用程序中有效，該 Bean 實例在整個 ServletContext 範圍内共享，适用於應用程序範圍内共享的 Bean。
- **WebSocket（Web 套接字）**：在 WebSocket 範圍内隻存在一個 Bean 實例。僅在支持 WebSocket 的應用程序中有效，該 Bean 實例在 WebSocket 會話範圍内共享，适用於 WebSocket 會話範圍内共享的 Bean。
- **Custom scopes（自定義作用域）**：Spring 允許開發者定義自定義的作用域，通過實現 Scope 接口來創建新的 Bean 作用域。

在 Spring 配置文件中，可以通過標簽的 scope 屬性來指定 Bean 的作用域。例如：

```
<bean id="myBean" class="com.example.MyBeanClass" scope="singleton"/>
```

在 Spring Boot 或基於 Java 的配置中，可以通過 @Scope 注解來指定 Bean 的作用域。例如：

```
@Bean  
@Scope("prototype")  
public MyBeanClass myBean() {  
    return new MyBeanClass();  
}
```

### [#](https://xiaolincoding.com/interview/spring.html#spring%E5%AE%B9%E5%99%A8%E9%87%8C%E5%AD%98%E7%9A%84%E6%98%AF%E4%BB%80%E4%B9%88)Spring 容器裏存的是什麽？

在 Spring 容器中，存儲的**主要是 Bean 對象**。

Bean 是 Spring 框架中的基本組件，用於表示應用程序中的各種對象。當應用程序啓動時，Spring 容器會根據配置文件或注解的方式創建和管理這些 Bean 對象。Spring 容器會負責創建、初始化、注入依賴以及銷毀 Bean 對象。

### [#](https://xiaolincoding.com/interview/spring.html#%E5%9C%A8spring%E4%B8%AD-%E5%9C%A8bean%E5%8A%A0%E8%BD%BD-%E9%94%80%E6%AF%81%E5%89%8D%E5%90%8E-%E5%A6%82%E6%9E%9C%E6%83%B3%E5%AE%9E%E7%8E%B0%E6%9F%90%E4%BA%9B%E9%80%BB%E8%BE%91-%E5%8F%AF%E4%BB%A5%E6%80%8E%E4%B9%88%E5%81%9A)在 Spring 中，在 bean 加載 / 銷毀前後，如果想實現某些邏輯，可以怎麽做

在 Spring 框架中，如果你希望在 Bean 加載（即實例化、屬性賦值、初始化等過程完成後）或銷毀前後執行某些邏輯，你可以使用 Spring 的生命周期回調接口或注解。這些接口和注解允許你定義在 Bean 生命周期的關鍵點執行的代碼。

> 使用 init-method 和 destroy-method

在 XML 配置中，你可以通過 init-method 和 destroy-method 屬性來指定 Bean 初始化後和銷毀前需要調用的方法。

```
<bean id="myBean" class="com.example.MyBeanClass"  
      init-method="init" destroy-method="destroy"/>
```

然後，在你的 Bean 類中實現這些方法：

```
public class MyBeanClass {  
  
    public void init() {  
        // 初始化邏輯  
    }  
  
    public void destroy() {  
        // 銷毀邏輯  
    }  
}
```

> 實現 InitializingBean 和 DisposableBean 接口

你的 Bean 類可以實現 org.springframework.beans.factory.InitializingBean 和 org.springframework.beans.factory.DisposableBean 接口，并分别實現 afterPropertiesSet 和 destroy 方法。

```
import org.springframework.beans.factory.DisposableBean;  
import org.springframework.beans.factory.InitializingBean;  
  
public class MyBeanClass implements InitializingBean, DisposableBean {  
  
    @Override  
    public void afterPropertiesSet() throws Exception {  
        // 初始化邏輯  
    }  
  
    @Override  
    public void destroy() throws Exception {  
        // 銷毀邏輯  
    }  
}
```

> 使用 @PostConstruct 和 @PreDestroy 注解

```
import javax.annotation.PostConstruct;  
import javax.annotation.PreDestroy;  
  
public class MyBeanClass {  
  
    @PostConstruct  
    public void init() {  
        // 初始化邏輯  
    }  
  
    @PreDestroy  
    public void destroy() {  
        // 銷毀邏輯  
    }  
}
```

> 使用 @Bean 注解的 initMethod 和 destroyMethod 屬性

在基於 Java 的配置中，你還可以在 @Bean 注解中指定 initMethod 和 destroyMethod 屬性。

```
@Configuration  
public class AppConfig {  
  
    @Bean(initMethod = "init", destroyMethod = "destroy")  
    public MyBeanClass myBean() {  
        return new MyBeanClass();  
    }  
}
```

### [#](https://xiaolincoding.com/interview/spring.html#bean%E6%B3%A8%E5%85%A5%E5%92%8Cxml%E6%B3%A8%E5%85%A5%E6%9C%80%E7%BB%88%E5%BE%97%E5%88%B0%E4%BA%86%E7%9B%B8%E5%90%8C%E7%9A%84%E6%95%88%E6%9E%9C-%E5%AE%83%E4%BB%AC%E5%9C%A8%E5%BA%95%E5%B1%82%E6%98%AF%E6%80%8E%E6%A0%B7%E5%81%9A%E7%9A%84)Bean 注入和 xml 注入最終得到了相同的效果，它們在底層是怎樣做的

> XML 注入

使用 XML 文件進行 Bean 注入時，Spring 在啓動時會讀取 XML 配置文件，以下是其底層步驟：

- **Bean 定義解析**：Spring 容器通過 `XmlBeanDefinitionReader` 類解析 XML 配置文件，讀取其中的 `<bean>` 標簽以獲取 Bean 的定義信息。
- **注冊 Bean 定義**：解析後的 Bean 信息被注冊到 `BeanDefinitionRegistry`（如 `DefaultListableBeanFactory`）中，包括 Bean 的類、作用域、依賴關系、初始化和銷毀方法等。
- **實例化和依賴注入**：當應用程序請求某個 Bean 時，Spring 容器會根據已經注冊的 Bean 定義：
    - 首先，使用反射機制創建該 Bean 的實例。
    - 然後，根據 Bean 定義中的配置，通過 setter 方法、構造函數或方法注入所需的依賴 Bean。

> 注解注入

使用注解進行 Bean 注入時，Spring 的處理過程如下：

- **類路徑掃描**：當 Spring 容器啓動時，它首先會進行類路徑掃描，查找帶有特定注解（如 `@Component`、`@Service`、`@Repository` 和 `@Controller`）的類。
- **注冊 Bean 定義**：找到的類會被注冊到 `BeanDefinitionRegistry` 中，Spring 容器將為其生成 Bean 定義信息。這通常通過 `AnnotatedBeanDefinitionReader` 類來實現。
- **依賴注入**：與 XML 注入類似，Spring 在實例化 Bean 時，也會檢查字段上是否有 `@Autowired`、`@Inject` 或 `@Resource` 注解。如果有，Spring 會根據注解的信息進行依賴注入。

盡管使用的方式不同，但 XML 注入和注解注入在底層的實現機制是相似的，主要體現在以下幾個方面：

1. **BeanDefinition**：無論是 XML 還是注解，最終都會生成 `BeanDefinition` 對象，并存儲在同一個 `BeanDefinitionRegistry` 中。
2. **後處理器**：
    - Spring 提供了多個 Bean 後處理器（如 `AutowiredAnnotationBeanPostProcessor`），用於處理注解（如 `@Autowired`）的依賴注入。
    - 對於 XML，Spring 也有相應的後處理器來處理 XML 配置的依賴注入。
3. **依賴查找**：在依賴注入時，Spring 容器會通過 `ApplicationContext` 中的 BeanFactory 方法來查找和注入依賴，無論是通過 XML 還是注解，都會調用類似的查找方法。

### [#](https://xiaolincoding.com/interview/spring.html#spring%E7%BB%99%E6%88%91%E4%BB%AC%E6%8F%90%E4%BE%9B%E4%BA%86%E5%BE%88%E5%A4%9A%E6%89%A9%E5%B1%95%E7%82%B9-%E8%BF%99%E4%BA%9B%E6%9C%89%E4%BA%86%E8%A7%A3%E5%90%97)Spring 給我們提供了很多擴展點，這些有了解嗎？

Spring 框架提供了許多擴展點，使得開發者可以根據需求定制和擴展 Spring 的功能。以下是一些常用的擴展點：

1. BeanFactoryPostProcessor：允許在 Spring 容器實例化 bean 之前修改 bean 的定義。常用於修改 bean 屬性或改變 bean 的作用域。
2. BeanPostProcessor：可以在 bean 實例化、配置以及初始化之後對其進行額外處理。常用於代理 bean、修改 bean 屬性等。
3. PropertySource：用於定義不同的屬性源，如文件、數據庫等，以便在 Spring 應用中使用。
4. ImportSelector 和 ImportBeanDefinitionRegistrar：用於根據條件動态注冊 bean 定義，實現配置類的模塊化。
5. Spring MVC 中的 HandlerInterceptor：用於攔截處理請求，可以在請求處理前、處理中和處理後執行特定邏輯。
6. Spring MVC 中的 ControllerAdvice：用於全局處理控制器的異常、數據綁定和數據校驗。
7. Spring Boot 的自動配置：通過創建自定義的自動配置類，可以實現對框架和第三方庫的自動配置。
8. 自定義注解：創建自定義注解，用於實現特定功能或約定，如權限控制、日志記錄等。

### [#](https://xiaolincoding.com/interview/spring.html#mvc%E5%88%86%E5%B1%82%E4%BB%8B%E7%BB%8D%E4%B8%80%E4%B8%8B)MVC 分層介紹一下

MVC 全名是 Model View Controller，是模型 (model)－視圖 (view)－控制器 (controller) 的縮寫，一種軟件設計典範，用一種業務邏輯、數據、界面顯示分離的方法組織代碼，將業務邏輯聚集到一個部件裏面，在改進和個性化定制界面及用戶交互的同時，不需要重新編寫業務邏輯。

- **視圖 (view)： 為用戶提供使用界面，與用戶直接進行交互。**
- **模型 (model)： 代表一個存取數據的對象或 JAVA POJO（Plain Old Java Object，簡單 java 對象）。它也可以帶有邏輯，主要用於承載數據，并對用戶提交請求進行計算的模塊。模型分為兩類，一類稱為數據承載 Bean，一類稱為業務處理 Bean。所謂數據承載 Bean 是指實體類（如：User 類），專門為用戶承載業務數據的；而業務處理 Bean 則是指 Service 或 Dao 對象， 專門用於處理用戶提交請求的。**
- **控制器 (controller)： 用於將用戶請求轉發給相應的 Model 進行處理，并根據 Model 的計算結果向用戶提供相應響應。它使視圖與模型分離。**

![[IMG-Spring 面试题-20250807203816183.png]]

流程步驟：

1. **用戶通過 View 頁面向服務端提出請求，可以是表單請求、超鏈接請求、AJAX 請求等；**
2. **服務端 Controller 控制器接收到請求後對請求進行解析，找到相應的 Model，對用戶請求進行處理 Model 處理；**
3. **將處理結果再交給 Controller（控制器其實隻是起到了承上啓下的作用）；**
4. **根據處理結果找到要作為向客戶端發回的響應 View 頁面，頁面經渲染後發送給客戶端。**

### [#](https://xiaolincoding.com/interview/spring.html#%E4%BA%86%E8%A7%A3springmvc%E7%9A%84%E5%A4%84%E7%90%86%E6%B5%81%E7%A8%8B%E5%90%97)了解 SpringMVC 的處理流程嗎？

![[IMG-Spring 面试题-20250807203910222.png]]

Spring MVC 的工作流程如下：

1. 用戶發送請求至前端控制器 DispatcherServlet
2. DispatcherServlet 收到請求調用處理器映射器 HandlerMapping。
3. 處理器映射器根據請求 url 找到具體的處理器，生成處理器執行鏈 HandlerExecutionChain (包括處理器對象和處理器攔截器) 一并返回給 DispatcherServlet。
4. DispatcherServlet 根據處理器 Handler 獲取處理器适配器 HandlerAdapter 執行 HandlerAdapter 處理一系列的操作，如：參數封裝，數據格式轉換，數據驗證等操作
5. 執行處理器 Handler (Controller，也叫頁面控制器)。
6. Handler 執行完成返回 ModelAndView
7. HandlerAdapter 將 Handler 執行結果 ModelAndView 返回到 DispatcherServlet
8. DispatcherServlet 將 ModelAndView 傳給 ViewReslover 視圖解析器
9. ViewReslover 解析後返回具體 View
10. DispatcherServlet 對 View 進行渲染視圖（即將模型數據 model 填充至視圖中）。
11. DispatcherServlet 響應用戶。

### [#](https://xiaolincoding.com/interview/spring.html#handlermapping-%E5%92%8C-handleradapter%E6%9C%89%E4%BA%86%E8%A7%A3%E5%90%97)Handlermapping 和 handleradapter 有了解嗎？

HandlerMapping：

- **作用**：HandlerMapping 負責將請求映射到處理器（Controller）。
- **功能**：根據請求的 URL、請求參數等信息，找到處理請求的 Controller。
- **類型**：Spring 提供了多種 HandlerMapping 實現，如 BeanNameUrlHandlerMapping、RequestMappingHandlerMapping 等。
- **工作流程**：根據請求信息确定要請求的處理器 (Controller)。HandlerMapping 可以根據 URL、請求參數等規則确定對應的處理器。

HandlerAdapter：

- **作用**：HandlerAdapter 負責調用處理器 (Controller) 來處理請求。
- **功能**：處理器 (Controller) 可能有不同的接口類型（Controller 接口、HttpRequestHandler 接口等），HandlerAdapter 根據處理器的類型來選擇合适的方法來調用處理器。
- **類型**：Spring 提供了多個 HandlerAdapter 實現，用於适配不同類型的處理器。
- **工作流程**：根據處理器的接口類型，選擇相應的 HandlerAdapter 來調用處理器。

工作流程：

1. 當客戶端發送請求時，HandlerMapping 根據請求信息找到對應的處理器 (Controller)。
2. HandlerAdapter 根據處理器的類型選擇合适的方法來調用處理器。
3. 處理器執行相應的業務邏輯，生成 ModelAndView。
4. HandlerAdapter 將處理器的執行結果包裝成 ModelAndView。
5. 視圖解析器根據 ModelAndView 找到對應的視圖進行渲染。
6. 將渲染後的視圖返回給客戶端。

HandlerMapping 和 HandlerAdapter 協同工作，通過將請求映射到處理器，并調用處理器來處理請求，實現了請求處理的流程。它們的靈活性使得在 Spring MVC 中可以支持多種處理器和處理方式，提高了框架的擴展性和适應性。

## [#](https://xiaolincoding.com/interview/spring.html#springboot)SpringBoot

### [#](https://xiaolincoding.com/interview/spring.html#%E4%B8%BA%E4%BB%80%E4%B9%88%E4%BD%BF%E7%94%A8springboot)為什麽使用 springboot

- 簡化開發：Spring Boot 通過提供一系列的開箱即用的組件和自動配置，簡化了項目的配置和開發過程，開發人員可以更專注於業務邏輯的實現，而不需要花費過多時間在繁瑣的配置上。
- 快速啓動：Spring Boot 提供了快速的應用程序啓動方式，可通過内嵌的 Tomcat、Jetty 或 Undertow 等容器快速啓動應用程序，無需額外的部署步驟，方便快捷。
- 自動化配置：Spring Boot 通過自動配置功能，根據項目中的依賴關系和約定俗成的規則來配置應用程序，減少了配置的複雜性，使開發者更容易實現應用的最佳實踐。

### [#](https://xiaolincoding.com/interview/spring.html#springboot%E6%AF%94spring%E5%A5%BD%E5%9C%A8%E5%93%AA%E9%87%8C)SpringBoot 比 Spring 好在哪裏

- Spring Boot 提供了自動化配置，大大簡化了項目的配置過程。通過約定優於配置的原則，很多常用的配置可以自動完成，開發者可以專注於業務邏輯的實現。
- Spring Boot 提供了快速的項目啓動器，通過引入不同的 Starter，可以快速集成常用的框架和庫（如數據庫、消息隊列、Web 開發等），極大地提高了開發效率。
- Spring Boot 默認集成了多種内嵌服務器（如 Tomcat、Jetty、Undertow），無需額外配置，即可將應用打包成可執行的 JAR 文件，方便部署和運行。

### [#](https://xiaolincoding.com/interview/spring.html#springboot%E7%94%A8%E5%88%B0%E5%93%AA%E4%BA%9B%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)SpringBoot 用到哪些設計模式？

- **代理模式**：Spring 的 AOP 通過動态代理實現方法級别的切面增強，有靜态和動态兩種代理方式，采用動态代理方式。
- **策略模式**：Spring AOP 支持 JDK 和 Cglib 兩種動态代理實現方式，通過策略接口和不同策略類，運行時動态選擇，其創建一般通過工廠方法實現。
- **裝飾器模式**：Spring 用 TransactionAwareCacheDecorator 解決緩存與數據庫事務問題增加對事務的支持。
- **單例模式**：Spring Bean 默認是單例模式，通過單例注冊表（如 HashMap）實現。
- **簡單工廠模式**：Spring 中的 BeanFactory 是簡單工廠模式的體現，通過工廠類方法獲取 Bean 實例。
- **工廠方法模式**：Spring 中的 FactoryBean 體現工廠方法模式，為不同産品提供不同工廠。
- **觀察者模式**：Spring 觀察者模式包含 Event 事件、Listener 監聽者、Publisher 發送者，通過定義事件、監聽器和發送者實現，觀察者注冊在 ApplicationContext 中，消息發送由 ApplicationEventMulticaster 完成。
- **模闆模式**：Spring Bean 的創建過程涉及模闆模式，體現擴展性，類似 Callback 回調實現方式。
- **适配器模式**：Spring MVC 中針對不同方式定義的 Controller，利用适配器模式統一函數定義，定義了統一接口 HandlerAdapter 及對應适配器類。

### [#](https://xiaolincoding.com/interview/spring.html#%E6%80%8E%E4%B9%88%E7%90%86%E8%A7%A3springboot%E4%B8%AD%E7%9A%84%E7%BA%A6%E5%AE%9A%E5%A4%A7%E4%BA%8E%E9%85%8D%E7%BD%AE)怎麽理解 SpringBoot 中的約定大於配置

理解 Spring Boot 中的 “約定大於配置” 原則，可以從以下幾個方面來解釋：

- **自動化配置**：Spring Boot 提供了大量的自動化配置，通過分析項目的依賴和環境，自動配置應用程序的行為。開發者無需顯式地配置每個細節，大部分常用的配置都已經預設好了。例如，Spring Boot 可以根據項目中引入的數據庫依賴自動配置數據源。
- **默認配置**：Spring Boot 在沒有明确配置的情況下，會使用合理的默認值來初始化應用程序。這種默認行為使得開發者可以專注於核心業務邏輯，而無需關心每個細節的配置。
- **約定優於配置**：Spring Boot 遵循了約定優於配置的設計哲學，即通過約定好的方式來提供默認行為，減少開發者需要做出的決策。例如，約定了項目結構、Bean 命名規範等，使得開發者可以更快地上手并保持團隊間的一致性。

Spring Boot 的 “約定大於配置” 原則是一種設計理念，通過減少配置和提供合理的默認值，使得開發者可以更快速地構建和部署應用程序，同時降低了入門門檻和維護成本。

Spring Boot 通過「自動化配置」和「起步依賴」實現了約定大於配置的特性。

- 自動化配置：Spring Boot 根據項目的依賴和環境自動配置應用程序，無需手動配置大量的 XML 或 Java 配置文件。例如，如果項目引入了 Spring Web MVC 依賴，Spring Boot 會自動配置一個基本的 Web 應用程序上下文。
- 起步依賴：Spring Boot 提供了一系列起步依賴，這些依賴包含了常用的框架和功能，可以幫助開發者快速搭建項目。通過引入适合項目需求的起步依賴，開發者可以

### [#](https://xiaolincoding.com/interview/spring.html#springboot%E7%9A%84%E9%A1%B9%E7%9B%AE%E7%BB%93%E6%9E%84%E6%98%AF%E6%80%8E%E4%B9%88%E6%A0%B7%E7%9A%84)SpringBoot 的項目結構是怎麽樣的？

一個正常的企業項目裏一種通用的項目結構和代碼層級劃分的指導意見。按這《阿裏巴巴 Java 開發手冊》時本書上說的，一般分為如下幾層：

![[IMG-Spring 面试题-20250807203912143.png]]

- 開放接口層：可直接封裝 Service 接口暴露成 RPC 接口；通過 Web 封裝成 http 接口；網關控制層等。
    
- 終端顯示層：各個端的模闆渲染并執行顯示的層。當前主要是 velocity 渲染，JS 渲染，JSP 渲染，移動端展示等。
    
- Web 層：主要是對訪問控制進行轉發，各類基本參數校驗，或者不複用的業務簡單處理等。
    
- Service 層：相對具體的業務邏輯服務層。
    
- Manager 層：通用業務處理層，它有如下特征
    
- 1）對第三方平台封裝的層，預處理返回結果及轉化異常信息，适配上層接口。
    
- 2）對 Service 層通用能力的下沉，如緩存方案、中間件通用處理。
    
- 3）與 DAO 層交互，對多個 DAO 的組合複用。
    
- DAO 層：數據訪問層，與底層 MySQL、Oracle、Hbase、OceanBase 等進行數據交互。
    
- 第三方服務：包括其它部門 RPC 服務接口，基礎平台，其它公司的 HTTP 接口，如淘寶開放平台、支付寶付款服務、高德地圖服務等。
    
- 外部接口：外部（應用）數據存儲服務提供的接口，多見於數據遷移場景中。
    

如果從一個用戶訪問一個網站的情況來看，對應着上面的項目代碼結構來分析，可以貫穿整個代碼分層：

![[IMG-Spring 面试题-20250807203914034.png]]

對應代碼目錄的流轉邏輯就是：

![[IMG-Spring 面试题-20250807203916041.png]]

所以，以後每當我們拿到一個新的項目到手時，隻要按照這個思路去看别人項目的代碼，應該基本都是能理得順的。

### [#](https://xiaolincoding.com/interview/spring.html#springboot%E8%87%AA%E5%8A%A8%E8%A3%85%E9%85%8D%E5%8E%9F%E7%90%86%E6%98%AF%E4%BB%80%E4%B9%88)SpringBoot 自動裝配原理是什麽？

> 什麽是自動裝配？

SpringBoot 的自動裝配原理是基於 Spring Framework 的條件化配置和 @EnableAutoConfiguration 注解實現的。這種機制允許開發者在項目中引入相關的依賴，SpringBoot 將根據這些依賴自動配置應用程序的上下文和功能。

SpringBoot 定義了一套接口規範，這套規範規定：SpringBoot 在啓動時會掃描外部引用 jar 包中的 META-INF/spring.factories 文件，將文件中配置的類型信息加載到 Spring 容器（此處涉及到 JVM 類加載機制與 Spring 的容器知識），并執行類中定義的各種操作。對於外部 jar 來說，隻需要按照 SpringBoot 定義的標準，就能將自己的功能裝置進 SpringBoot。

通俗來講，自動裝配就是通過注解或一些簡單的配置就可以在 SpringBoot 的幫助下開啓和配置各種功能，比如數據庫訪問、Web 開發。

> SpringBoot 自動裝配原理

首先點進 `@SpringBootApplication` 注解的内部

![[IMG-Spring 面试题-20250807203918444.png]]

接下來將逐個解釋這些注解的作用：

- `@Target({ElementType.TYPE})`: 該注解指定了這個注解可以用來標記在類上。在這個特定的例子中，這表示該注解用於標記配置類。
- `@Retention(RetentionPolicy.RUNTIME)`: 這個注解指定了注解的生命周期，即在運行時保留。這是因為 Spring Boot 在運行時掃描類路徑上的注解來實現自動配置，所以這裏使用了 RUNTIME 保留策略。
- `@Documented`: 該注解表示這個注解應該被包含在 Java 文檔中。它是用於生成文檔的標記，使開發者能夠看到這個注解的相關信息。
- `@Inherited`: 這個注解指示一個被標注的類型是被繼承的。在這個例子中，它表明這個注解可以被繼承，如果一個類繼承了帶有這個注解的類，它也會繼承這個注解。
- `@SpringBootConfiguration`: 這個注解表明這是一個 Spring Boot 配置類。如果點進這個注解内部會發現與標準的 @Configuration 沒啥區别，隻是為了表明這是一個專門用於 SpringBoot 的配置。
- `@EnableAutoConfiguration`: 這個注解是 Spring Boot 自動裝配的核心。它告訴 Spring oot 啓用自動配置機制，根據項目的依賴和配置自動配置應用程序的上下文。通過這個注解，SpringBoot 將嘗試根據類路徑上的依賴自動配置應用程序。
- `@ComponentScan`: 這個注解用於配置組件掃描的規則。在這裏，它告訴 SpringBoot 在指定的包及其子包中查找組件，這些組件包括被注解的類、@Component 注解的類等。其中的 excludeFilters 參數用於指定排除哪些組件，這裏使用了兩個自定義的過濾器，分别是 TypeExcludeFilter 和 AutoConfigurationExcludeFilter。

`@EnableAutoConfiguration` 這個注解是實現自動裝配的核心注解

![[IMG-Spring 面试题-20250807203921137.png]]

- @AutoConfigurationPackage，將項目 src 中 main 包下的所有組件注冊到容器中，例如標注了 Component 注解的類等
- @Import ({AutoConfigurationImportSelector.class})，是自動裝配的核心，接下來分析一下這個注解

AutoConfigurationImportSelector 是 Spring Boot 中一個重要的類，它實現了 ImportSelector 接口，用於實現自動配置的選擇和導入。具體來說，它通過分析項目的類路徑和條件來決定應該導入哪些自動配置類。

代碼態多，選取部分主要功能的代碼：

```
public class AutoConfigurationImportSelector implements DeferredImportSelector, BeanClassLoaderAware,
		ResourceLoaderAware, BeanFactoryAware, EnvironmentAware, Ordered {
    
    // ... （其他方法和屬性）

  // 獲取所有符合條件的類的全限定類名，例如RedisTemplate的全限定類名(org.springframework.data.redis.core.RedisTemplate;)，這些類需要被加載到 IoC 容器中。
	@Override
	public String[] selectImports(AnnotationMetadata annotationMetadata) {
		// 掃描類路徑上的 META-INF/spring.factories 文件，獲取所有實現了 AutoConfiguration 接口的自動配置類
		List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes);

		// 過濾掉不滿足條件的自動配置類，比如一些自動裝配類
		configurations = filter(configurations, annotationMetadata, attributes);

		// 排序自動配置類，根據 @AutoConfigureOrder 和 @AutoConfigureAfter/@AutoConfigureBefore 注解指定的順序
		sort(configurations, annotationMetadata, attributes);

		// 將滿足條件的自動配置類的類名數組返回，這些類將被導入到應用程序上下文中
		return StringUtils.toStringArray(configurations);
	}

	// ... （其他方法）
	protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
		// 獲取自動配置類的候選列表，從 META-INF/spring.factories 文件中讀取
		// 通過類加載器加載所有候選類
		List<String> configurations = SpringFactoriesLoader.loadFactoryNames(getSpringFactoriesLoaderFactoryClass(),
				getBeanClassLoader());

		// 過濾出實現了 AutoConfiguration 接口的自動配置類
		configurations = configurations.stream()
				.filter(this::isEnabled)
				.collect(Collectors.toList());

		// 對於 Spring Boot 1.x 版本，還需要添加 spring-boot-autoconfigure 包中的自動配置類
		// configurations.addAll(getAutoConfigEntry(getAutoConfigurationEntry(metadata)));
		return configurations;
	}

	// ... （其他方法）
	protected List<String> filter(List<String> configurations, AnnotationMetadata metadata,
			AnnotationAttributes attributes) {
		// 使用條件判斷機制，過濾掉不滿足條件的自動配置類
		configurations = configurations.stream()
				.filter(configuration -> isConfigurationCandidate(configuration, metadata, attributes))
				.collect(Collectors.toList());
		return configurations;
	}

	// ... （其他方法）
	protected void sort(List<String> configurations, AnnotationMetadata metadata,
			AnnotationAttributes attributes) {
		// 根據 @AutoConfigureOrder 和 @AutoConfigureAfter/@AutoConfigureBefore 注解指定的順序對自動配置類進行排序
		configurations.sort((o1, o2) -> {
			int i1 = getAutoConfigurationOrder(o1, metadata, attributes);
			int i2 = getAutoConfigurationOrder(o2, metadata, attributes);
			return Integer.compare(i1, i2);
		});
	}
  
  	// ... （其他方法）

}
```

梳理一下，以下是 `AutoConfigurationImportSelector` 的主要工作：

- 掃描類路徑：在應用程序啓動時，`AutoConfigurationImportSelector` 會掃描類路徑上的 `META-INF/spring.factories` 文件，這個文件中包含了各種 Spring 配置和擴展的定義。在這裏，它會查找所有實現了 `AutoConfiguration` 接口的類，具體的實現為 `getCandidateConfigurations` 方法。
    
- 條件判斷：對於每一個發現的自動配置類，`AutoConfigurationImportSelector` 會使用條件判斷機制（通常是通過 `@ConditionalOnXxx` 注解）來确定是否滿足導入條件。這些條件可以是配置屬性、類是否存在、Bean 是否存在等等。
    
- 根據條件導入自動配置類：滿足條件的自動配置類將被導入到應用程序的上下文中。這意味着它們會被實例化并應用於應用程序的配置。
    

### [#](https://xiaolincoding.com/interview/spring.html#%E8%AF%B4%E5%87%A0%E4%B8%AA%E5%90%AF%E5%8A%A8%E5%99%A8-starter)說幾個啓動器（starter)？

- **spring-boot-starter-web**：這是最常用的起步依賴之一，它包含了 Spring MVC 和 Tomcat 嵌入式服務器，用於快速構建 Web 應用程序。
- **spring-boot-starter-security**：提供了 Spring Security 的基本配置，幫助開發者快速實現應用的安全性，包括認證和授權功能。
- **mybatis-spring-boot-starter**：這個 Starter 是由 MyBatis 團隊提供的，用於簡化在 Spring Boot 應用中集成 MyBatis 的過程。它自動配置了 MyBatis 的相關組件，包括 SqlSessionFactory、MapperScannerConfigurer 等，使得開發者能夠快速地開始使用 MyBatis 進行數據庫操作。
- **spring-boot-starter-data-jpa** 或 **spring-boot-starter-jdbc**：如果使用的是 Java Persistence API (JPA) 進行數據庫操作，那麽應該使用 spring-boot-starter-data-jpa。這個 Starter 包含了 Hibernate 等 JPA 實現以及數據庫連接池等必要的庫，可以讓你輕松地與 MySQL 數據庫進行交互。你需要在 application.properties 或 application.yml 中配置 MySQL 的連接信息。如果傾向於直接使用 JDBC 而不通過 JPA，那麽可以使用 spring-boot-starter-jdbc，它提供了基本的 JDBC 支持。
- **spring-boot-starter-data-redis**：用於集成 Redis 緩存和數據存儲服務。這個 Starter 包含了與 Redis 交互所需的客戶端（默認是 Jedis 客戶端，也可以配置為 Lettuce 客戶端），以及 Spring Data Redis 的支持，使得在 Spring Boot 應用中使用 Redis 變得非常便捷。同樣地，需要在配置文件中設置 Redis 服務器的連接詳情。
- **spring-boot-starter-test**：包含了單元測試和集成測試所需的庫，如 JUnit, Spring Test, AssertJ 等，便於進行測試驅動開發 (TDD)。

### [#](https://xiaolincoding.com/interview/spring.html#%E5%86%99%E8%BF%87springboot-starter%E5%90%97)寫過 SpringBoot starter 嗎？

> 步驟 1: 創建 Maven 項目

首先，需要創建一個新的 Maven 項目。在 pom.xml 中添加 Spring Boot 的 starter parent 和一些必要的依賴。例如：

```
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.7.0</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```

> 步驟 2: 添加自動配置

在 src/main/resources/META-INF/spring.factories 中添加自動配置的元數據。例如：

```
org.springframework.boot.autoconfigure.EnableAutoConfiguration = com.example.starter.MyAutoConfiguration
```

然後，創建 MyAutoConfiguration 類，該類需要 @Configuration 和 @EnableConfigurationProperties 注解。@EnableConfigurationProperties 用於啓用你定義的配置屬性類。

```
@Configuration
@EnableConfigurationProperties(MyProperties.class)
public class MyAutoConfiguration {

    @Autowired
    private MyProperties properties;

    @Bean
    public MyService myService() {
        return new MyServiceImpl(properties);
    }
}
```

> 步驟 3: 創建配置屬性類

創建一個配置屬性類，使用 @ConfigurationProperties 注解來綁定配置文件中的屬性。

```
@ConfigurationProperties(prefix = "my")
public class MyProperties {
    private String name;
    // getters and setters
}
```

> 步驟 4: 創建服務和控制器

創建一個服務類和服務實現類，以及一個控制器來展示你的 starter 的功能。

```
@Service
public interface MyService {
    String getName();
}

@Service
public class MyServiceImpl implements MyService {
    private final MyProperties properties;

    public MyServiceImpl(MyProperties properties) {
        this.properties = properties;
    }

    @Override
    public String getName() {
        return properties.getName();
    }
}

@RestController
public class MyController {
    private final MyService myService;

    public MyController(MyService myService) {
        this.myService = myService;
    }

    @GetMapping("/name")
    public String getName() {
        return myService.getName();
    }
}
```

> 步驟 5: 發布 Starter

將你的 starter 發布到 Maven 倉庫，無論是私有的還是公共的，如 Nexus 或 Maven Central。

> 步驟 6: 使用 Starter

在你的主應用的 pom.xml 中添加你的 starter 依賴，然後在 application.yml 或 application.properties 中配置你的屬性。

```
my:
    name: Hello World
```

### [#](https://xiaolincoding.com/interview/spring.html#springboot%E9%87%8C%E9%9D%A2%E6%9C%89%E5%93%AA%E4%BA%9B%E9%87%8D%E8%A6%81%E7%9A%84%E6%B3%A8%E8%A7%A3-%E8%BF%98%E6%9C%89%E4%B8%80%E4%B8%AA%E9%85%8D%E7%BD%AE%E7%9B%B8%E5%85%B3%E7%9A%84%E6%B3%A8%E8%A7%A3%E6%98%AF%E5%93%AA%E4%B8%AA)SpringBoot 裏面有哪些重要的注解？還有一個配置相關的注解是哪個？

Spring Boot 中一些常用的注解包括：

- **@SpringBootApplication**：用於標注主應用程序類，標識一個 Spring Boot 應用程序的入口點，同時啓用自動配置和組件掃描。
- **@Controller**：標識控制器類，處理 HTTP 請求。
- **@RestController**：結合 @Controller 和 @ResponseBody，返回 RESTful 風格的數據。
- **@Service**：標識服務類，通常用於標記業務邏輯層。
- **@Repository**：標識數據訪問組件，通常用於標記數據訪問層。
- **@Component**：通用的 Spring 組件注解，表示一個受 Spring 管理的組件。
- **@Autowired**：用於自動裝配 Spring Bean。
- **@Value**：用於注入配置屬性值。
- **@RequestMapping**：用於映射 HTTP 請求路徑到 Controller 的處理方法。
- **@GetMapping**、@PostMapping、@PutMapping、@DeleteMapping：簡化 @RequestMapping 的 GET、POST、PUT 和 DELETE 請求。

另外，一個與配置相關的重要注解是：

- **@Configuration**：用於指定一個類為配置類，其中定義的 bean 會被 Spring 容器管理。通常與 @Bean 配合使用，@Bean 用於聲明一個 Bean 實例，由 Spring 容器進行管理。

### [#](https://xiaolincoding.com/interview/spring.html#springboot%E6%80%8E%E4%B9%88%E5%BC%80%E5%90%AF%E4%BA%8B%E5%8A%A1)springboot 怎麽開啓事務？

在 Spring Boot 中開啓事務非常簡單，隻需在服務層的方法上添加 `@Transactional` 注解即可。

例如，假設我們有一個 UserService 接口，其中有一個保存用戶的方法 saveUser ()：

```
public interface UserService {
    void saveUser(User user);
}
```

我們希望在這個方法中開啓事務，隻需在該方法上添加 `@Transactional` 注解，如下所示：

```
public class UserServiceImpl implements UserService {

    @Autowired
    private UserRepository userRepository;

    @Override
    @Transactional
    public void saveUser(User user) {
        userRepository.save(user);
    }
}
```

這樣，當調用 saveUser () 方法時，Spring 就會自動為該方法開啓一個事務。如果方法執行成功，事務會自動提交；如果方法執行失敗，事務會自動回滾。

## [#](https://xiaolincoding.com/interview/spring.html#mybatis)Mybatis

### [#](https://xiaolincoding.com/interview/spring.html#%E4%B8%8E%E4%BC%A0%E7%BB%9F%E7%9A%84jdbc%E7%9B%B8%E6%AF%94-mybatis%E7%9A%84%E4%BC%98%E7%82%B9)與傳統的 JDBC 相比，MyBatis 的優點？

- 基於 SQL 語句編程，相當靈活，不會對應用程序或者數據庫的現有設計造成任 何影響，SQL 寫在 XML 裏，解除 sql 與程序代碼的耦合，便於統一管理；提供 XML 標簽，支持編寫動态 SQL 語句，并可重用。
- 與 JDBC 相比，減少了 50% 以上的代碼量，消除了 JDBC 大量冗餘的代碼，不 需要手動開關連接；
- 很好的與各種數據庫兼容，因為 MyBatis 使用 JDBC 來連接數據庫，所以隻要 JDBC 支持的數據庫 MyBatis 都支持。
- 能夠與 Spring 很好的集成，開發效率高
- 提供映射標簽，支持對象與數據庫的 ORM 字段關系映射；提供對象關系映射 標簽，支持對象關系組件維護。

### [#](https://xiaolincoding.com/interview/spring.html#%E8%BF%98%E8%AE%B0%E5%BE%97jdbc%E8%BF%9E%E6%8E%A5%E6%95%B0%E6%8D%AE%E5%BA%93%E7%9A%84%E6%AD%A5%E9%AA%A4%E5%90%97)還記得 JDBC 連接數據庫的步驟嗎？

使用 Java JDBC 連接數據庫的一般步驟如下：

1. **加載數據庫驅動程序**：在使用 JDBC 連接數據庫之前，需要加載相應的數據庫驅動程序。可以通過 Class.forName ("com.mysql.jdbc.Driver") 來加載 MySQL 數據庫的驅動程序。不同數據庫的驅動類名會有所不同。
2. **建立數據庫連接**：使用 DriverManager 類的 getConnection (url, username, password) 方法來連接數據庫，其中 url 是數據庫的連接字符串（包括數據庫類型、主機、端口等）、username 是數據庫用戶名，password 是密碼。
3. **創建 Statement 對象**：通過 Connection 對象的 createStatement () 方法創建一個 Statement 對象，用於執行 SQL 查詢或更新操作。
4. **執行 SQL 查詢或更新操作**：使用 Statement 對象的 executeQuery (sql) 方法來執行 SELECT 查詢操作，或者使用 executeUpdate (sql) 方法來執行 INSERT、UPDATE 或 DELETE 操作。
5. **處理查詢結果**：如果是 SELECT 查詢操作，通過 ResultSet 對象來處理查詢結果。可以使用 ResultSet 的 next () 方法遍曆查詢結果集，然後通過 getXXX () 方法獲取各個字段的值。
6. **關閉連接**：在完成數據庫操作後，需要逐級關閉數據庫連接相關對象，即先關閉 ResultSet，再關閉 Statement，最後關閉 Connection。

以下是一個簡單的示例代碼：

```
import java.sql.*;

public class Main {
    public static void main(String[] args) {
        try {
            // 加載數據庫驅動程序
            Class.forName("com.mysql.cj.jdbc.Driver");

            // 建立數據庫連接
            Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/mydatabase", "username", "password");

            // 創建 Statement 對象
            Statement statement = connection.createStatement();

            // 執行 SQL 查詢
            ResultSet resultSet = statement.executeQuery("SELECT * FROM mytable");

            // 處理查詢結果
            while (resultSet.next()) {
              // 處理每一行數據
            }

            // 關閉資源
            resultSet.close();
            statement.close();
            connection.close();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

請注意，在實際應用中，需要進行異常處理以确保資源的正确釋放，以及使用 try-with-resources 來簡化代碼和确保資源的及時關閉。

### [#](https://xiaolincoding.com/interview/spring.html#%E5%A6%82%E6%9E%9C%E9%A1%B9%E7%9B%AE%E4%B8%AD%E8%A6%81%E7%94%A8%E5%88%B0%E5%8E%9F%E7%94%9F%E7%9A%84mybatis%E5%8E%BB%E6%9F%A5%E8%AF%A2-%E8%AF%A5%E6%80%8E%E6%A0%B7%E5%86%99)如果項目中要用到原生的 mybatis 去查詢，該怎樣寫？

步驟概述：

1. **配置 MyBatis：** 在項目中配置 MyBatis 的數據源、SQL 映射文件等。
2. **創建實體類：** 創建用於映射數據庫表的實體類。
3. **編寫 SQL 映射文件：** 創建 XML 文件，定義 SQL 語句和映射關系。
4. **編寫 DAO 接口：** 創建 DAO 接口，定義數據庫操作的方法。
5. **編寫具體的 SQL 查詢語句：** 在 DAO 接口中定義查詢方法，并在 XML 文件中編寫對應的 SQL 語句。
6. **調用查詢方法：** 在服務層或控制層調用 DAO 接口中的方法進行查詢。

詳細步驟：

1. **配置 MyBatis：** 在配置文件中配置數據源、MyBatis 的 Mapper 文件位置等信息。
2. **創建實體類：** 創建與數據庫表對應的實體類，字段名和類型需與數據庫表保持一致。

```
public class User {
    private Long id;
    private String username;
    private String email;
    // Getters and setters
}
```

1. **編寫 SQL 映射文件：** 在 resources 目錄下創建 XML 文件，定義 SQL 語句和映射關系。

```
<!-- userMapper.xml -->
<mapper namespace="com.example.dao.UserMapper">
    <select id="selectUserById" resultType="com.example.model.User">
        SELECT * FROM users WHERE id = #{id}
    </select>
</mapper>
```

1. **編寫 DAO 接口：** 創建 DAO 接口，定義查詢方法。

```
public interface UserMapper {
    User selectUserById(Long id);
}
```

1. **編寫具體的 SQL 查詢語句：** 在 XML 文件中編寫對應的 SQL 語句。
2. **調用查詢方法：** 在服務層或控制層中調用 DAO 接口中的方法進行查詢。

```
// 在Service層中調用
User user = userMapper.selectUserById(1);
```

通過以上步驟，你可以利用原生的 MyBatis 框架來進行數據庫查詢操作。請确保配置正确、SQL 語句準确并與數據庫字段匹配，以确保查詢的準确性和高效性。

### [#](https://xiaolincoding.com/interview/spring.html#mybatis%E9%87%8C%E7%9A%84-%E5%92%8C-%E7%9A%84%E5%8C%BA%E5%88%AB)Mybatis 裏的 # 和 $ 的區别？

- Mybatis 在處理 #{} 時，會創建預編譯的 SQL 語句，將 SQL 中的 #{} 替換為？號，在執行 SQL 時會為預編譯 SQL 中的占位符（?）賦值，調用 PreparedStatement 的 set 方法來賦值，預編譯的 SQL 語句執行效率高，并且可以防止 SQL 注入，提供更高的安全性，适合傳遞參數值。
- Mybatis 在處理 ${} 時，隻是創建普通的 SQL 語句，然後在執行 SQL 語句時 MyBatis 將參數直接拼入到 SQL 裏，不能防止 SQL 注入，因為參數直接拼接到 SQL 語句中，如果參數未經過驗證、過濾，可能會導致安全問題。

### [#](https://xiaolincoding.com/interview/spring.html#mybatisplus%E5%92%8Cmybatis%E7%9A%84%E5%8C%BA%E5%88%AB)MybatisPlus 和 Mybatis 的區别？

MybatisPlus 是一個基於 MyBatis 的增強工具庫，旨在簡化開發并提高效率。以下是 MybatisPlus 和 MyBatis 之間的一些主要區别：

- **CRUD 操作**：MybatisPlus 通過繼承 BaseMapper 接口，提供了一系列内置的快捷方法，使得 CRUD 操作更加簡單，無需編寫重複的 SQL 語句。
- **代碼生成器**：MybatisPlus 提供了代碼生成器功能，可以根據數據庫表結構自動生成實體類、Mapper 接口以及 XML 映射文件，減少了手動編寫的工作量。
- **通用方法封裝**：MybatisPlus 封裝了許多常用的方法，如條件構造器、排序、分頁查詢等，簡化了開發過程，提高了開發效率。
- **分頁插件**：MybatisPlus 内置了分頁插件，支持各種數據庫的分頁查詢，開發者可以輕松實現分頁功能，而在傳統的 MyBatis 中，需要開發者自己手動實現分頁邏輯。
- **多租戶支持**：MybatisPlus 提供了多租戶的支持，可以輕松實現多租戶數據隔離的功能。
- **注解支持**：MybatisPlus 引入了更多的注解支持，使得開發者可以通過注解來配置實體與數據庫表之間的映射關系，減少了 XML 配置文件的編寫。

### [#](https://xiaolincoding.com/interview/spring.html#mybatis%E8%BF%90%E7%94%A8%E4%BA%86%E5%93%AA%E4%BA%9B%E5%B8%B8%E8%A7%81%E7%9A%84%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)MyBatis 運用了哪些常見的設計模式？

- 建造者模式（Builder），如：SqlSessionFactoryBuilder、XMLConfigBuilder、XMLMapperBuilder、XMLStatementBuilder、CacheBuilder 等；
- 工廠模式，如：SqlSessionFactory、ObjectFactory、MapperProxyFactory；
- 單例模式，例如 ErrorContext 和 LogFactory；
- 代理模式，Mybatis 實現的核心，比如 MapperProxy、ConnectionLogger，用的 jdk 的動态代理；還有 executor.loader 包使用了 cglib 或者 javassist 達到延遲加載的效果；
- 組合模式，例如 SqlNode 和各個子類 ChooseSqlNode 等；
- 模闆方法模式，例如 BaseExecutor 和 SimpleExecutor，還有 BaseTypeHandler 和所有的子類例如 IntegerTypeHandler；
- 适配器模式，例如 Log 的 Mybatis 接口和它對 jdbc、log4j 等各種日志框架的适配實現；
- 裝飾者模式，例如 Cache 包中的 cache.decorators 子包中等各個裝飾者的實現；
- 叠代器模式，例如叠代器模式 PropertyTokenizer；

## [#](https://xiaolincoding.com/interview/spring.html#springcloud)SpringCloud

### [#](https://xiaolincoding.com/interview/spring.html#%E4%BA%86%E8%A7%A3springcloud%E5%90%97-%E8%AF%B4%E4%B8%80%E4%B8%8B%E4%BB%96%E5%92%8Cspringboot%E7%9A%84%E5%8C%BA%E5%88%AB)了解 SpringCloud 嗎，說一下他和 SpringBoot 的區别

Spring Boot 是用於構建單個 Spring 應用的框架，而 Spring Cloud 則是用於構建分布式系統中的微服務架構的工具，Spring Cloud 提供了服務注冊與發現、負載均衡、斷路器、網關等功能。

兩者可以結合使用，通過 Spring Boot 構建微服務應用，然後用 Spring Cloud 來實現微服務架構中的各種功能。

### [#](https://xiaolincoding.com/interview/spring.html#%E7%94%A8%E8%BF%87%E5%93%AA%E4%BA%9B%E5%BE%AE%E6%9C%8D%E5%8A%A1%E7%BB%84%E4%BB%B6)用過哪些微服務組件？

![[IMG-Spring 面试题-20250807203923701.png]]

微服務常用的組件：

- **注冊中心**：注冊中心是微服務架構最核心的組件。它起到的作用是對新節點的注冊與狀态維護，**解決了「如何發現新節點以及檢查各節點的運行狀态的問題」**。微服務節點在啓動時會將自己的服務名稱、IP、端口等信息在注冊中心登記，注冊中心會定時檢查該節點的運行狀态。注冊中心通常會采用心跳機制最大程度保證已登記過的服務節點都是可用的。
- **負載均衡**：負載均衡**解決了「如何發現服務及負載均衡如何實現的問題」**，通常微服務在互相調用時，并不是直接通過 IP、端口進行訪問調用。而是先通過服務名在注冊中心查詢該服務擁有哪些節點，注冊中心將該服務可用節點列表返回給服務調用者，這個過程叫服務發現，因服務高可用的要求，服務調用者會接收到多個節點，必須要從中進行選擇。因此服務調用者一端必須内置負載均衡器，通過負載均衡策略選擇合适的節點發起實質性的通信請求。
- **服務通信**：服務通信組件解決了「**服務間如何進行消息通信的問題**」，服務間通信采用輕量級協議，通常是 HTTP RESTful 風格。但因為 RESTful 風格過於靈活，必須加以約束，通常應用時對其封裝。例如在 SpringCloud 中就提供了 Feign 和 RestTemplate 兩種技術屏蔽底層的實現細節，所有開發者都是基於封裝後統一的 SDK 進行開發，有利於團隊間的相互合作。
- **配置中心**：配置中心主要解決了「**如何集中管理各節點配置文件的問題**」，在微服務架構下，所有的微服務節點都包含自己的各種配置文件，如 jdbc 配置、自定義配置、環境配置、運行參數配置等。要知道有的微服務可能可能有幾十個節點，如果將這些配置文件分散存儲在節點上，發生配置更改就需要逐個節點調整，將給運維人員帶來巨大的壓力。配置中心便由此而生，通過部署配置中心服務器，將各節點配置文件從服務中剝離，集中轉存到配置中心。一般配置中心都有 UI 界面，方便實現大規模集群配置調整。
- **集中式日志管理**：集中式日志主要是解決了「**如何收集各節點日志并統一管理的問題**」。微服務架構默認將應用日志分别保存在部署節點上，當需要對日志數據和操作數據進行數據分析和數據統計時，必須收集所有節點的日志數據。那麽怎麽高效收集所有節點的日志數據呢？業内常見的方案有 ELK、EFK。通過搭建獨立的日志收集系統，定時抓取各節點增量日志形成有效的統計報表，為統計和分析提供數據支撐。
- 分布式鏈路追蹤：分布式鏈路追蹤解決了「**如何直觀的了解各節點間的調用鏈路的問題**」。系統中一個複雜的業務流程，可能會出現連續調用多個微服務，我們需要了解完整的業務邏輯涉及的每個微服務的運行狀态，通過可視化鏈路圖展現，可以幫助開發人員快速分析系統瓶頸及出錯的服務。
- **服務保護**：服務保護主要是解決了「**如何對系統進行鏈路保護，避免服務雪崩的問題**」。在業務運行時，微服務間互相調用支撐，如果某個微服務出現高延遲導致線程池滿載，或是業務處理失敗。這裏就需要引入服務保護組件來實現高延遲服務的快速降級，避免系統崩潰。

SpringCloud Alibaba 實現的微服務架構：

![[IMG-Spring 面试题-20250807203926403.png]]

- SpringCloud Alibaba 中使用 **Alibaba Nacos** 組件實現 **注冊中心**，Nacos 提供了一組簡單易用的特性集，可快速實現動态服務發現、服務配置、服務元數據及流量管理。
- SpringCloud Alibaba 使用 **Nacos 服務端均衡**實現負載均衡，與 Ribbon 在調用端負載不同，Nacos 是在服務發現的同時利用負載均衡返回服務節點數據。
- SpringCloud Alibaba 使用 **Netflix Feign** 和 **Alibaba Dubbo** 組件來實現服務通行，前者與 SpringCloud 采用了相同的方案，後者則是對自家的 **RPC 框架 Dubbo** 也給予支持，為服務間通信提供另一種選擇。
- SpringCloud Alibaba 在 **API 服務網關**組件中，使用與 SpringCloud 相同的組件，即： **SpringCloud Gateway**。
- SpringCloud Alibaba 在配置中心組件中使用 **Nacos 内置配置中心**，Nacos 内置的配置中心，可將配置信息**存儲保存在指定數據庫**中
- SpringCloud Alibaba 在原有的 **ELK 方案**外，還可以使用阿裏雲日志服務（LOG）實現日志集中式管理。
- SpringCloud Alibaba 在**分布式鏈路組件**中采用與 SpringCloud 相同的方案，即： **Sleuth/Zipkin Server**。
- SpringCloud Alibaba 使用 **Alibaba Sentinel** 實現系統保護，Sentinel 不僅功能更強大，實現系統保護比 Hystrix 更優雅，而且還擁有更好的 UI 界面。

### [#](https://xiaolincoding.com/interview/spring.html#%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1%E6%9C%89%E5%93%AA%E4%BA%9B%E7%AE%97%E6%B3%95)負載均衡有哪些算法？

- 簡單輪詢：將請求按順序分發給後端服務器上，不關心服務器當前的狀态，比如後端服務器的性能、當前的負載。
- 加權輪詢：根據服務器自身的性能給服務器設置不同的權重，將請求按順序和權重分發給後端服務器，可以讓性能高的機器處理更多的請求
- 簡單随機：將請求随機分發給後端服務器上，請求越多，各個服務器接收到的請求越平均
- 加權随機：根據服務器自身的性能給服務器設置不同的權重，將請求按各個服務器的權重随機分發給後端服務器
- 一致性哈希：根據請求的客戶端 ip、或請求參數通過哈希算法得到一個數值，利用該數值取模映射出對應的後端服務器，這樣能保證同一個客戶端或相同參數的請求每次都使用同一台服務器
- 最小活躍數：統計每台服務器上當前正在處理的請求數，也就是請求活躍數，將請求分發給活躍數最少的後台服務器

### [#](https://xiaolincoding.com/interview/spring.html#%E5%A6%82%E4%BD%95%E5%AE%9E%E7%8E%B0%E4%B8%80%E7%9B%B4%E5%9D%87%E8%A1%A1%E7%BB%99%E4%B8%80%E4%B8%AA%E7%94%A8%E6%88%B7)如何實現一直均衡給一個用戶？

可以通過「一致性哈希算法」來實現，根據請求的客戶端 ip、或請求參數通過哈希算法得到一個數值，利用該數值取模映射出對應的後端服務器，這樣能保證同一個客戶端或相同參數的請求每次都使用同一台服務器。

### [#](https://xiaolincoding.com/interview/spring.html#%E4%BB%8B%E7%BB%8D%E4%B8%80%E4%B8%8B%E6%9C%8D%E5%8A%A1%E7%86%94%E6%96%AD)介紹一下服務熔斷

服務熔斷是應對微服務雪崩效應的一種**鏈路保護機制，類似股市、保險絲**。

比如說，微服務之間的數據交互是通過遠程調用來完成的。服務 A 調用服務，服務 B 調用服務 c，某一時間鏈路上對服務 C 的調用響應時間過長或者服務 C 不可用，随着時間的增長，對服務 C 的調用也越來越多，然後服務 C 崩潰了，但是鏈路調用還在，對服務 B 的調用也在持續增多，然後服務 B 崩潰，随之 A 也崩潰，導致雪崩效應。

服務熔斷是應對雪崩效應的一種微服務鏈路保護機制。例如在高壓電路中，如果某個地方的電壓過高，熔斷器就會熔斷，對電路進行保護。同樣，在微服務架構中，熔斷機制也是起着類似的作用。當調用鏈路的某個微服務不可用或者響應時間態長時，會進行服務熔斷，不再有該節點微服務的調用，快速返回錯誤的響應信息。當檢測到該節點微服務調用響應正常後，恢複調用鏈路。

所以，服務熔斷的作用類似於我們家用的保險絲，當某服務出現不可用或響應超時的情況時，為了防止整個系統出現雪崩，暫時停止對該服務的調用。

在 Spring Cloud 框架裏，熔斷機制通過 Hystrix 實現。Hystrix 會監控微服務間調用的狀況，當失敗的調用到一定阈值，缺省是 5 秒内 20 次調用失敗，就會啓動熔斷機制。

### [#](https://xiaolincoding.com/interview/spring.html#%E4%BB%8B%E7%BB%8D%E4%B8%80%E4%B8%8B%E6%9C%8D%E5%8A%A1%E9%99%8D%E7%BA%A7)介紹一下服務降級

服務降級一般是指在服務器壓力劇增的時候，根據實際業務使用情況以及流量，對一些服務和頁面有策略的不處理或者用一種簡單的方式進行處理，從而**釋放服務器資源的資源以保證核心業務的正常高效運行。**

服務器的資源是有限的，而請求是無限的。在用戶使用即并發高峰期，會影響整體服務的性能，嚴重的話會導致宕機，以至於某些重要服務不可用。故高峰期為了保證核心功能服務的可用性，就需要對某些服務降級處理。可以理解為舍小保大

服務降級是從整個系統的負荷情況出發和考慮的，對某些負荷會比較高的情況，為了預防某些功能（業務場景）出現負荷過載或者響應慢的情況，在其内部暫時舍棄對一些非核心的接口和數據的請求，而直接返回一個提前準備好的 fallback（退路）錯誤處理信息。這樣，雖然提供的是一個有損的服務，但卻保證了整個系統的穩定性和可用性。