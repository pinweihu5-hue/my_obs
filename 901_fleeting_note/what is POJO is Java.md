[What is a POJO in Java? Almost EVERYONE Gets This Wrong - YouTube](https://www.youtube.com/watch?v=oqPiEc2zNb0)

related:
- [一次搞懂POJO、PO、DTO、VO、BO - HackMD](https://hackmd.io/@MonsterLee/HJyAdgRBB)

 沒有 implement, 沒有 extends, 沒有 annotation 就是 的 java class
 就是 dead simple obj


有時人們會跟 Java bean 混用
Java bean 的規則不同，如下
![[IMG-what is POJO is Java-20250805201304727.png|896]]


---

Java Bean 是一種符合特定規範的 Java 類別，它被設計用來簡化軟體開發中的程式碼複用和元件化。可以把它想像成軟體開發中的「樂高積木」，可以方便地組裝和重複使用。

**Java Bean 的主要特性：**

1. **無參數建構子 (No-argument Constructor):**  Java Bean 必須提供一個公共的、無參數的建構子。這是為了讓工具或框架可以透過反射機制來建立 Bean 的實例。

2. **屬性 (Properties):**  Java Bean 的屬性通常是私有的 (private)，並透過公共的 getter 和 setter 方法來訪問和修改。這些 getter 和 setter 方法必須遵循特定的命名規範，例如，對於名為 `name` 的屬性，其 getter 方法應為 `getName()`，setter 方法應為 `setName()`。  這也是 Java Bean 最重要的特性，透過 getter/setter 控制存取，確保資料的封裝性和一致性。

3. **序列化 (Serialization):**  Java Bean 應該實現 `java.io.Serializable` 介面。這使得 Bean 的狀態可以被儲存到檔案或透過網路傳輸。  這讓 Bean 的狀態可以被保存和恢復，方便在不同系統間傳遞或持久化儲存。

4. **事件 (Events, 選用):** Java Bean 可以支援事件，允許它在特定情況下通知其他元件。  雖然不是必要的，但事件機制讓 Bean 可以更靈活地與其他元件互動。


**簡單範例：**

```java
import java.io.Serializable;

public class Person implements Serializable {
    private String name;
    private int age;

    public Person() { // 無參數建構子
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}
```

**Java Bean 的用途：**

* **程式碼複用：**  可以將常用的功能封裝成 Java Bean，在不同的應用程式中重複使用。
* **元件化開發：**  可以將應用程式分解成多個 Java Bean，每個 Bean 負責一個特定的功能，簡化開發和維護。
* **視覺化開發工具：**  許多視覺化開發工具（例如 IDE）都支援 Java Bean，可以方便地拖曳和放置 Bean 來構建使用者介面。
* **框架整合：**  許多 Java 框架（例如 Spring）都使用 Java Bean 作為基本的組建塊。


**總結：**

Java Bean 是一種簡單而強大的元件模型，它促進了程式碼複用、簡化了開發流程，並增強了應用程式的可維護性。 雖然看似簡單，但它卻是許多 Java 框架和技術的基石。
