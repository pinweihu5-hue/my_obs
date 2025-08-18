

假设我们有一个 `Shape` 接口和两个实现该接口的类：`Circle` 和 `Rectangle`。
我们将实现 `equals()` 方法，以允许这两个类之间的比较。
```java


// 定义一个 Shape 接口
interface Shape {
    double area();
}

// Circle 类实现 Shape 接口
class Circle implements Shape {
    private final double radius;

    public Circle(double radius) {
        this.radius = radius;
    }

    @Override
    public double area() {
        return Math.PI * radius * radius;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true; // 自反性
        if (!(o instanceof Circle)) return false; // 检查类型
        Circle circle = (Circle) o; // 类型转换
        return Double.compare(circle.radius, radius) == 0; // 比较半径
    }

    @Override
    public int hashCode() {
        return Double.hashCode(radius);
    }
}

// Rectangle 类实现 Shape 接口
class Rectangle implements Shape {
    private final double width;
    private final double height;

    public Rectangle(double width, double height) {
        this.width = width;
        this.height = height;
    }

    @Override
    public double area() {
        return width * height;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true; // 自反性
        if (!(o instanceof Rectangle)) return false; // 检查类型
        Rectangle rectangle = (Rectangle) o; // 类型转换
        return Double.compare(rectangle.width, width) == 0 &&
               Double.compare(rectangle.height, height) == 0; // 比较宽度和高度
    }

    @Override
    public int hashCode() {
        return Double.hashCode(width) ^ Double.hashCode(height);
    }
}

// 测试类
public class ShapeTest {
    public static void main(String[] args) {
        Shape circle1 = new Circle(5.0);
        Shape circle2 = new Circle(5.0);
        Shape rectangle = new Rectangle(5.0, 10.0);

        // 测试 equals 方法
        System.out.println(circle1.equals(circle2)); // true
        System.out.println(circle1.equals(rectangle)); // false
    }
}
```


### 代码解释
1. **Shape 接口**：定义了一个 `Shape` 接口，包含一个 `area()` 方法，用于计算形状的面积。
2. **Circle 类**：
    - 实现了 `Shape` 接口。
    - 在 `equals()` 方法中，首先检查 `this` 是否与 `o` 相同（自反性）。
    - 使用 `instanceof` 运算符检查 `o` 是否是 `Circle` 的实例。如果不是，返回 `false`。
    - 如果是 `Circle` 的实例，则进行类型转换，并比较两个圆的半径。
3. **Rectangle 类**：
    - 同样实现了 `Shape` 接口。
    - 在 `equals()` 方法中，使用相同的逻辑来检查类型和比较宽度和高度。
4. **测试类**：
    - 创建了两个相同的 `Circle` 对象和一个 `Rectangle` 对象。
    - 测试 `equals()` 方法，验证两个圆是否相等，以及圆和矩形之间的比较。

### 总结

这个示例展示了如何在实现 `equals()` 方法时使用 `instanceof` 运算符来检查参数的类型，并确保类型的正确性。通过这种方式，可以实现跨不同实现类的比较，确保符合 `equals()` 方法的约定。