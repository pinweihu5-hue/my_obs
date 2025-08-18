这篇文章主要是想通过一些问题，加深大家对于 Spring 的理解，所以不会涉及太多的代码！


## [Spring 基础](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#spring-%E5%9F%BA%E7%A1%80)

### [什么是 Spring 框架？](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#%E4%BB%80%E4%B9%88%E6%98%AF-spring-%E6%A1%86%E6%9E%B6)

Spring 是一款开源的轻量级 Java 开发框架，旨在提高开发人员的开发效率以及系统的可维护性。

我们一般说 Spring 框架指的都是 Spring Framework，它是很多模块的集合，使用这些模块可以很方便地协助我们进行开发，比如说 Spring 支持 IoC（Inversion of Control: 控制反转） 和 AOP (Aspect-Oriented Programming: 面向切面编程)、可以很方便地对数据库进行访问、可以很方便地集成第三方组件（电子邮件，任务，调度，缓存等等）、对单元测试支持比较好、支持 RESTful Java 应用程序的开发。

![[IMG-Spring 常见面试题总结-20250214082114376.png]]

Spring 最核心的思想就是不重新造轮子，开箱即用，提高开发效率。

Spring 翻译过来就是春天的意思，可见其目标和使命就是为 Java 程序员带来春天啊！感动！

🤐 多提一嘴：**语言的流行通常需要一个杀手级的应用，Spring 就是 Java 生态的一个杀手级的应用框架。**

Spring 提供的核心功能主要是 IoC 和 AOP。学习 Spring ，一定要把 IoC 和 AOP 的核心思想搞懂！

