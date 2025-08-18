


### SpringBoot 实现热部署有哪几种方式？

- 热部署就是可以不用重新运行 SpringBoot 项目可以实现操作后台代码自动更新到以运行的项目中
- 主要有两种方式：
    - Spring Loaded
    - Spring-boot-devtools

- 这可以使用 DEV 工具来实现。通过这种依赖关系，您可以节省任何更改，嵌入式 tomcat 将重新启动。Spring Boot 有一个开发工具（DevTools）模块，它有助于提高开发人员的生产力。Java 开发人员面临的一个主要挑战是将文件更改自动部署到服务器并自动重启服务器。开发人员可以重新加载 Spring Boot 上的更改，而无需重新启动服务器。这将消除每次手动部署更改的需要。Spring Boot 在发布它的第一个版本时没有这个功能。这是开发人员最需要的功能。DevTools 模块完全满足开发人员的需求。该模块将在生产环境中被禁用。它还提供 H2 数据库控制台以更好地测试应用程序。

```pom

<dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-devtools</artifactId>
</dependency>


```
### SpringBoot 事務的使用

- 首先使用注解 EnableTransactionManagement 开启事務，然后在 Service 方法上添加注解 Transactional 便可。


### Async 异步调用方法

- 需要在方法上使用 @Async 注解即可实现方法的异步调用。 注意：需要在启动类加入 @EnableAsync 使异步调用 @Async 注解生效。


### 如何在 Spring Boot 启动的时候运行一些特定的代码？

- 可以实现接口 ApplicationRunner 或者 CommandLineRunner，这两个接口实现方式一样，它们都只提供了一个 run 方法

### Spring Boot 有哪几种读取配置的方式？

- Spring Boot 可以通过 @PropertySource,@Value,@Environment, @ConfigurationPropertie 注解来绑定变量

  
### 什么是 JavaConfig？

- Spring JavaConfig 是 Spring 社区的产品，Spring 3.0 引入了他，它提供了配置 Spring IOC 容器的纯 Java 方法。因此它有助于避免使用 XML 配置。使用 JavaConfig 的优点在于：
    
    - 面向对象的配置。由于配置被定义为 JavaConfig 中的类，因此用户可以充分利用 Java 中的面向对象功能。一个配置类可以继承另一个，重写它的 @Bean 方法等。
        
    - 减少或消除 XML 配置。基于依赖注入原则的外化配置的好处已被证明。但是，许多开发人员不希望在 XML 和 Java 之间来回切换。JavaConfig 为开发人员提供了一种纯 Java 方法来配置与 XML 配置概念相似的 Spring 容器。从技术角度来讲，只使用 JavaConfig 配置类来配置容器是可行的，但实际上很多人认为将 JavaConfig 与 XML 混合匹配是理想的。
        
    - 类型安全和重构友好。JavaConfig 提供了一种类型安全的方法来配置 Spring 容器。由于 Java 5.0 对泛型的支持，现在可以按类型而不是按名称检索 bean，不需要任何强制转换或基于字符串的查找。
        
- 常用的 Java config：
    
    - @Configuration：在类上打上写下此注解，表示这个类是配置类
    - @ComponentScan：在配置类上添加 @ComponentScan 注解。该注解默认会扫描该类所在的包下所有的配置类，相当于之前的 <context:component-scan>。
    - @Bean：bean 的注入：相当于以前的 <bean id="objectMapper" class="org.codehaus.jackson.map.ObjectMapper" />
    - @EnableWebMvc：相当于 xml 的 `<mvc:annotation-driven>`
    - @ImportResource： 相当于 xml 的 `<import resource="applicationContext-cache.xml">`



### 你如何理解 Spring Boot 配置加载顺序？

- 在 Spring Boot 里面，可以使用以下几种方式来加载配置。
    
    - 1.properties 文件；
    - 2.YAML 文件；
    - 3. 系统环境变量；
    - 4. 命令行参数；
    - 等等……



### 我们如何监视所有 Spring Boot 微服务？

- Spring Boot 提供监视器端点以监控各个微服务的度量。
- 这些端点对于获取有关应用程序的信息（如它们是否已启动）以及它们的组件（如数据库等）是否正常运行很有帮助。
- 但是，使用监视器的一个主要缺点或困难是，我们必须单独打开应用程序的知识点以了解其状态或健康状况。想象一下涉及 50 个应用程序的微服务，管理员将不得不击中所有 50 个应用程序的执行终端。
- 为了帮助我们处理这种情况，我们将使用位于的开源项目。 它建立在 Spring Boot Actuator 之上，它提供了一个 Web UI，使我们能够可视化多个应用程序的度量。


