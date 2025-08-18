## **2 Creating and Destroying Object**
- [[Item 1 考虑使用静态工厂方法替代构造方法]]
- [[Item 2 当构造方法参数过多时使用 builder 模式]]
- [[Item 3 使用私有构造方法或枚类实现 Singleton 属性]]
- [[Item 4 使用私有构造方法來讓類非实例化]]
- [[Item 5 使用依赖注入取代硬连接资源(hardwiring resources)]]
- [[Item 6 避免创建不必要的对象]]
- [[Item 7 消除过期的对象引用]]
- [[Item 8 避免使用 Finalizer 和 Cleaner 机制]]
- [[Item 9 使用 try-with-resources 语句替代 try-finally 语句]]

## **3 Methods common to all objects**
- 虽然 Object 是一个具体的类，但它主要是为继承而设计的。
- 它的所有非 final 方法 (equals、hashCode、 toString、clone 和 finalize) 都有清晰的通用约定( general contracts)，因为它们被设计为被子类重写。
- 任何类都有义务重写这些方法，以遵从他们的通用约定
- 如果不这样做，将会阻止其他依赖于约定的类 (例如 HashMap 和 HashSet) 与此类一起正常工作。
- 本章论述何时以及如何重写类的非 final 的方法。这一章省略了 finalize 方法，因为它在条目 8 中进行了讨论。 方法虽然不是 Object 中的方法，因为具有很多的相似性，所以也在这里讨论。
- [[Item 10 覆盖 equals 方法时应遵守的约定]]
- [[Item 11 重写 equals 方法时同时也要重写 hashcode 方法]]
- [[Item 12 始终覆盖 toString 方法]]
- [[Item 13 谨慎地重写 clone 方法]]
- [[Item 14 考虑实现 Comparable 接口]]

## 4 **Classes and Interfaces**
类和接口是 Java 编程语言的核心。它们是抽象的基本单位。该语言提供了许多强大的元素，你可以使用它们来设计类和接口。本章包含了帮助你充分利用这些元素的指导原则，以便让你的类和接口是可用的、健壮的和灵活的。


- [[Item15 使类和成员的可访问性最小化]]    done
- [[Item 16 在公共类中使用访问方法而不是公共属性]]
- [[Item 17 最小化可变性]] toReview
- [[Item 18 组合优于继承]] toReview
- [[Item 19 如使用继承则设计，应当文档说明，否则不该使用]] toReview
- [[Item 20 接口优于抽象类]] toReview
- [[Item 21 为后代设计接口]] toReview
- [[Item 22 接口仅用来定义类型]] toReview
- [[Item 23 优先使用类层次而不是标签类]] toReview
- [[Item 24 优先考虑静态成员类]] toReview
- [[Item 25 将源文件限制为单个顶级类]] 



第一版的筆記(重點概念擷取，但不夠完整)
- [[Item 15 使类和成员的可访问性最小化 v0]] 
- [[Item16 在公共类中使用访问方法而不是公共属性 v0]]
- [[Item 17 最小化可变性 v0]] toReview
- [[Item 18 组合优于继承 v0]] toReview
- [[Item 19 如使用继承则设计，应当文档说明，否则不该使用 v0]] toReview
- [[Item 20 接口优于抽象类 v0]] toReview
- [[Item 21 为后代设计接口 v0]] toReview
- [[Item 22 接口仅用来定义类型 v0]] toReview
- [[Item 23 优先使用类层次而不是标签类 v0]] toReview
- [[Item 24 优先考虑静态成员类 v0]] toReview
- [[Item 25 将源文件限制为单个顶级类 v0]] 

## 5 **Generics**
自 Java 5 以来，泛型已经成为该语言的一部分。 在泛型之前，你必须转换从集合中读取的每个对象。 如果有人 不小心插入了错误类型的对象，则在运行时可能会失败。 使用泛型，你告诉编译器在每个集合中允许哪些类型的对 象。 编译器会自动插入强制转换，并在编译时告诉你是否尝试插入错误类型的对象。 这样做的结果是既安全又清晰的程序，但这些益处，不限于集合，是有代价的。 本章告诉你如何最大限度地提高益处，并将 side effect 降至最低。