- Spring 官网：[https://spring.io/](https://spring.io/)
- GitHub 地址： [https://github.com/spring-projects/spring-framework](https://github.com/spring-projects/spring-framework)

### [Spring 包含的模块有哪些？](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#spring-%E5%8C%85%E5%90%AB%E7%9A%84%E6%A8%A1%E5%9D%97%E6%9C%89%E5%93%AA%E4%BA%9B)

**Spring4.x 版本**：

![[IMG-Spring 常见面试题总结-20250222193846518.png]]

**Spring5.x 版本**：

![[IMG-Spring 常见面试题总结-20250807203859574.png]]

Spring5.x 版本中 Web 模块的 Portlet 组件已经被废弃掉，同时增加了用于异步响应式处理的 WebFlux 组件。

Spring 各个模块的依赖关系如下：

![[IMG-Spring 常见面试题总结-20250807203901274.png]]

#### [Core Container](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#core-container)

Spring 框架的核心模块，也可以说是基础模块，主要提供 IoC 依赖注入功能的支持。Spring 其他所有的功能基本都需要依赖于该模块，我们从上面那张 Spring 各个模块的依赖关系图就可以看出来。

- **spring-core**：Spring 框架基本的核心工具类。
- **spring-beans**：提供对 bean 的创建、配置和管理等功能的支持。
- **spring-context**：提供对国际化、事件传播、资源加载等功能的支持。
- **spring-expression**：提供对表达式语言（Spring Expression Language） SpEL 的支持，只依赖于 core 模块，不依赖于其他模块，可以单独使用。

#### [AOP](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#aop)

- **spring-aspects**：该模块为与 AspectJ 的集成提供支持。
- **spring-aop**：提供了面向切面的编程实现。
- **spring-instrument**：提供了为 JVM 添加代理（agent）的功能。 具体来讲，它为 Tomcat 提供了一个织入代理，能够为 Tomcat 传递类文 件，就像这些文件是被类加载器加载的一样。没有理解也没关系，这个模块的使用场景非常有限。

#### [Data Access/Integration](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#data-access-integration)

- **spring-jdbc**：提供了对数据库访问的抽象 JDBC。不同的数据库都有自己独立的 API 用于操作数据库，而 Java 程序只需要和 JDBC API 交互，这样就屏蔽了数据库的影响。
- **spring-tx**：提供对事务的支持。
- **spring-orm**：提供对 Hibernate、JPA、iBatis 等 ORM 框架的支持。
- **spring-oxm**：提供一个抽象层支撑 OXM (Object-to-XML-Mapping)，例如：JAXB、Castor、XMLBeans、JiBX 和 XStream 等。
- **spring-jms** : 消息服务。自 Spring Framework 4.1 以后，它还提供了对 spring-messaging 模块的继承。

#### [Spring Web](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#spring-web)

- **spring-web**：对 Web 功能的实现提供一些最基础的支持。
- **spring-webmvc**：提供对 Spring MVC 的实现。
- **spring-websocket**：提供了对 WebSocket 的支持，WebSocket 可以让客户端和服务端进行双向通信。
- **spring-webflux**：提供对 WebFlux 的支持。WebFlux 是 Spring Framework 5.0 中引入的新的响应式框架。与 Spring MVC 不同，它不需要 Servlet API，是完全异步。

#### [Messaging](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#messaging)

**spring-messaging** 是从 Spring4.0 开始新加入的一个模块，主要职责是为 Spring 框架集成一些基础的报文传送应用。

#### [Spring Test](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#spring-test)

Spring 团队提倡测试驱动开发（TDD）。有了控制反转 (IoC) 的帮助，单元测试和集成测试变得更简单。

Spring 的测试模块对 JUnit（单元测试框架）、TestNG（类似 JUnit）、Mockito（主要用来 Mock 对象）、PowerMock（解决 Mockito 的问题比如无法模拟 final, static， private 方法）等等常用的测试框架支持的都比较好。

### [Spring,Spring MVC,Spring Boot 之间什么关系？](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#spring-spring-mvc-spring-boot-%E4%B9%8B%E9%97%B4%E4%BB%80%E4%B9%88%E5%85%B3%E7%B3%BB)

很多人对 Spring,Spring MVC,Spring Boot 这三者傻傻分不清楚！这里简单介绍一下这三者，其实很简单，没有什么高深的东西。

Spring 包含了多个功能模块（上面刚刚提到过），其中最重要的是 Spring-Core（主要提供 IoC 依赖注入功能的支持） 模块， Spring 中的其他模块（比如 Spring MVC）的功能实现基本都需要依赖于该模块。

下图对应的是 Spring4.x 版本。目前最新的 5.x 版本中 Web 模块的 Portlet 组件已经被废弃掉，同时增加了用于异步响应式处理的 WebFlux 组件。

![[IMG-Spring 常见面试题总结-20250807203902917.png]]

Spring MVC 是 Spring 中的一个很重要的模块，主要赋予 Spring 快速构建 MVC 架构的 Web 程序的能力。MVC 是模型 (Model)、视图 (View)、控制器 (Controller) 的简写，其核心思想是通过将业务逻辑、数据、显示分离来组织代码。

![[IMG-Spring 常见面试题总结-20250807203904320.png]]

使用 Spring 进行开发各种配置过于麻烦比如开启某些 Spring 特性时，需要用 XML 或 Java 进行显式配置。于是，Spring Boot 诞生了！

Spring 旨在简化 J2EE 企业应用程序开发。Spring Boot 旨在简化 Spring 开发（减少配置文件，开箱即用！）。

Spring Boot 只是简化了配置，如果你需要构建 MVC 架构的 Web 程序，你还是需要使用 Spring MVC 作为 MVC 框架，只是说 Spring Boot 帮你简化了 Spring MVC 的很多配置，真正做到开箱即用！

## [Spring IoC](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#spring-ioc)

### [谈谈自己对于 Spring IoC 的了解](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#%E8%B0%88%E8%B0%88%E8%87%AA%E5%B7%B1%E5%AF%B9%E4%BA%8E-spring-ioc-%E7%9A%84%E4%BA%86%E8%A7%A3)

**IoC（Inversion of Control: 控制反转）** 是一种设计思想，而不是一个具体的技术实现。IoC 的思想就是将原本在程序中手动创建对象的控制权，交由 Spring 框架来管理。不过， IoC 并非 Spring 特有，在其他语言中也有应用。

**为什么叫控制反转？**

- **控制**：指的是对象创建（实例化、管理）的权力
- **反转**：控制权交给外部环境（Spring 框架、IoC 容器）

![[IMG-Spring 常见面试题总结-20250807203815912.png]]

将对象之间的相互依赖关系交给 IoC 容器来管理，并由 IoC 容器完成对象的注入。这样可以很大程度上简化应用的开发，把应用从复杂的依赖关系中解放出来。 IoC 容器就像是一个工厂一样，当我们需要创建一个对象的时候，只需要配置好配置文件 / 注解即可，完全不用考虑对象是如何被创建出来的。

在实际项目中一个 Service 类可能依赖了很多其他的类，假如我们需要实例化这个 Service，你可能要每次都要搞清这个 Service 所有底层类的构造函数，这可能会把人逼疯。如果利用 IoC 的话，你只需要配置好，然后在需要的地方引用就行了，这大大增加了项目的可维护性且降低了开发难度。

在 Spring 中， IoC 容器是 Spring 用来实现 IoC 的载体， IoC 容器实际上就是个 Map（key，value），Map 中存放的是各种对象。

Spring 时代我们一般通过 XML 文件来配置 Bean，后来开发人员觉得 XML 文件来配置不太好，于是 SpringBoot 注解配置就慢慢开始流行起来。

相关阅读：
- [IoC 源码阅读](https://javadoop.com/post/spring-ioc)
- [IoC & AOP 详解（快速搞懂）](https://javaguide.cn/system-design/framework/spring/ioc-and-aop.html)

### [什么是 Spring Bean？](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#%E4%BB%80%E4%B9%88%E6%98%AF-spring-bean)

简单来说，**Bean 代指的就是那些被 IoC 容器所管理的对象**。

我们需要告诉 IoC 容器帮助我们管理哪些对象，这个是通过配置元数据来定义的。配置元数据可以是 XML 文件、注解或者 Java 配置类。

```
<!-- Constructor-arg with 'value' attribute -->
<bean id="..." class="...">
   <constructor-arg value="..."/>
</bean>
```

下图简单地展示了 IoC 容器如何使用配置元数据来管理对象。

![[IMG-Spring 常见面试题总结-20250807203907432.png]]

`org.springframework.beans` 和 `org.springframework.context` 这两个包是 IoC 实现的基础，如果想要研究 IoC 相关的源码的话，可以去看看

### [将一个类声明为 Bean 的注解有哪些？](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#%E5%B0%86%E4%B8%80%E4%B8%AA%E7%B1%BB%E5%A3%B0%E6%98%8E%E4%B8%BA-bean-%E7%9A%84%E6%B3%A8%E8%A7%A3%E6%9C%89%E5%93%AA%E4%BA%9B)

- `@Component`：通用的注解，可标注任意类为 `Spring` 组件。如果一个 Bean 不知道属于哪个层，可以使用 `@Component` 注解标注。
- `@Controller` : 对应 Spring MVC 控制层，主要用于接受用户请求并调用 `Service` 层返回数据给前端页面。
- `@Service` : 对应服务层，主要涉及一些复杂的逻辑，需要用到 Dao 层。
- `@Repository` : 对应持久层即 Dao 层，主要用于数据库相关操作。

### [@Component 和 @Bean 的区别是什么？](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#component-%E5%92%8C-bean-%E7%9A%84%E5%8C%BA%E5%88%AB%E6%98%AF%E4%BB%80%E4%B9%88)

- `@Component` 注解作用于类，而 `@Bean` 注解作用于方法。
- `@Component` 通常是通过类路径扫描来自动侦测以及自动装配到 Spring 容器中
	- 我们可以使用 `@ComponentScan` 注解定义要扫描的路径从中找出标识了需要装配的类自动装配到 Spring 的 bean 容器中。
- `@Bean` 注解通常是我们在标有该注解的方法中定义产生这个 bean, `@Bean` 告诉了 Spring 这是某个类的实例，当我需要用它的时候还给我。
- `@Bean` 注解比 `@Component` 注解的自定义性更强，而且很多地方我们只能通过 `@Bean` 注解来注册 bean。比如当我们引用第三方库中的类需要装配到 `Spring` 容器时，则只能通过 `@Bean` 来实现。

`@Bean` 注解使用示例：

```java
@Configuration
public class AppConfig {
    @Bean
    public TransferService transferService() {
        return new TransferServiceImpl();
    }

}
```

上面的代码相当于下面的 xml 配置

```java
<beans>
    <bean id="transferService" class="com.acme.TransferServiceImpl"/>
</beans>
```

下面这个例子是通过 `@Component` 无法实现的。

```java
@Bean
public OneService getService(status) {
    case (status)  {
        when 1:
                return new serviceImpl1();
        when 2:
                return new serviceImpl2();
        when 3:
                return new serviceImpl3();
    }
}
```

### [注入 Bean 的注解有哪些？](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#%E6%B3%A8%E5%85%A5-bean-%E7%9A%84%E6%B3%A8%E8%A7%A3%E6%9C%89%E5%93%AA%E4%BA%9B)

Spring 内置的 `@Autowired` 以及 JDK 内置的 `@Resource` 和 `@Inject` 都可以用于注入 Bean。

|Annotation|Package|Source|
|---|---|---|
|`@Autowired`|`org.springframework.bean.factory`|Spring 2.5+|
|`@Resource`|`javax.annotation`|Java JSR-250|
|`@Inject`|`javax.inject`|Java JSR-330|

`@Autowired` 和 `@Resource` 使用的比较多一些。

### [@Autowired 和 @Resource 的区别是什么？](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#autowired-%E5%92%8C-resource-%E7%9A%84%E5%8C%BA%E5%88%AB%E6%98%AF%E4%BB%80%E4%B9%88)

`Autowired` 属于 Spring 内置的注解，默认的注入方式为 `byType`（根据类型进行匹配），也就是说会优先根据接口类型去匹配并注入 Bean （接口的实现类）。

**这会有什么问题呢？** 当一个接口存在多个实现类的话，`byType` 这种方式就无法正确注入对象了，因为这个时候 Spring 会同时找到多个满足条件的选择，默认情况下它自己不知道选择哪一个。

这种情况下，注入方式会变为 `byName`（根据名称进行匹配），这个名称通常就是类名（首字母小写）。就比如说下面代码中的 `smsService` 就是我这里所说的名称，这样应该比较好理解了吧。

```java
// smsService 就是我们上面所说的名称
@Autowired
private SmsService smsService;
```

举个例子，`SmsService` 接口有两个实现类: `SmsServiceImpl1` 和 `SmsServiceImpl2`，且它们都已经被 Spring 容器所管理。
```java

// 报错，byName 和 byType 都无法匹配到 bean
@Autowired
private SmsService smsService;


// 正确注入 SmsServiceImpl1 对象对应的 bean
@Autowired
private SmsService smsServiceImpl1;

// 正确注入  SmsServiceImpl1 对象对应的 bean
// smsServiceImpl1 就是我们上面所说的名称
@Autowired
@Qualifier(value = "smsServiceImpl1")
private SmsService smsService;
```

我们还是建议通过 `@Qualifier` 注解来显式指定名称而不是依赖变量的名称。

`@Resource` 属于 JDK 提供的注解，默认注入方式为 `byName`。如果无法通过名称匹配到对应的 Bean 的话，注入方式会变为 `byType`。

`@Resource` 有两个比较重要且日常开发常用的属性：`name`（名称）、`type`（类型）。

```java
public @interface Resource {
    String name() default "";
    Class<?> type() default Object.class;
}
```

如果仅指定 `name` 属性则注入方式为 `byName`，如果仅指定 `type` 属性则注入方式为 `byType`，如果同时指定 `name` 和 `type` 属性（不建议这么做）则注入方式为 `byType`+`byName`。

```java
// 报错，byName 和 byType 都无法匹配到 bean
@Resource
private SmsService smsService;


// 正确注入 SmsServiceImpl1 对象对应的 bean
@Resource
private SmsService smsServiceImpl1;


// 正确注入 SmsServiceImpl1 对象对应的 bean（比较推荐这种方式）
@Resource(name = "smsServiceImpl1")
private SmsService smsService;
```


简单总结一下：
- `@Autowired` 是 Spring 提供的注解，`@Resource` 是 JDK 提供的注解。
- `Autowired` 默认的注入方式为 `byType`（根据类型进行匹配），`@Resource` 默认注入方式为 `byName`（根据名称进行匹配）。
- 当一个接口存在多个实现类的情况下，`@Autowired` 和 `@Resource` 都需要通过名称才能正确匹配到对应的 Bean。`Autowired` 可以通过 `@Qualifier` 注解来显式指定名称，`@Resource` 可以通过 `name` 属性来显式指定名称。
- `@Autowired` 支持在构造函数、方法、字段和参数上使用。`@Resource` 主要用于字段和方法上的注入，不支持在构造函数或参数上使用。

### [注入 Bean 的方式有哪些？](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#%E6%B3%A8%E5%85%A5-bean-%E7%9A%84%E6%96%B9%E5%BC%8F%E6%9C%89%E5%93%AA%E4%BA%9B)

依赖注入 (Dependency Injection, DI) 的常见方式：

1. 构造函数注入：通过类的构造函数来注入依赖项。
2. Setter 注入：通过类的 Setter 方法来注入依赖项。
3. Field（字段） 注入：直接在类的字段上使用注解（如 `@Autowired` 或 `@Resource`）来注入依赖项。

构造函数注入示例：

```java
@Service
public class UserService {

    private final UserRepository userRepository;
    
	@Autowired
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    //...
}
```

Setter 注入示例：

```java
@Service
public class UserService {

    private UserRepository userRepository;

    // 在 Spring 4.3 及以后的版本，特定情况下 @Autowired 可以省略不写
    @Autowired
    public void setUserRepository(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    //...
}
```

Field 注入示例：

```java
@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    //...
}
```

### [构造函数注入还是 Setter 注入？](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#%E6%9E%84%E9%80%A0%E5%87%BD%E6%95%B0%E6%B3%A8%E5%85%A5%E8%BF%98%E6%98%AF-setter-%E6%B3%A8%E5%85%A5)

Spring 官方有对这个问题的回答：[https://docs.spring.io/spring-framework/reference/core/beans/dependencies/factory-collaborators.html#beans-setter-injection](https://docs.spring.io/spring-framework/reference/core/beans/dependencies/factory-collaborators.html#beans-setter-injection)。

我这里主要提取总结完善一下 Spring 官方的建议。

**Spring 官方推荐构造函数注入**，这种注入方式的优势如下：

1. 依赖完整性：确保所有必需依赖在对象创建时就被注入，避免了空指针异常的风险。
2. 不可变性：有助于创建不可变对象，提高了线程安全性。
3. 初始化保证：组件在使用前已完全初始化，减少了潜在的错误。
4. 测试便利性：在单元测试中，可以直接通过构造函数传入模拟的依赖项，而不必依赖 Spring 容器进行注入。

构造函数注入适合处理**必需的依赖项**，而 **Setter 注入** 则更适合**可选的依赖项**，这些依赖项可以有默认值或在对象生命周期中动态设置。虽然 `@Autowired` 可以用于 Setter 方法来处理必需的依赖项，但构造函数注入仍然是更好的选择。

在某些情况下（例如第三方类不提供 Setter 方法），构造函数注入可能是**唯一的选择**。


### [Bean 的作用域有哪些？](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#bean-%E7%9A%84%E4%BD%9C%E7%94%A8%E5%9F%9F%E6%9C%89%E5%93%AA%E4%BA%9B)

Spring 中 Bean 的作用域通常有下面几种：

- **singleton** : IoC 容器中只有唯一的 bean 实例。Spring 中的 bean 默认都是单例的，是对单例设计模式的应用。
- **prototype** : 每次获取都会创建一个新的 bean 实例。也就是说，连续 `getBean()` 两次，得到的是不同的 Bean 实例。
- **request** （仅 Web 应用可用）: 每一次 HTTP 请求都会产生一个新的 bean（请求 bean），该 bean 仅在当前 HTTP request 内有效。
- **session** （仅 Web 应用可用） : 每一次来自新 session 的 HTTP 请求都会产生一个新的 bean（会话 bean），该 bean 仅在当前 HTTP session 内有效。
- **application/global-session** （仅 Web 应用可用）：每个 Web 应用在启动时创建一个 Bean（应用 Bean），该 bean 仅在当前应用启动时间内有效。
- **websocket** （仅 Web 应用可用）：每一次 WebSocket 会话产生一个新的 bean。

**如何配置 bean 的作用域呢？**

xml 方式：

```java
<bean id="..." class="..." scope="singleton"></bean>
```

注解方式：

```java
@Bean
@Scope(value = ConfigurableBeanFactory.SCOPE_PROTOTYPE)
public Person personPrototype() {
    return new Person();
}
```

### [Bean 是线程安全的吗？](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#bean-%E6%98%AF%E7%BA%BF%E7%A8%8B%E5%AE%89%E5%85%A8%E7%9A%84%E5%90%97)

Spring 框架中的 Bean 是否线程安全，取决于其作用域和状态。

我们这里以最常用的两种作用域 prototype 和 singleton 为例介绍。几乎所有场景的 Bean 作用域都是使用默认的 singleton ，重点关注 singleton 作用域即可。

prototype 作用域下，每次获取都会创建一个新的 bean 实例，不存在资源竞争问题，所以不存在线程安全问题。singleton 作用域下，IoC 容器中只有唯一的 bean 实例，可能会存在资源竞争问题（取决于 Bean 是否有状态）。如果这个 bean 是有状态的话，那就存在线程安全问题（有状态 Bean 是指包含可变的成员变量的对象）。

有状态 Bean 示例：

```java

// 定义了一个购物车类，其中包含一个用户购物车里商品的 List
@Component
public class ShoppingCart {
    private List<String> items = new ArrayList<>(); // 有状态

    public void addItem(String item) {
        items.add(item);
    }

    public List<String> getItems() {
        return items;
    }
}
```

不过，大部分 Bean 实际都是无状态（没有定义可变的成员变量）的（比如 Dao、Service），这种情况下， Bean 是线程安全的。

无状态 Bean 示例：

```java


// 定义了一个用户服务，它仅包含业务逻辑而不保存任何状态。
@Component
public class UserService {

    public User findUserById(Long id) {
        //...
    }
    //...
}
```

对于有状态单例 Bean 的线程安全问题，常见的三种解决办法是：

1. **避免可变成员变量**: 尽量设计 Bean 为无状态。
2. **使用 `ThreadLocal`**: 将可变成员变量保存在 `ThreadLocal` 中，确保线程独立。
3. **使用同步机制**: 利用 `synchronized` 或 `ReentrantLock` 来进行同步控制，确保线程安全。

这里以 `ThreadLocal` 为例，演示一下 `ThreadLocal` 保存用户登录信息的场景：

```java
public class UserThreadLocal {

    private UserThreadLocal() {}

    private static final ThreadLocal<SysUser> LOCAL = ThreadLocal.withInitial(() -> null);

    public static void put(SysUser sysUser) {
        LOCAL.set(sysUser);
    }

    public static SysUser get() {
        return LOCAL.get();
    }

    public static void remove() {
        LOCAL.remove();
    }
}
```

### [Bean 的生命周期了解么？](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#bean-%E7%9A%84%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E4%BA%86%E8%A7%A3%E4%B9%88)

1. **创建 Bean 的实例**：Bean 容器首先会找到配置文件中的 Bean 定义，然后使用 Java 反射 API 来创建 Bean 的实例。
2. **Bean 属性赋值 / 填充**：为 Bean 设置相关属性和依赖，例如 `@Autowired` 等注解注入的对象、`@Value` 注入的值、`setter` 方法或构造函数注入依赖和值、`@Resource` 注入的各种资源。
3. **Bean 初始化**：
    - 如果 Bean 实现了 `BeanNameAware` 接口，调用 `setBeanName()` 方法，传入 Bean 的名字。
    - 如果 Bean 实现了 `BeanClassLoaderAware` 接口，调用 `setBeanClassLoader()` 方法，传入 `ClassLoader` 对象的实例。
    - 如果 Bean 实现了 `BeanFactoryAware` 接口，调用 `setBeanFactory()` 方法，传入 `BeanFactory` 对象的实例。
    - 与上面的类似，如果实现了其他 `*.Aware` 接口，就调用相应的方法。
    - 如果有和加载这个 Bean 的 Spring 容器相关的 `BeanPostProcessor` 对象，执行 `postProcessBeforeInitialization()` 方法
    - 如果 Bean 实现了 `InitializingBean` 接口，执行 `afterPropertiesSet()` 方法。
    - 如果 Bean 在配置文件中的定义包含 `init-method` 属性，执行指定的方法。
    - 如果有和加载这个 Bean 的 Spring 容器相关的 `BeanPostProcessor` 对象，执行 `postProcessAfterInitialization()` 方法。
4. **销毁 Bean**：销毁并不是说要立马把 Bean 给销毁掉，而是把 Bean 的销毁方法先记录下来，将来需要销毁 Bean 或者销毁容器的时候，就调用这些方法去释放 Bean 所持有的资源。
    - 如果 Bean 实现了 `DisposableBean` 接口，执行 `destroy()` 方法。
    - 如果 Bean 在配置文件中的定义包含 `destroy-method` 属性，执行指定的 Bean 销毁方法。或者，也可以直接通过 `@PreDestroy` 注解标记 Bean 销毁之前执行的方法。

`AbstractAutowireCapableBeanFactory` 的 `doCreateBean()` 方法中能看到依次执行了这 4 个阶段：

```java
protected Object doCreateBean(final String beanName, final RootBeanDefinition mbd, final @Nullable Object[] args) throws BeanCreationException 
{

    // 1. 创建 Bean 的实例
    BeanWrapper instanceWrapper = null;
    if (instanceWrapper == null) {
        instanceWrapper = createBeanInstance(beanName, mbd, args);
    }

    Object exposedObject = bean;
    try {
        // 2. Bean 属性赋值/填充
        populateBean(beanName, mbd, instanceWrapper);
        // 3. Bean 初始化
        exposedObject = initializeBean(beanName, exposedObject, mbd);
    }

    // 4. 销毁 Bean-注册回调接口
    try {
        registerDisposableBeanIfNecessary(beanName, bean, mbd);
    }

    return exposedObject;
}
```

`Aware` 接口能让 Bean 能拿到 Spring 容器资源。

Spring 中提供的 `Aware` 接口主要有：

1. `BeanNameAware`：注入当前 bean 对应 beanName；
2. `BeanClassLoaderAware`：注入加载当前 bean 的 ClassLoader；
3. `BeanFactoryAware`：注入当前 `BeanFactory` 容器的引用。

`BeanPostProcessor` 接口是 Spring 为修改 Bean 提供的强大扩展点。
  
```java
public interface BeanPostProcessor {

	// 初始化前置处理
	default Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
		return bean;
	}

	// 初始化后置处理
	default Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
		return bean;
	}

}
```

- `postProcessBeforeInitialization`：Bean 实例化、属性注入完成后，`InitializingBean#afterPropertiesSet` 方法以及自定义的 `init-method` 方法之前执行；
- `postProcessAfterInitialization`：类似于上面，不过是在 `InitializingBean#afterPropertiesSet` 方法以及自定义的 `init-method` 方法之后执行。

`InitializingBean` 和 `init-method` 是 Spring 为 Bean 初始化提供的扩展点。

```java
public interface InitializingBean {
    // 初始化逻辑
	void afterPropertiesSet() throws Exception;
}
```

指定 `init-method` 方法，指定初始化方法：

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="demo" class="com.chaycao.Demo" init-method="init()"/>

</beans>
```

**如何记忆呢？**

1. 整体上可以简单分为四步：实例化 —> 属性赋值 —> 初始化 —> 销毁。
2. 初始化这一步涉及到的步骤比较多，包含 `Aware` 接口的依赖注入、`BeanPostProcessor` 在初始化前后的处理以及 `InitializingBean` 和 `init-method` 的初始化操作。
3. 销毁这一步会注册相关销毁回调接口，最后通过 `DisposableBean` 和 `destory-method` 进行销毁。

最后，再分享一张清晰的图解（图源：[如何记忆 Spring Bean 的生命周期](https://chaycao.github.io/2020/02/15/%E5%A6%82%E4%BD%95%E8%AE%B0%E5%BF%86Spring-Bean%E7%9A%84%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F.html)）。

![[IMG-Spring 常见面试题总结-20250807203909229.png]]

## [Spring AOP](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#spring-aop)

### [谈谈自己对于 AOP 的了解](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#%E8%B0%88%E8%B0%88%E8%87%AA%E5%B7%B1%E5%AF%B9%E4%BA%8E-aop-%E7%9A%84%E4%BA%86%E8%A7%A3)

AOP (Aspect-Oriented Programming: 面向切面编程) 能够将那些与业务无关，却为业务模块所共同调用的逻辑或责任（例如事务处理、日志管理、权限控制等）封装起来，便于减少系统的重复代码，降低模块间的耦合度，并有利于未来的可拓展性和可维护性。

Spring AOP 就是基于动态代理的，如果要代理的对象，实现了某个接口，那么 Spring AOP 会使用 **JDK Proxy**，去创建代理对象，而对于没有实现接口的对象，就无法使用 JDK Proxy 去进行代理了，这时候 Spring AOP 会使用 **Cglib** 生成一个被代理对象的子类来作为代理，如下图所示：

![[IMG-IoC & AOP 详解（快速搞懂）-20250222194006277.jpg]]

当然你也可以使用 **AspectJ** ！Spring AOP 已经集成了 AspectJ ，AspectJ 应该算的上是 Java 生态系统中最完整的 AOP 框架了。

AOP 切面编程涉及到的一些专业术语：

| 术语              | 含义                                   |
| --------------- | ------------------------------------ |
| 目标 (Target)     | 被通知的对象                               |
| 代理 (Proxy)      | 向目标对象应用通知之后创建的代理对象                   |
| 连接点 (JoinPoint) | 目标对象的所属类中，定义的所有方法均为连接点               |
| 切入点 (Pointcut)  | 被切面拦截 / 增强的连接点（切入点一定是连接点，连接点不一定是切入点） |
| 通知 (Advice)     | 增强的逻辑 / 代码，也即拦截到目标对象的连接点之后要做的事情      |
| 切面 (Aspect)     | 切入点 (Pointcut)+ 通知 (Advice)          |
| Weaving (织入)    | 将通知应用到目标对象，进而生成代理对象的过程动作             |

### [Spring AOP 和 AspectJ AOP 有什么区别？](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#spring-aop-%E5%92%8C-aspectj-aop-%E6%9C%89%E4%BB%80%E4%B9%88%E5%8C%BA%E5%88%AB)

**Spring AOP 属于运行时增强，而 AspectJ 是编译时增强。** Spring AOP 基于代理 (Proxying)，而 AspectJ 基于字节码操作 (Bytecode Manipulation)。

Spring AOP 已经集成了 AspectJ ，AspectJ 应该算的上是 Java 生态系统中最完整的 AOP 框架了。AspectJ 相比于 Spring AOP 功能更加强大，但是 Spring AOP 相对来说更简单，

如果我们的切面比较少，那么两者性能差异不大。但是，当切面太多的话，最好选择 AspectJ ，它比 Spring AOP 快很多。

### [AOP 常见的通知类型有哪些？](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#aop-%E5%B8%B8%E8%A7%81%E7%9A%84%E9%80%9A%E7%9F%A5%E7%B1%BB%E5%9E%8B%E6%9C%89%E5%93%AA%E4%BA%9B)

![[IMG-IoC & AOP 详解（快速搞懂）-20250807203750399.jpg]]

- **Before**（前置通知）：目标对象的方法调用之前触发
- **After** （后置通知）：目标对象的方法调用之后触发
- **AfterReturning**（返回通知）：目标对象的方法调用完成，在返回结果值之后触发
- **AfterThrowing**（异常通知）：目标对象的方法运行中抛出 / 触发异常后触发。AfterReturning 和 AfterThrowing 两者互斥。如果方法调用成功无异常，则会有返回值；如果方法抛出了异常，则不会有返回值。
- **Around** （环绕通知）：编程式控制目标对象的方法调用。环绕通知是所有通知类型中可操作范围最大的一种，因为它可以直接拿到目标对象，以及要执行的方法，所以环绕通知可以任意的在目标对象的方法调用前后搞事，甚至不调用目标对象的方法

### [多个切面的执行顺序如何控制？](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#%E5%A4%9A%E4%B8%AA%E5%88%87%E9%9D%A2%E7%9A%84%E6%89%A7%E8%A1%8C%E9%A1%BA%E5%BA%8F%E5%A6%82%E4%BD%95%E6%8E%A7%E5%88%B6)

1、通常使用 `@Order` 注解直接定义切面顺序

```java
// 值越小优先级越高
@Order(3)
@Component
@Aspect
public class LoggingAspect implements Ordered {
```

**2、实现 `Ordered` 接口重写 `getOrder` 方法。**

```java
@Component
@Aspect
public class LoggingAspect implements Ordered {

    // ....

    @Override
    public int getOrder() {
        // 返回值越小优先级越高
        return 1;
    }
}
```

## [Spring MVC](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#spring-mvc)

### [说说自己对于 Spring MVC 了解？](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#%E8%AF%B4%E8%AF%B4%E8%87%AA%E5%B7%B1%E5%AF%B9%E4%BA%8E-spring-mvc-%E4%BA%86%E8%A7%A3)

MVC 是模型 (Model)、视图 (View)、控制器 (Controller) 的简写，其核心思想是通过将业务逻辑、数据、显示分离来组织代码。

![[IMG-Spring 常见面试题总结-20250807203904320.png]]

网上有很多人说 MVC 不是设计模式，只是软件设计规范，我个人更倾向于 MVC 同样是众多设计模式中的一种。**[java-design-patterns](https://github.com/iluwatar/java-design-patterns)** 项目中就有关于 MVC 的相关介绍。

![[IMG-Spring 常见面试题总结-20250807203914668.png]]

想要真正理解 Spring MVC，我们先来看看 Model 1 和 Model 2 这两个没有 Spring MVC 的时代。

**Model 1 时代**

很多学 Java 后端比较晚的朋友可能并没有接触过 Model 1 时代下的 JavaWeb 应用开发。在 Model1 模式下，整个 Web 应用几乎全部用 JSP 页面组成，只用少量的 JavaBean 来处理数据库连接、访问等操作。

这个模式下 JSP 即是控制层（Controller）又是表现层（View）。显而易见，这种模式存在很多问题。比如控制逻辑和表现逻辑混杂在一起，导致代码重用率极低；再比如前端和后端相互依赖，难以进行测试维护并且开发效率极低。

![[IMG-Spring 常见面试题总结-20250807203916774.png]]

**Model 2 时代**

学过 Servlet 并做过相关 Demo 的朋友应该了解 “Java Bean (Model)+ JSP（View）+Servlet（Controller） ” 这种开发模式，这就是早期的 JavaWeb MVC 开发模式。

- Model: 系统涉及的数据，也就是 dao 和 bean。
- View：展示模型中的数据，只是用来展示。
- Controller：接受用户请求，并将请求发送至 Model，最后返回数据给 JSP 并展示给用户

![[IMG-Spring 常见面试题总结-20250807203919189.png]]

Model2 模式下还存在很多问题，Model2 的抽象和封装程度还远远不够，使用 Model2 进行开发时不可避免地会重复造轮子，这就大大降低了程序的可维护性和复用性。

于是，很多 JavaWeb 开发相关的 MVC 框架应运而生比如 Struts2，但是 Struts2 比较笨重。

**Spring MVC 时代**

随着 Spring 轻量级开发框架的流行，Spring 生态圈出现了 Spring MVC 框架， Spring MVC 是当前最优秀的 MVC 框架。相比于 Struts2 ， Spring MVC 使用更加简单和方便，开发效率更高，并且 Spring MVC 运行速度更快。

MVC 是一种设计模式，Spring MVC 是一款很优秀的 MVC 框架。Spring MVC 可以帮助我们进行更简洁的 Web 层的开发，并且它天生与 Spring 框架集成。Spring MVC 下我们一般把后端项目分为 Service 层（处理业务）、Dao 层（数据库操作）、Entity 层（实体类）、Controller 层 (控制层，返回数据给前台页面)。

### [Spring MVC 的核心组件有哪些？](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#spring-mvc-%E7%9A%84%E6%A0%B8%E5%BF%83%E7%BB%84%E4%BB%B6%E6%9C%89%E5%93%AA%E4%BA%9B)

记住了下面这些组件，也就记住了 SpringMVC 的工作原理。

- **`DispatcherServlet`**：**核心的中央处理器**，负责接收请求、分发，并给予客户端响应。
- **`HandlerMapping`**：**处理器映射器**，根据 URL 去匹配查找能处理的 `Handler` ，并会将请求涉及到的拦截器和 `Handler` 一起封装。
- **`HandlerAdapter`**：**处理器适配器**，根据 `HandlerMapping` 找到的 `Handler` ，适配执行对应的 `Handler`；
- **`Handler(Controller)`**：**请求处理器**，处理实际请求的处理器。
- **`ViewResolver`**：**视图解析器**，根据 `Handler` 返回的逻辑视图 / 视图，解析并渲染真正的视图，并传递给 `DispatcherServlet` 响应客户端

### [SpringMVC 工作原理了解吗？](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#springmvc-%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86%E4%BA%86%E8%A7%A3%E5%90%97)

**Spring MVC 原理如下图所示：**

> SpringMVC 工作原理的图解我没有自己画，直接图省事在网上找了一个非常清晰直观的，原出处不明。

![[IMG-Spring 常见面试题总结-20250807203921924.png]]

**流程说明（重要）：**

1. 客户端（浏览器）发送请求， `DispatcherServlet` 拦截请求。
2. `DispatcherServlet` 根据请求信息调用 `HandlerMapping` 。`HandlerMapping` 根据 URL 去匹配查找能处理的 `Handler`（也就是我们平常说的 `Controller` 控制器） ，并会将请求涉及到的拦截器和 `Handler` 一起封装。
3. `DispatcherServlet` 调用 `HandlerAdapter` 适配器执行 `Handler` 。
4. `Handler` 完成对用户请求的处理后，会返回一个 `ModelAndView` 对象给 `DispatcherServlet`，`ModelAndView` 顾名思义，包含了数据模型以及相应的视图的信息。`Model` 是返回的数据对象，`View` 是个逻辑上的 `View`。
5. `ViewResolver` 会根据逻辑 `View` 查找实际的 `View`。
6. `DispaterServlet` 把返回的 `Model` 传给 `View`（视图渲染）。
7. 把 `View` 返回给请求者（浏览器）

上述流程是传统开发模式（JSP，Thymeleaf 等）的工作原理。然而现在主流的开发方式是前后端分离，这种情况下 Spring MVC 的 `View` 概念发生了一些变化。由于 `View` 通常由前端框架（Vue, React 等）来处理，后端不再负责渲染页面，而是只负责提供数据，因此：

- 前后端分离时，后端通常不再返回具体的视图，而是返回**纯数据**（通常是 JSON 格式），由前端负责渲染和展示。
- `View` 的部分在前后端分离的场景下往往不需要设置，Spring MVC 的控制器方法只需要返回数据，不再返回 `ModelAndView`，而是直接返回数据，Spring 会自动将其转换为 JSON 格式。相应的，`ViewResolver` 也将不再被使用。

怎么做到呢？

- 使用 `@RestController` 注解代替传统的 `@Controller` 注解，这样所有方法默认会返回 JSON 格式的数据，而不是试图解析视图。
- 如果你使用的是 `@Controller`，可以结合 `@ResponseBody` 注解来返回 JSON。

### [统一异常处理怎么做？](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#%E7%BB%9F%E4%B8%80%E5%BC%82%E5%B8%B8%E5%A4%84%E7%90%86%E6%80%8E%E4%B9%88%E5%81%9A)

推荐使用注解的方式统一异常处理，具体会使用到 `@ControllerAdvice` + `@ExceptionHandler` 这两个注解 。

```java
@ControllerAdvice
@ResponseBody
public class GlobalExceptionHandler {

    @ExceptionHandler(BaseException.class)
    public ResponseEntity<?> handleAppException(BaseException ex, HttpServletRequest request) {
      //......
    }

    @ExceptionHandler(value = ResourceNotFoundException.class)
    public ResponseEntity<ErrorReponse> handleResourceNotFoundException(ResourceNotFoundException ex, HttpServletRequest request) {
      //......
    }
}
```

这种异常处理方式下，会给所有或者指定的 `Controller` 织入异常处理的逻辑（AOP），当 `Controller` 中的方法抛出异常的时候，由被 `@ExceptionHandler` 注解修饰的方法进行处理。

`ExceptionHandlerMethodResolver` 中 `getMappedMethod` 方法决定了异常具体被哪个被 `@ExceptionHandler` 注解修饰的方法处理异常。

```java
@Nullable
  private Method getMappedMethod(Class<? extends Throwable> exceptionType) {
  
    List<Class<? extends Throwable>> matches = new ArrayList<>();
    
    // 找到可以处理的所有异常信息。
    // mappedMethods 中存放了异常和处理异常的方法的对应关系
    for (Class<? extends Throwable> mappedException : this.mappedMethods.keySet()) {
      if (mappedException.isAssignableFrom(exceptionType)) {
        matches.add(mappedException);
      }
    }
    
    // 不为空说明有方法处理异常
    if (!matches.isEmpty()) {
      // 按照匹配程度从小到大排序
      matches.sort(new ExceptionDepthComparator(exceptionType));
      // 返回处理异常的方法
      return this.mappedMethods.get(matches.get(0));
    }
    else {
      return null;
    }
  }
```

从源代码看出：**`getMappedMethod()` 会首先找到可以匹配处理异常的所有方法信息，然后对其进行从小到大的排序，最后取最小的那一个匹配的方法 (即匹配度最高的那个)。**

## [Spring 框架中用到了哪些设计模式？](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#spring-%E6%A1%86%E6%9E%B6%E4%B8%AD%E7%94%A8%E5%88%B0%E4%BA%86%E5%93%AA%E4%BA%9B%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)

> 关于下面这些设计模式的详细介绍，可以看我写的 [Spring 中的设计模式详解](https://javaguide.cn/system-design/framework/spring/spring-design-patterns-summary.html) 这篇文章。

- **工厂设计模式** : Spring 使用工厂模式通过 `BeanFactory`、`ApplicationContext` 创建 bean 对象。
- **代理设计模式** : Spring AOP 功能的实现。
- **单例设计模式** : Spring 中的 Bean 默认都是单例的。
- **模板方法模式** : Spring 中 `jdbcTemplate`、`hibernateTemplate` 等以 Template 结尾的对数据库操作的类，它们就使用到了模板模式。
- **包装器设计模式** : 我们的项目需要连接多个数据库，而且不同的客户在每次访问中根据需要会去访问不同的数据库。这种模式让我们可以根据客户的需求能够动态切换不同的数据源。
- **观察者模式:** Spring 事件驱动模型就是观察者模式很经典的一个应用。
- **适配器模式** : Spring AOP 的增强或通知 (Advice) 使用到了适配器模式、spring MVC 中也是用到了适配器模式适配 `Controller`。
- ……

## [Spring 的循环依赖](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#spring-%E7%9A%84%E5%BE%AA%E7%8E%AF%E4%BE%9D%E8%B5%96)

### [Spring 循环依赖了解吗，怎么解决？](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#spring-%E5%BE%AA%E7%8E%AF%E4%BE%9D%E8%B5%96%E4%BA%86%E8%A7%A3%E5%90%97-%E6%80%8E%E4%B9%88%E8%A7%A3%E5%86%B3)

循环依赖是指 Bean 对象循环引用，是两个或多个 Bean 之间相互持有对方的引用，例如 CircularDependencyA → CircularDependencyB → CircularDependencyA。

```java
@Component
public class CircularDependencyA {
    @Autowired
    private CircularDependencyB circB;
}

@Component
public class CircularDependencyB {
    @Autowired
    private CircularDependencyA circA;
}
```

单个对象的自我依赖也会出现循环依赖，但这种概率极低，属于是代码编写错误。

```java
@Component
public class CircularDependencyA {
    @Autowired
    private CircularDependencyA circA;
}
```

Spring 框架通过使用三级缓存来解决这个问题，确保即使在循环依赖的情况下也能正确创建 Bean。

Spring 中的三级缓存其实就是三个 Map，如下：

```java
// 一级缓存
/** Cache of singleton objects: bean name to bean instance. */
private final Map<String, Object> singletonObjects = new ConcurrentHashMap<>(256);

// 二级缓存
/** Cache of early singleton objects: bean name to bean instance. */
private final Map<String, Object> earlySingletonObjects = new HashMap<>(16);

// 三级缓存
/** Cache of singleton factories: bean name to ObjectFactory. */
private final Map<String, ObjectFactory<?>> singletonFactories = new HashMap<>(16);
```

简单来说，Spring 的三级缓存包括：

1. **一级缓存（singletonObjects）**：存放最终形态的 Bean（已经实例化、属性填充、初始化）的单例池，为 “Spring 的单例属性” ⽽⽣。一般情况我们获取 Bean 都是从这里获取的，但是并不是所有的 Bean 都在单例池里面，例如原型 Bean 就不在里面。
2. **二级缓存（earlySingletonObjects）**：存放过渡 Bean（半成品，尚未属性填充），也就是三级缓存中 `ObjectFactory` 产生的对象，与三级缓存配合使用的，可以防止 AOP 的情况下，每次调用 `ObjectFactory#getObject()` 都是会产生新的代理对象的。
3. **三级缓存（singletonFactories）**：存放 `ObjectFactory`，`ObjectFactory` 的 `getObject()` 方法（最终调用的是 `getEarlyBeanReference()` 方法）可以生成原始 Bean 对象或者代理对象（如果 Bean 被 AOP 切面代理）。三级缓存只会对单例 Bean 生效。

接下来说一下 Spring 创建 Bean 的流程：

1. 先去 **一级缓存 `singletonObjects`** 中获取，存在就返回；
2. 如果不存在或者对象正在创建中，于是去 **二级缓存 `earlySingletonObjects`** 中获取；
3. 如果还没有获取到，就去 **三级缓存 `singletonFactories`** 中获取，通过执行 `ObjectFacotry` 的 `getObject()` 就可以获取该对象，获取成功之后，从三级缓存移除，并将该对象加入到二级缓存中。

在三级缓存中存储的是 `ObjectFacoty` ：

```java
public interface ObjectFactory<T> {
    T getObject() throws BeansException;
}
```

Spring 在创建 Bean 的时候，如果允许循环依赖的话，Spring 就会将刚刚实例化完成，但是属性还没有初始化完的 Bean 对象给提前暴露出去，这里通过 `addSingletonFactory` 方法，向三级缓存中添加一个 `ObjectFactory` 对象：

```java

// AbstractAutowireCapableBeanFactory # doCreateBean #
public abstract class AbstractAutowireCapableBeanFactory ... {

	protected Object doCreateBean(...) {
        //...

        // 支撑循环依赖：将 ()->getEarlyBeanReference 作为一个 ObjectFactory 对象的 getObject() 方法加入到三级缓存中
		addSingletonFactory(beanName, () -> getEarlyBeanReference(beanName, mbd, bean));
    }
}
```

那么上边在说 Spring 创建 Bean 的流程时说了，如果一级缓存、二级缓存都取不到对象时，会去三级缓存中通过 `ObjectFactory` 的 `getObject` 方法获取对象。

```
class A {
    // 使用了 B
    private B b;
}
class B {
    // 使用了 A
    private A a;
}
```

以上面的循环依赖代码为例，整个解决循环依赖的流程如下：

- 当 Spring 创建 A 之后，发现 A 依赖了 B ，又去创建 B，B 依赖了 A ，又去创建 A；
- 在 B 创建 A 的时候，那么此时 A 就发生了循环依赖，由于 A 此时还没有初始化完成，因此在 **一二级缓存** 中肯定没有 A；
- 那么此时就去三级缓存中调用 `getObject()` 方法去获取 A 的 **前期暴露的对象** 
	- 也就是调用上边加入的 `getEarlyBeanReference()` 方法，生成一个 A 的 **前期暴露对象**；
- 然后就将这个 `ObjectFactory` 从三级缓存中移除，并且将前期暴露对象放入到二级缓存中，那么 B 就将这个前期暴露对象注入到依赖，来支持循环依赖。

**只用两级缓存够吗？** 在没有 AOP 的情况下，确实可以只使用一级和三级缓存来解决循环依赖问题。但是，当涉及到 AOP 时，二级缓存就显得非常重要了，因为它确保了即使在 Bean 的创建过程中有多次对早期引用的请求，也始终只返回同一个代理对象，从而避免了同一个 Bean 有多个代理对象的问题。

**最后总结一下 Spring 如何解决三级缓存**：

在三级缓存这一块，主要记一下 Spring 是如何支持循环依赖的即可，也就是如果发生循环依赖的话，就去 **三级缓存 `singletonFactories`** 中拿到三级缓存中存储的 `ObjectFactory` 并调用它的 `getObject()` 方法来获取这个循环依赖对象的前期暴露对象（虽然还没初始化完成，但是可以拿到该对象在堆中的存储地址了），并且将这个前期暴露对象放到二级缓存中，这样在循环依赖时，就不会重复初始化了！

不过，这种机制也有一些缺点，比如增加了内存开销（需要维护三级缓存，也就是三个 Map），降低了性能（需要进行多次检查和转换）。并且，还有少部分情况是不支持循环依赖的，比如非单例的 bean 和 `@Async` 注解的 bean 无法支持循环依赖。

### [@Lazy 能解决循环依赖吗？](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#lazy-%E8%83%BD%E8%A7%A3%E5%86%B3%E5%BE%AA%E7%8E%AF%E4%BE%9D%E8%B5%96%E5%90%97)

`@Lazy` 用来标识类是否需要懒加载 / 延迟加载，可以作用在类上、方法上、构造器上、方法参数上、成员变量中。

Spring Boot 2.2 新增了**全局懒加载属性**，开启后全局 bean 被设置为懒加载，需要时再去创建。

配置文件配置全局懒加载：

```java
#默认false
spring.main.lazy-initialization=true
```

编码的方式设置全局懒加载：

```java
SpringApplication springApplication=new SpringApplication(Start.class);
springApplication.setLazyInitialization(false);
springApplication.run(args);
```

如非必要，尽量不要用全局懒加载。全局懒加载会让 Bean 第一次使用的时候加载会变慢，并且它会延迟应用程序问题的发现（当 Bean 被初始化时，问题才会出现）。

如果一个 Bean 没有被标记为懒加载，那么它会在 Spring IoC 容器启动的过程中被创建和初始化。如果一个 Bean 被标记为懒加载，那么它不会在 Spring IoC 容器启动时立即实例化，而是在第一次被请求时才创建。这可以帮助减少应用启动时的初始化时间，也可以用来解决循环依赖问题。

循环依赖问题是如何通过 `@Lazy` 解决的呢？这里举一个例子，比如说有两个 Bean，A 和 B，他们之间发生了循环依赖，那么 A 的构造器上添加 `@Lazy` 注解之后（延迟 Bean B 的实例化），加载的流程如下：

- 首先 Spring 会去创建 A 的 Bean，创建时需要注入 B 的属性；
- 由于在 A 上标注了 `@Lazy` 注解，因此 Spring 会去创建一个 B 的代理对象，将这个代理对象注入到 A 中的 B 属性；
- 之后开始执行 B 的实例化、初始化，在注入 B 中的 A 属性时，此时 A 已经创建完毕了，就可以将 A 给注入进去。

从上面的加载流程可以看出： `@Lazy` 解决循环依赖的关键点在于代理对象的使用。

- **没有 `@Lazy` 的情况下**：在 Spring 容器初始化 `A` 时会立即尝试创建 `B`，而在创建 `B` 的过程中又会尝试创建 `A`，最终导致循环依赖（即无限递归，最终抛出异常）。
- **使用 `@Lazy` 的情况下**：Spring 不会立即创建 `B`，而是会注入一个 `B` 的代理对象。由于此时 `B` 仍未被真正初始化，`A` 的初始化可以顺利完成。等到 `A` 实例实际调用 `B` 的方法时，代理对象才会触发 `B` 的真正初始化。

`@Lazy` 能够在一定程度上打破循环依赖链，允许 Spring 容器顺利地完成 Bean 的创建和注入。但这并不是一个根本性的解决方案，尤其是在构造函数注入、复杂的多级依赖等场景中，`@Lazy` 无法有效地解决问题。因此，最佳实践仍然是尽量避免设计上的循环依赖。

### [SpringBoot 允许循环依赖发生么？](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#springboot-%E5%85%81%E8%AE%B8%E5%BE%AA%E7%8E%AF%E4%BE%9D%E8%B5%96%E5%8F%91%E7%94%9F%E4%B9%88)

SpringBoot 2.6.x 以前是默认允许循环依赖的，也就是说你的代码出现了循环依赖问题，一般情况下也不会报错。SpringBoot 2.6.x 以后官方不再推荐编写存在循环依赖的代码，建议开发者自己写代码的时候去减少不必要的互相依赖。这其实也是我们最应该去做的，循环依赖本身就是一种设计缺陷，我们不应该过度依赖 Spring 而忽视了编码的规范和质量，说不定未来某个 SpringBoot 版本就彻底禁止循环依赖的代码了。

SpringBoot 2.6.x 以后，如果你不想重构循环依赖的代码的话，也可以采用下面这些方法：

- 在全局配置文件中设置允许循环依赖存在：`spring.main.allow-circular-references=true`。最简单粗暴的方式，不太推荐。
- 在导致循环依赖的 Bean 上添加 `@Lazy` 注解，这是一种比较推荐的方式。`@Lazy` 用来标识类是否需要懒加载 / 延迟加载，可以作用在类上、方法上、构造器上、方法参数上、成员变量中。
- ……

## [Spring 事务](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#spring-%E4%BA%8B%E5%8A%A1)

关于 Spring 事务的详细介绍，可以看我写的 [Spring 事务详解](https://javaguide.cn/system-design/framework/spring/spring-transaction.html) 这篇文章。

### [Spring 管理事务的方式有几种？](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#spring-%E7%AE%A1%E7%90%86%E4%BA%8B%E5%8A%A1%E7%9A%84%E6%96%B9%E5%BC%8F%E6%9C%89%E5%87%A0%E7%A7%8D)

- **编程式事务**：在代码中硬编码 (在分布式系统中推荐使用) : 通过 `TransactionTemplate` 或者 `TransactionManager` 手动管理事务，事务范围过大会出现事务未提交导致超时，因此事务要比锁的粒度更小。
- **声明式事务**：在 XML 配置文件中配置或者直接基于注解（单体应用或者简单业务系统推荐使用） : 实际是通过 AOP 实现（基于 `@Transactional` 的全注解方式使用最多）

### [Spring 事务中哪几种事务传播行为？](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#spring-%E4%BA%8B%E5%8A%A1%E4%B8%AD%E5%93%AA%E5%87%A0%E7%A7%8D%E4%BA%8B%E5%8A%A1%E4%BC%A0%E6%92%AD%E8%A1%8C%E4%B8%BA)

**事务传播行为是为了解决业务层方法之间互相调用的事务问题**。

当事务方法被另一个事务方法调用时，必须指定事务应该如何传播。例如：方法可能继续在现有事务中运行，也可能开启一个新事务，并在自己的事务中运行。

正确的事务传播行为可能的值如下:

**1.`TransactionDefinition.PROPAGATION_REQUIRED`**
使用的最多的一个事务传播行为，我们平时经常使用的 `@Transactional` 注解默认使用就是这个事务传播行为。如果当前存在事务，则加入该事务；如果当前没有事务，则创建一个新的事务。

**`2.TransactionDefinition.PROPAGATION_REQUIRES_NEW`**
创建一个新的事务，如果当前存在事务，则把当前事务挂起。也就是说不管外部方法是否开启事务，`Propagation.REQUIRES_NEW` 修饰的内部方法会新开启自己的事务，且开启的事务相互独立，互不干扰。

**3.`TransactionDefinition.PROPAGATION_NESTED`**
如果当前存在事务，则创建一个事务作为当前事务的嵌套事务来运行；如果当前没有事务，则该取值等价于 `TransactionDefinition.PROPAGATION_REQUIRED`。

**4.`TransactionDefinition.PROPAGATION_MANDATORY`**
如果当前存在事务，则加入该事务；如果当前没有事务，则抛出异常。（mandatory：强制性）
这个使用的很少。


若是错误的配置以下 3 种事务传播行为，事务将不会发生回滚：
- **`TransactionDefinition.PROPAGATION_SUPPORTS`**: 如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务的方式继续运行。
- **`TransactionDefinition.PROPAGATION_NOT_SUPPORTED`**: 以非事务方式运行，如果当前存在事务，则把当前事务挂起。
- **`TransactionDefinition.PROPAGATION_NEVER`**: 以非事务方式运行，如果当前存在事务，则抛出异常。

### [Spring 事务中的隔离级别有哪几种？](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#spring-%E4%BA%8B%E5%8A%A1%E4%B8%AD%E7%9A%84%E9%9A%94%E7%A6%BB%E7%BA%A7%E5%88%AB%E6%9C%89%E5%93%AA%E5%87%A0%E7%A7%8D)

和事务传播行为这块一样，为了方便使用，Spring 也相应地定义了一个枚举类：`Isolation`

```java
public enum Isolation {

    DEFAULT(TransactionDefinition.ISOLATION_DEFAULT),
    READ_UNCOMMITTED(TransactionDefinition.ISOLATION_READ_UNCOMMITTED),
    READ_COMMITTED(TransactionDefinition.ISOLATION_READ_COMMITTED),
    REPEATABLE_READ(TransactionDefinition.ISOLATION_REPEATABLE_READ),
    SERIALIZABLE(TransactionDefinition.ISOLATION_SERIALIZABLE);

    private final int value;

    Isolation(int value) {
        this.value = value;
    }

    public int value() {
        return this.value;
    }

}
```

下面我依次对每一种事务隔离级别进行介绍：

- **`TransactionDefinition.ISOLATION_DEFAULT`** : 使用后端数据库默认的隔离级别，MySQL 默认采用的 `REPEATABLE_READ` 隔离级别 Oracle 默认采用的 `READ_COMMITTED` 隔离级别.
- **`TransactionDefinition.ISOLATION_READ_UNCOMMITTED`** : 最低的隔离级别，使用这个隔离级别很少，因为它允许读取尚未提交的数据变更，**可能会导致脏读、幻读或不可重复读**
- **`TransactionDefinition.ISOLATION_READ_COMMITTED`** : 允许读取并发事务已经提交的数据，**可以阻止脏读，但是幻读或不可重复读仍有可能发生**
- **`TransactionDefinition.ISOLATION_REPEATABLE_READ`** : 对同一字段的多次读取结果都是一致的，除非数据是被本身事务自己所修改，**可以阻止脏读和不可重复读，但幻读仍有可能发生。**
- **`TransactionDefinition.ISOLATION_SERIALIZABLE`** : 最高的隔离级别，完全服从 ACID 的隔离级别。所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰，也就是说，**该级别可以防止脏读、不可重复读以及幻读**。但是这将严重影响程序的性能。通常情况下也不会用到该级别。

### [@Transactional (rollbackFor = Exception.class) 注解了解吗？](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#transactional-rollbackfor-exception-class-%E6%B3%A8%E8%A7%A3%E4%BA%86%E8%A7%A3%E5%90%97)

`Exception` 分为运行时异常 `RuntimeException` 和非运行时异常。事务管理对于企业应用来说是至关重要的，即使出现异常情况，它也可以保证数据的一致性。

当 `@Transactional` 注解作用于类上时，该类的所有 public 方法将都具有该类型的事务属性，同时，我们也可以在方法级别使用该标注来覆盖类级别的定义。

`@Transactional` 注解默认回滚策略是只有在遇到 `RuntimeException`(运行时异常) 或者 `Error` 时才会回滚事务，而不会回滚 `Checked Exception`（受检查异常）。这是因为 Spring 认为 `RuntimeException` 和 Error 是不可预期的错误，而受检异常是可预期的错误，可以通过业务逻辑来处理。

![[IMG-Spring 常见面试题总结-20250807203924521.png]]

如果想要修改默认的回滚策略，可以使用 `@Transactional` 注解的 `rollbackFor` 和 `noRollbackFor` 属性来指定哪些异常需要回滚，哪些异常不需要回滚。

例如，如果想要让所有的异常都回滚事务，可以使用如下的注解：
```java
@Transactional(rollbackFor = Exception.class)
public void someMethod() {
// some business logic
}
```

如果想要让某些特定的异常不回滚事务，可以使用如下的注解：
```java
@Transactional(noRollbackFor = CustomException.class)
public void someMethod() {
// some business logic
}
```

## [Spring Data JPA](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#spring-data-jpa)

JPA 重要的是实战，这里仅对小部分知识点进行总结。

### [如何使用 JPA 在数据库中非持久化一个字段？](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8-jpa-%E5%9C%A8%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%AD%E9%9D%9E%E6%8C%81%E4%B9%85%E5%8C%96%E4%B8%80%E4%B8%AA%E5%AD%97%E6%AE%B5)

假如我们有下面一个类：

```java
@Entity(name="USER")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    @Column(name = "ID")
    private Long id;

    @Column(name="USER_NAME")
    private String userName;

    @Column(name="PASSWORD")
    private String password;

    private String secrect;
}
```

如果我们想让 `secrect` 这个字段不被持久化，也就是不被数据库存储怎么办？我们可以采用下面几种方法：

```java

static String transient1; // not persistent because of static


final String transient2 = "Satish"; // not persistent because of final


transient String transient3; // not persistent because of transient


@Transient
String transient4; // not persistent because of @Transient
```

一般使用后面两种方式比较多，我个人使用注解的方式比较多。

### [JPA 的审计功能是做什么的？有什么用？](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#jpa-%E7%9A%84%E5%AE%A1%E8%AE%A1%E5%8A%9F%E8%83%BD%E6%98%AF%E5%81%9A%E4%BB%80%E4%B9%88%E7%9A%84-%E6%9C%89%E4%BB%80%E4%B9%88%E7%94%A8)

审计功能主要是帮助我们记录数据库操作的具体行为比如某条记录是谁创建的、什么时间创建的、最后修改人是谁、最后修改时间是什么时候。

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
@MappedSuperclass
@EntityListeners(value = AuditingEntityListener.class)
public abstract class AbstractAuditBase {

    @CreatedDate
    @Column(updatable = false)
    @JsonIgnore
    private Instant createdAt;

    @LastModifiedDate
    @JsonIgnore
    private Instant updatedAt;

    @CreatedBy
    @Column(updatable = false)
    @JsonIgnore
    private String createdBy;

    @LastModifiedBy
    @JsonIgnore
    private String updatedBy;
}
```

- `@CreatedDate`: 表示该字段为创建时间字段，在这个实体被 insert 的时候，会设置值
- `@CreatedBy` : 表示该字段为创建人，在这个实体被 insert 的时候，会设置值
    `@LastModifiedDate`、`@LastModifiedBy` 同理。
    

### [实体之间的关联关系注解有哪些？](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#%E5%AE%9E%E4%BD%93%E4%B9%8B%E9%97%B4%E7%9A%84%E5%85%B3%E8%81%94%E5%85%B3%E7%B3%BB%E6%B3%A8%E8%A7%A3%E6%9C%89%E5%93%AA%E4%BA%9B)

- `@OneToOne` : 一对一。
- `@ManyToMany`：多对多。
- `@OneToMany` : 一对多。
- `@ManyToOne`：多对一。

利用 `@ManyToOne` 和 `@OneToMany` 也可以表达多对多的关联关系。

## [Spring Security](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#spring-security)

Spring Security 重要的是实战，这里仅对小部分知识点进行总结。

### [有哪些控制请求访问权限的方法？](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#%E6%9C%89%E5%93%AA%E4%BA%9B%E6%8E%A7%E5%88%B6%E8%AF%B7%E6%B1%82%E8%AE%BF%E9%97%AE%E6%9D%83%E9%99%90%E7%9A%84%E6%96%B9%E6%B3%95)

![[IMG-Spring 常见面试题总结-20250807203927214.png]]

- `permitAll()`：无条件允许任何形式访问，不管你登录还是没有登录。
- `anonymous()`：允许匿名访问，也就是没有登录才可以访问。
- `denyAll()`：无条件决绝任何形式的访问。
- `authenticated()`：只允许已认证的用户访问。
- `fullyAuthenticated()`：只允许已经登录或者通过 remember-me 登录的用户访问。
- `hasRole(String)` : 只允许指定的角色访问。
- `hasAnyRole(String)` : 指定一个或者多个角色，满足其一的用户即可访问。
- `hasAuthority(String)`：只允许具有指定权限的用户访问
- `hasAnyAuthority(String)`：指定一个或者多个权限，满足其一的用户即可访问。
- `hasIpAddress(String)` : 只允许指定 ip 的用户访问。

### [hasRole 和 hasAuthority 有区别吗？](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#hasrole-%E5%92%8C-hasauthority-%E6%9C%89%E5%8C%BA%E5%88%AB%E5%90%97)

可以看看松哥的这篇文章：[Spring Security 中的 hasRole 和 hasAuthority 有区别吗？](https://mp.weixin.qq.com/s/GTNOa2k9_n_H0w24upClRw)，介绍的比较详细。

### [如何对密码进行加密？](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#%E5%A6%82%E4%BD%95%E5%AF%B9%E5%AF%86%E7%A0%81%E8%BF%9B%E8%A1%8C%E5%8A%A0%E5%AF%86)

如果我们需要保存密码这类敏感数据到数据库的话，需要先加密再保存。

Spring Security 提供了多种加密算法的实现，开箱即用，非常方便。这些加密算法实现类的接口是 `PasswordEncoder` ，如果你想要自己实现一个加密算法的话，也需要实现 `PasswordEncoder` 接口。

`PasswordEncoder` 接口一共也就 3 个必须实现的方法。

```java
public interface PasswordEncoder {
    // 加密也就是对原始密码进行编码
    String encode(CharSequence var1);
    // 比对原始密码和数据库中保存的密码
    boolean matches(CharSequence var1, String var2);
    // 判断加密密码是否需要再次进行加密，默认返回 false
    default boolean upgradeEncoding(String encodedPassword) {
        return false;
    }
}
```

![[IMG-Spring 常见面试题总结-20250807203930077.png]]

官方推荐使用基于 bcrypt 强哈希函数的加密算法实现类。

### [如何优雅更换系统使用的加密算法？](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#%E5%A6%82%E4%BD%95%E4%BC%98%E9%9B%85%E6%9B%B4%E6%8D%A2%E7%B3%BB%E7%BB%9F%E4%BD%BF%E7%94%A8%E7%9A%84%E5%8A%A0%E5%AF%86%E7%AE%97%E6%B3%95)

如果我们在开发过程中，突然发现现有的加密算法无法满足我们的需求，需要更换成另外一个加密算法，这个时候应该怎么办呢？

推荐的做法是通过 `DelegatingPasswordEncoder` 兼容多种不同的密码加密方案，以适应不同的业务需求。

从名字也能看出来，`DelegatingPasswordEncoder` 其实就是一个代理类，并非是一种全新的加密算法，它做的事情就是代理上面提到的加密算法实现类。在 Spring Security 5.0 之后，默认就是基于 `DelegatingPasswordEncoder` 进行密码加密的。

## [参考](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#%E5%8F%82%E8%80%83)

- 《Spring 技术内幕》
- 《从零开始深入学习 Spring》：[https://juejin.cn/book/6857911863016390663](https://juejin.cn/book/6857911863016390663)
- [http://www.cnblogs.com/wmyskxz/p/8820371.html](http://www.cnblogs.com/wmyskxz/p/8820371.html)
- [https://www.journaldev.com/2696/spring-interview-questions-and-answers](https://www.journaldev.com/2696/spring-interview-questions-and-answers)
- [https://www.edureka.co/blog/interview-questions/spring-interview-questions/](https://www.edureka.co/blog/interview-questions/spring-interview-questions/)
- [https://www.cnblogs.com/clwydjgs/p/9317849.html](https://www.cnblogs.com/clwydjgs/p/9317849.html)
- [https://howtodoinjava.com/interview-questions/top-spring-interview-questions-with-answers/](https://howtodoinjava.com/interview-questions/top-spring-interview-questions-with-answers/)
- [http://www.tomaszezula.com/2014/02/09/spring-series-part-5-component-vs-bean/](http://www.tomaszezula.com/2014/02/09/spring-series-part-5-component-vs-bean/)
- [https://stackoverflow.com/questions/34172888/difference-between-bean-and-autowired](https://stackoverflow.com/questions/34172888/difference-between-bean-and-autowired)