Spring Boot Actuator 是 Spring Boot 框架的一个子项目。它使我们能够通过一组 API 端点查看见解和指标并监控正在运行的应用程序。我们不需要手动创建它们。

数据库统计信息：数据源使用情况
CPU 内存使用情况
GC 周期
跟踪 HTTP 请求
SpringBoot 初始化器
这是一个基于 Web 的 UI，主要提供了使用可用依赖项创建新 Spring Boot 项目并下载创建为 zip 的项目的能力。所以我们不必从头开始创建它。该项目的所有基本结构都已在此下载的 zip 中。Spring Initializer 作为 IDE 插件提供，也具有不同的名称。



### SpringBoot 性能如何优化

- 如果项目比较大，类比较多，不使用 @SpringBootApplication，采用 @Compoment 指定扫包范围
- 在项目启动时设置 JVM 初始内存和最大内存相同
- 将 springboot 内置服务器由 tomcat 设置为 undertow



### spring-boot-starter-parent 有什么用？

- 我们都知道，新创建一个 Spring Boot 项目，默认都是有 parent 的，这个 parent 就是 spring-boot-starter-parent ，spring-boot-starter-parent 主要有如下作用：
    
    1. 定义了 Java 编译版本为 1.8 。
    2. 使用 UTF-8 格式编码。
    3. 继承自 spring-boot-dependencies，这个里边定义了依赖的版本，也正是因为继承了这个依赖，所以我们在写依赖时才不需要写版本号。
    4. 执行打包操作的配置。
    5. 自动化的资源过滤。
    6. 自动化的插件配置。
    7. 针对 application.properties 和 application.yml 的资源过滤，包括通过 profile 定义的不同环境的配置文件，例如 application-dev.properties 和 application-dev.yml。
- 总结就是打包用的



### SpringBoot 如何实现打包

- 进入项目目录在控制台输入 mvn clean package，clean 是清空已存在的项目包，package 进行打包

### Spring Boot 打成的 jar 和普通的 jar 有什么区别？

- Spring Boot 项目最终打包成的 jar 是可执行 jar ，这种 jar 可以直接通过 `java -jar xxx.jar` 命令来运行，这种 jar 不可以作为普通的 jar 被其他项目依赖，即使依赖了也无法使用其中的类。
    
- Spring Boot 的 jar 无法被其他项目依赖，主要还是他和普通 jar 的结构不同。普通的 jar 包，解压后直接就是包名，包里就是我们的代码，而 Spring Boot 打包成的可执行 jar 解压后，在 `\BOOT-INF\classes` 目录下才是我们的代码，因此无法被直接引用。如果非要引用，可以在 pom.xml 文件中增加配置，将 Spring Boot 项目打包成两个 jar ，一个可执行，一个可引用。
    


### Spring Boot 项目中可以替换 Tomcat 服务器吗？

是的。如果需要，我们可以通过在 POM 中添加 maven 排除来删除 Tomcat 服务器。实际上，Web 服务器捆绑在 started-web Spring Boot starter 依赖项中。应该添加排除。

<dependency> 
  <groupId>org.springframework.boot</groupId> 
  <artifactId>spring-boot-starter-web</artifactId> 
  <exclusions> 
    <exclusion> 
      <groupId>org.springframework.boot</groupId> 
      <artifactId> spring-boot-starter-tomcat</artifactId> 
    </exclusion> 
  </exclusions> 
</dependency>
然后我们必须将任何其他服务器（如 Jetty）添加到 POM。无论如何，我们必须在删除 tomcat 后添加一个 Web 服务器。

<dependency> <groupId>org.springframework.boot</groupId> <artifactId>spring-boot-starter-jetty</artifactId> </dependency><dependency> 
  <groupId>org.springframework.boot</groupId> 
  <artifactId>spring-boot-starter-jetty</artifactId> 
</dependency>
您现在将看到 Jetty 服务器日志，而不是 Tomcat。



![[IMG-Spring Boot 面试题彙整-20250214082113029.png]]



在 application.properties 中设置属性 debug=true，可以在控制台查看已启用和未启用的自动配置。







ref:
- [Spring Boot面试题（总结最全面的面试题！！！）Spring Boot 是 Spring 开源组织下的子项目，是 - 掘金](https://juejin.cn/post/6844904125709156359)
- [最经典的20个Spring Boot面试题，95%以上会被问到，不服来战\_springboot面试题-CSDN博客](https://blog.csdn.net/xxxzzzqqq_/article/details/129926457)
- [Site Unreachable](https://zhuanlan.zhihu.com/p/557542656)