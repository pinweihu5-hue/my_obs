在 Java 中，不可變值類（Immutable Value Class）是一種設計模式，指的是一種對象在創建後其狀態（屬性）無法被修改的類。這意味著一旦創建了這個類的實例，其屬性值就不能被改變。不可變類的特性使得它們在多線程環境中非常安全，因為不需要擔心對象的狀態會被其他線程修改。

不可變值類的特點

- 狀態不可變：一旦對象被創建，其屬性值就不能被改變。這通常是通過不提供任何修改方法（如 setter 方法）來實現的。
- 所有屬性為 final：不可變類的所有屬性通常都被聲明為 final，這樣可以確保它們在對象創建後不會被重新賦值。
- 通過構造函數初始化：所有的屬性值通常在構造函數中進行初始化，並且在對象創建後不再改變。
- 返回新對象：如果需要對對象的某個屬性進行修改，通常會返回一個新的對象，而不是修改原有對象。

不可變值類的示例
以下是一個簡單的不可變值類的示例：
```java
public final class ImmutablePoint {
    private final int x;
    private final int y;

    public ImmutablePoint(int x, int y) {
        this.x = x;
        this.y = y;
    }

    public int getX() {
        return x;
    }

    public int getY() {
        return y;
    }

    // 返回一個新的 ImmutablePoint 對象，表示移動後的位置
    public ImmutablePoint move(int deltaX, int deltaY) {
        return new ImmutablePoint(this.x + deltaX, this.y + deltaY);
    }
}

// 使用示例
public class Main {
    public static void main(String[] args) {
        ImmutablePoint point1 = new ImmutablePoint(1, 2);
        System.out.println("Point1: (" + point1.getX() + ", " + point1.getY() + ")");

        // 移動點，創建一個新的 ImmutablePoint 對象
        ImmutablePoint point2 = point1.move(3, 4);
        System.out.println("Point2: (" + point2.getX() + ", " + point2.getY() + ")");
        System.out.println("Point1: (" + point1.getX() + ", " + point1.getY() + ")"); // point1 不變
    }
}
```


不可變值類的優勢
- 線程安全：由於對象的狀態不可變，因此在多線程環境中使用時不需要額外的同步機制。
- 簡化代碼：不可變對象的行為是可預測的，這使得代碼更容易理解和維護。
- 提高性能：在某些情況下，可以通過共享不可變對象來提高性能，因為不需要創建新的對象。

總結
不可變值類是一種設計模式，通過創建狀態不可變的對象來提高代碼的安全性和可維護性。
在 Java 中，常見的不可變類包括 String、Integer 和其他基本數據類型的封裝類。
使用不可變類可以有效地避免許多常見的錯誤，特別是在多線程環境中。