- [[Item26 不要使用原始类型 ]]
- [[Item27 消除非检查警告]] 
- [[Item28 列表优于数组]]
- [[Item29 优先考虑泛型]]
- [[Item30 优先使用泛型方法 ]]
- [[Item31 使用限定通配符增加 API 灵活性]]
- [[Item32 合理地结合泛型和可变参数]]
- [[Item33 优先考虑类型安全的异构容器]] toReview


第一版的筆記(重點概念擷取，但不夠完整)
- [[Item26 不要使用原始类型 v0]]
- [[Item27 消除非检查警告 v0]]
- [[Item28 列表优于数组 v0]]
- [[Item29 优先考虑泛型 v0]]
- [[Item30 优先使用泛型方法 v0]]
- [[Item31 使用限定通配符来增加 API 的灵活性 v0]]
- [[Item32 合理地结合泛型和可变参数 v0]]
- [[Item33 优先考虑类型安全的异构容器 v0]]

## **6 Enums and Annotations**
[[Java Enum]]
[[介紹 Java Annotations]]

JAVA 支持两种特殊用途的 引用类型：一种称为枚举类型的类，以及一种称为注解类型的接口
- [[Item 34 用枚举类型代替 int 常量]]
- [[Item 35 使用实例字段替代序数]]
- [[Item 36 用 EnumSet 替代位字段]]
- [[Item 37 使用 EnumMap 替换序数索引]]
- [[Item 38 使用接口模拟可扩展枚举]]
- [[Item 39 注解优于命名模式 ]]
- [[Item 40 堅持使用 @Override 注解]]
- [[Item 41 使用标记接口(marker interfaces)定义类型]]





## **7 Lambdas and Streams**
[[介紹 Java Lambdas and Streams]]
在 Java 8 中，为了更容易地创建函数对象，添加了函数式接口、lambda 表达式和方法引用；流 API 也与这些语言特性一并添加进来，为处理数据元素序列提供库支持。
- [[Item42 lambda 优于匿名类]]
- [[Item43 方法引用优于 lambda]]
- [[Item44 优先使用标准函数式接口]]
- [[Item45 明智地使用流]]
- [[Item46 在流中使用无副作用的函数]]
- [[Item47 优先选择 Collection 而不是流作为返回类型]]
- [[Item48 谨慎使用并行流]]

## **8 Methods**
本章讨论了方法设计的几个方面：如何处理参数和返回值，如何设计方法签名，以及如何编写方法文档。本章的大部分内容不仅适用于方法，也适用于构造函数。
- [[Item 49 检查参数的有效性 ]]
- [[Item 50 在需要时制作防御性副本]]
- [[Item 51 仔细设计方法签名]]
- [[Item 52 明智地使用重载 overloading]]
- [[Item 53 明智地使用可变参数]]
- [[Item 54 返回空集合或数组，而不是 null]]
- [[Item 55 明智地的返回 Optional]]
- [[Item 56 为所有公开的 API 元素编写文档注释]]
## **9 General Programming**
本章主要讨论了 Java 语言的具体细节，包括局部变量、控制结构、类库、数据结构和两种不是由语言本身提供的机制：反射和本地方法。最后，讨论了优化和命名惯例。
- [[Item 57 将局部变量的作用域最小化]]
- [[Item 58 for-each 循环优于传统的 for 循环]]
- [[Item 59 了解并使用库]]
- [[Item 60 需精确答案就不要使用 float 和 double 类型]]
- [[Item 61 基本数据类型优于包装类]]
- [[Item 62 其他类型更合适时应避免使用字符串]]
- [[Item 63 当心字符串连接引起的性能问题]]
- [[Item 64 通过接口引用对象]]
- [[Item 65 接口优于反射]]
- [[Item 66 明智地使用本地方法]]
- [[Item 67 明智地进行优化]]
- [[Item 68 遵守被广泛认可的命名约定]]

## **10 Exceptions**
[[介紹 Java 異常]]
当充分利用好异常时，可以提高程序的可读性、可靠性和可维护性。如果使用不当，则会产生负面效果。

