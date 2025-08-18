
`Cloneable` 接口在 Java 中是一個標記接口 (marker interface)，也就是說它沒有任何方法需要實現。
它的作用是標記一個類的實例可以被複製，允許使用 `Object.clone()` 方法創建對象的副本。

**運作機制:**

`Object.clone()` 方法是 protected 的，它創建對象的淺拷貝 (shallow copy)。  淺拷貝意味著：

* 新對象與原對象擁有不同的內存地址。
* 基本數據類型 (primitive types) 的字段會被複製到新對象。
* 引用類型 (reference types) 的字段在新對象中仍然指向原對象的成員變量所指向的對象。也就是說，原對象和新對象共享相同的引用類型成員變量。

如果一個類沒有實現 `Cloneable` 接口，直接調用 `Object.clone()` 方法會拋出 `CloneNotSupportedException`。

**如何使用:**

1. **實現 `Cloneable` 接口:**  讓你的類實現 `Cloneable` 接口。
2. **覆寫 `clone()` 方法:**  覆寫 `Object.clone()` 方法，並將其訪問修飾符改為 `public`。在覆寫的方法中，通常會先調用 `super.clone()`，然後根據需要進行深拷貝的處理。

**例子:**

```java
class Person implements Cloneable {
    String name;
    int age;
    Address address;

    public Person(String name, int age, Address address) {
        this.name = name;
        this.age = age;
        this.address = address;
    }

    @Override
    public Person clone() throws CloneNotSupportedException {
        // 淺拷貝
        // Person clonedPerson = (Person) super.clone();

        // 深拷貝
        Person clonedPerson = (Person) super.clone();
        clonedPerson.address = new Address(address.street, address.city);

        return clonedPerson;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", address=" + address +
                '}';
    }
}

class Address {
    String street;
    String city;

    public Address(String street, String city) {
        this.street = street;
        this.city = city;
    }

    @Override
    public String toString() {
        return "Address{" +
                "street='" + street + '\'' +
                ", city='" + city + '\'' +
                '}';
    }
}

public class Main {
    public static void main(String[] args) throws CloneNotSupportedException {
        Address address = new Address("Main St", "Anytown");
        Person person1 = new Person("John Doe", 30, address);
        Person person2 = person1.clone();

        System.out.println("person1: " + person1);
        System.out.println("person2: " + person2);

        person2.name = "Jane Doe";
        person2.address.street = "Other St"; // 修改 person2 的 address 也會影響 person1 的 address (淺拷貝)

        System.out.println("After modification:");
        System.out.println("person1: " + person1);
        System.out.println("person2: " + person2);
    }
}
```

**何時使用 `Cloneable`:**

* 需要創建對象的副本，並且希望避免複雜的構造器調用。
* 需要修改副本而不影響原對象。

**何時不應該使用 `Cloneable`:**

* 類包含可變的引用類型成員，且需要深拷貝時，`Cloneable` 的淺拷貝機制會帶來問題，需要自行實現深拷貝邏輯，這可能很複雜。
* 類的繼承體系複雜時，正確覆寫 `clone()` 方法變得困難。
* 使用拷貝構造函數或工廠方法通常是更清晰、更安全的選擇。  拷貝構造函數可以讓你精確控制如何複製對象，並且避免 `CloneNotSupportedException`。


**建議:**

優先考慮使用拷貝構造函數或工廠方法。  
如果一定要使用 `Cloneable`，務必理解淺拷貝和深拷貝的區別，並根據需要實現深拷貝邏輯。  仔細處理可變的引用類型成員，避免潛在的錯誤。


