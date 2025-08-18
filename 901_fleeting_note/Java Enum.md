
Java Enum（列舉）是一種特殊的資料類型，用於定義一組固定的常數。它提供類型安全性和程式碼可讀性，比使用傳統的常數 (例如 `int` 或 `String`) 更好。

**定義 Enum：**

使用 `enum` 關鍵字來定義 Enum。

```java
enum Weekday {
    MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY
}
```

**使用 Enum：**

```java
public class EnumExample {
    public static void main(String[] args) {
        Weekday today = Weekday.MONDAY;

        if (today == Weekday.MONDAY) {
            System.out.println("今天是星期一");
        }

        switch (today) {
            case MONDAY:
                System.out.println("Start of the work week");
                break;
            case SATURDAY, SUNDAY:
                System.out.println("Weekend!");
                break;
            default:
                System.out.println("Weekday");
        }


        // 遍歷所有 Enum 常數
        for (Weekday day : Weekday.values()) {
            System.out.println(day);
        }

        // 取得 Enum 常數的序號 (從 0 開始)
        int ordinal = today.ordinal();
        System.out.println(ordinal); // 輸出 0

        // 透過名稱取得 Enum 常數
        Weekday wednesday = Weekday.valueOf("WEDNESDAY");
        System.out.println(wednesday); // 輸出 WEDNESDAY

    }
}
```

**Enum 的特性：**

* **類型安全:** 編譯器會檢查 Enum 類型的使用，避免類型錯誤。
* **可讀性:** 使用 Enum 可以使程式碼更易讀易懂，因為 Enum 常數的名稱比數字或字串常數更具語義。
* **可維護性:**  如果需要修改 Enum 常數的值，只需要修改 Enum 定義即可，不需要修改使用 Enum 的程式碼。
* **可以有建構子、方法和欄位:**  Enum 可以像類別一樣，擁有建構子、方法和欄位。

```java
enum Planet {
    MERCURY(3.303e+23, 2.4397e6),
    VENUS(4.869e+24, 6.0518e6),
    EARTH(5.976e+24, 6.37814e6),
    MARS(6.421e+23, 3.3972e6),
    JUPITER(1.9e+27, 7.1492e7),
    SATURN(5.688e+26, 6.0268e7),
    URANUS(8.686e+25, 2.5559e7),
    NEPTUNE(1.024e+26, 2.4746e7);

    private final double mass;   // in kilograms
    private final double radius; // in meters

    Planet(double mass, double radius) {
        this.mass = mass;
        this.radius = radius;
    }

    public double surfaceGravity() {
        return 6.67300E-11 * mass / (radius * radius);
    }

    public double surfaceWeight(double otherMass) {
        return otherMass * surfaceGravity();
    }
}
```

**何時使用 Enum：**

* **表示一組固定的常數：** 例如星期幾、月份、顏色、狀態等。
* **提高程式碼可讀性和可維護性：** 使用 Enum 可以使程式碼更清晰易懂，更容易維護。
* **需要類型安全：**  使用 Enum 可以避免類型錯誤。


**何時不應該使用 Enum：**

* **常數的值需要頻繁變更：**  如果常數的值經常變更，使用 Enum 可能會導致程式碼需要頻繁重新編譯。
* **需要大量的常數：**  如果常數的數量非常大，使用 Enum 可能會影響效能。  (但大多數情況下，Enum 的效能影響可以忽略不計)


**總結：**

Java Enum 是一種非常有用的資料類型，它可以提高程式碼的可讀性、可維護性和類型安全。  在表示一組固定的常數時，應該優先考慮使用 Enum。




另一個例子


![[IMG-Java Enum-20250805201155114.png]]

enum implcitly extends enum type, 有一些 method 可以用
like  .values 可以拿到全部
![[IMG-Java Enum-20250805201155901.png|845]]



可以有 ctor
然後給每一個 enum 屬性
![[IMG-Java Enum-20250805201156734.png|783]]


這樣用
![[IMG-Java Enum-20250805201157512.png|802]]