- [[Item 69 只在异常的情況下使用异常]]
- [[Item 70 recoverable 下用 checked 异常，编程错误用 runtime异常]]
- [[Item 71 避免不必要地使用 checked 异常]]
- [[Item 72 鼓励复用标准异常]]
- [[Item 73 在適合的抽象層抛出异常]]
- [[Item 74 为每个方法记录会抛出的所有异常]]
- [[Item 75 异常的 detail messages 中应包含 failure capture 資訊]]
- [[Item 76 尽力保证故障原子性]]
- [[Item 77 不要忽略异常]]

- [ ] can also see [[Fault, Error, Failure, Exception - 搞笑談軟工]]
## **11 Concurrency**
- [[介紹 Java Concurrency]]
- [[介紹 synchronized keyword in Java]]
- [[Executor Framework in Java]]
- [[第 10 章 多线程]]
- [[尚硅谷-juc-高并发 note]]

线程允许多个活动并发进行。并发编程比单线程编程更困难，容易出错的地方更多，而且失败很难重现。你无法避开并发。它是平台中固有的，并且多核处理器现在也是无处不在，而你会有从多核处理器获得良好的性能的需求。

- [[Item 78 对共享可变数据的同步访问]]
- [[Item 79 避免过度同步]]
- [[Item 80 Executor、task、streams 优于於 threads]]
- [[Item 81 concurrency utilities 优于 wait 和 notify]]
- [[Item 82 文档应包含线程安全属性]]
- [[Item 83 明智地使用延迟初始化]]
- [[Item 84 不要依赖线程调度器]]


[[介紹 virtual threads in Java]]


## **12 Serialization**
[[介紹 Java Serialization]]

本章关注对象序列化，它是 Java 的框架，用于将对象编码为字节流（序列化），并从对象的编码中重构对象（反序列化）。对象序列化后，可以将其编码从一个 VM 发送到另一个 VM，或者存储在磁盘上，以便今后反序列化。本章主要讨论序列化的风险以及如何将其最小化。

- [[Item 85 优先选择 Java 序列化的替代方案]]
- [[Item 86 非常谨慎地实现 Serializable v1]]
	- [[Item 86 非常谨慎地实现 Serializable AI版本]]
- [[Item 87 考虑使用自定义序列化形式]]
- [[Item 88 防御性地编写 readObject 方法]]
- [[Item 89 对于实例控制，枚举类型优于 readResolve]]
- [[Item 90 考虑以 序列化代理 代替 序列化实例  ]]



# related yt
- [Effective Java, Third Edition - Keepin' it Effective - YouTube](https://youtu.be/7qXfoZIqi2Q?si=23XNFEPGy9eL68TD)
- [Revisiting Effective Java in 2019 by Edson Yanaga - YouTube](https://youtu.be/jKCSTi5HMw8?si=9qLBHVgfdrVbXAru)
- [(813) Static Factory Method - YouTube](https://www.youtube.com/watch?v=6pshPs01anU&list=PLLcEatxeThuPIvnC3LwNqj4wt6EhF5HZK)
- [Effective Java in Depth: Introduction - YouTube](https://www.youtube.com/watch?v=3P1Tmcb4c58&list=PLWchVAowvRxDnezSWMNII2g0Dr_Q3abJs)
- [Effective Java By Joshua Bloch Item 1: Consider Static Factory Methods Instead Of Constructors - YouTube](https://www.youtube.com/watch?v=YoeRpqLf5A0&list=PLcJ6nqGscKrd2UxeLTY-fIr0dzdMIOHAK)
- [Effective Java, Third Edition Keepin' it Effective (J. Bloch) - YouTube](https://www.youtube.com/watch?v=hSfylUXhpkA&list=PLMR0-ljfgzvculYWU_83PCeZWJ_jc9RLn)
- [\[Effective Java\] Introduction - YouTube](https://www.youtube.com/watch?v=mLHw8GuvPAM&list=PL77jNQPi0lGtpGyYwjRt-qLBBqV9ANijL)