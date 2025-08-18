Prefer class hierarchies to tagged classes

don't do this
```java

// Tagged class - vastly inferior to a class hierarchy!
class Figure {
    enum Shape { RECTANGLE, CIRCLE };
    // Tag field - the shape of this figure
    final Shape shape;
    // These fields are used only if shape is RECTANGLE
    double length;
    double width;
    // This field is used only if shape is CIRCLE
    double radius;
    
    // Constructor for circle
     Figure(double radius) {
        shape = Shape.CIRCLE;
        this.radius = radius;
}
    // Constructor for rectangle
    Figure(double length, double width) {
        shape = Shape.RECTANGLE;
        this.length = length;
        this.width = width;
}
    double area() {
        switch(shape) {
          case RECTANGLE:
            return length * width;
          case CIRCLE:
            return Math.PI * (radius * radius);
          default:
            throw new AssertionError(shape);
} }
}

```

这样的标签类具有许多缺点。 他们杂乱无章的样板代码，包括枚举声明，标签属性和 switch 语句。 可读性 更差，因为多个实现在一个类中混杂在一起。 内存使用增加，因为实例负担属于其他风格不相关的领域。 属性不能 成为 final，除非构造方法初始化不相关的属性，导致更多的样板代码。 构造方法在编译器的帮助下，必须设置标签 属性并初始化正确的数据属性:如果初始化错误的属性，程序将在运行时失败。 除非可以修改其源文件，否则不能 将其添加到标记的类中。 如果你添加一个风格，你必须记得给每个 switch 语句添加一个 case ，否则这个类将 在运行时失败。 最后，一个实例的数据类型没有提供任何关于风格的线索。 总之，标签类是冗长的，容易出错的， 而且效率低下。



do this
```java

// Class hierarchy replacement for a tagged class
abstract class Figure {
    abstract double area();
}


class Circle extends Figure {
    final double radius
    ;
	Circle(double radius) { this.radius = radius; }
	@Override double area() { return Math.PI * (radius * radius); }
}


class Rectangle extends Figure {
    final double length;
    final double width;
    Rectangle(double length, double width) {
        this.length = length;
        this.width  = width;
}
    @Override double area() { return length * width; }
}

```
PS: 请注意，上述层中的属性是直接访问的，而不是访问方法。 这里是为了简洁起见，如果类层次是公开的(条目 16)，这将是一个糟糕的设计。


总之，标签类很少有适用的情况。 如果你想写一个带有明显标签属性的类，请考虑标签属性是否可以被删除， 而类是否被类层次替换。 当遇到一个带有标签属性的现有类时，可以考虑将其重构为一个类层次中。