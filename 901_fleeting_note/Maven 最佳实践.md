Maven 是一种广泛使用的 Java 项目构建自动化工具。它简化了构建过程并帮助管理依赖关系，使开发人员的工作更轻松。Maven 详细介绍可以参考我写的这篇 [Maven 核心概念总结](https://javaguide.cn/tools/maven/maven-core-concepts.html) 。

这篇文章不会涉及到 Maven 概念的介绍，主要讨论一些最佳实践、建议和技巧，以优化我们在项目中对 Maven 的使用并改善我们的开发体验。

## [Maven 标准目录结构](https://javaguide.cn/tools/maven/maven-best-practices.html#maven-%E6%A0%87%E5%87%86%E7%9B%AE%E5%BD%95%E7%BB%93%E6%9E%84)

Maven 遵循标准目录结构来保持项目之间的一致性。遵循这种结构可以让其他开发人员更轻松地理解我们的项目。

Maven 项目的标准目录结构如下：

```java
src/
  main/
    java/
    resources/
  test/
    java/
    resources/
pom.xml
```

- `src/main/java`：源代码目录
- `src/main/resources`：资源文件目录
- `src/test/java`：测试代码目录
- `src/test/resources`：测试资源文件目录

这只是一个最简单的 Maven 项目目录示例。实际项目中，我们还会根据项目规范去做进一步的细分。

## [指定 Maven JDK 编译器](https://javaguide.cn/tools/maven/maven-best-practices.html#%E6%8C%87%E5%AE%9A-maven-%E7%BC%96%E8%AF%91%E5%99%A8%E6%8F%92%E4%BB%B6)

默认情况下，Maven 使用 Java5 编译我们的项目。要使用不同的 JDK 版本，请在 `pom.xml` 文件中配置 Maven 编译器插件。

例如，如果你想要使用 Java8 来编译你的项目，你可以在 `<build>` 标签下添加以下的代码片段：

```html
<build>
  <plugins>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>3.8.1</version>
      <configuration>
        <source>1.8</source>
        <target>1.8</target>
      </configuration>
    </plugin>
  </plugins>
</build>
```

这样，Maven 就会使用 Java8 的编译器来编译你的项目。如果你想要使用其他版本的 JDK，你只需要修改 `<source>` 和 `<target>` 标签的值即可。例如，如果你想要使用 Java11，你可以将它们的值改为 11。

## [有效管理依赖关系](https://javaguide.cn/tools/maven/maven-best-practices.html#%E6%9C%89%E6%95%88%E7%AE%A1%E7%90%86%E4%BE%9D%E8%B5%96%E5%85%B3%E7%B3%BB)

Maven 的依赖管理系统是其最强大的功能之一。

**在顶层 pom 文件中，通过标签 `dependencyManagement` 定义公共的依赖关系**，
这有助于避免冲突并确保所有模块使用相同版本的依赖项。

例如，假设我们有一个父模块和两个子模块 A 和 B，我们想要在所有模块中使用 JUnit 5.7.2 作为测试框架。
**我们可以在父模块的 `pom.xml` 文件中使用 `<dependencyManagement>` 标签来定义 JUnit 的版本**：

```html
<dependencyManagement>
  <dependencies>
    <dependency>
      
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter</artifactId>
      <version>5.7.2</version>
      <scope>test</scope>
      
    </dependency>
  </dependencies>
</dependencyManagement>
```

在子模块 A 和 B 的 `pom.xml` 文件中，我们只需要引用 JUnit 的 `groupId` 和 `artifactId` 即可:

```html
<dependencies>
  <dependency>
    
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter</artifactId>
    
  </dependency>
</dependencies>
```

## [针对不同环境使用配置文件](https://javaguide.cn/tools/maven/maven-best-practices.html#%E9%92%88%E5%AF%B9%E4%B8%8D%E5%90%8C%E7%8E%AF%E5%A2%83%E4%BD%BF%E7%94%A8%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6)

Maven 配置文件允许我们配置不同环境的构建设置，例如开发、测试和生产。

在 `pom.xml` 文件中定义配置文件并使用命令行参数激活它们：
```html
<profiles>

<profile>
    <id>development</id>
    <activation>
      <activeByDefault>true</activeByDefault>
    </activation>
    <properties>
      <environment>dev</environment>
    </properties>
  </profile>

<profile>
    <id>production</id>
    <properties>
      <environment>prod</environment>
    </properties>
  </profile>

</profiles>
```

使用命令行激活配置文件 `-P`：

```
mvn clean install -P production
```

## [保持 pom.xml 干净且井然有序](https://javaguide.cn/tools/maven/maven-best-practices.html#%E4%BF%9D%E6%8C%81-pom-xml-%E5%B9%B2%E5%87%80%E4%B8%94%E4%BA%95%E7%84%B6%E6%9C%89%E5%BA%8F)

组织良好的 `pom.xml` 文件更易于维护和理解。以下是维护干净的 `pom.xml` 的一些技巧：

- 将相似的依赖项和插件组合在一起。
- 使用注释来描述特定依赖项或插件的用途。
- 将插件和依赖项的版本号保留在 `<properties>` 标签内以便于管理。

```html
<properties>
  <junit.version>5.7.0</junit.version>
  <mockito.version>3.9.0</mockito.version>
</properties>
```

## [使用 Maven Wrapper](https://javaguide.cn/tools/maven/maven-best-practices.html#%E4%BD%BF%E7%94%A8-maven-wrapper)

Maven Wrapper 是一个用于管理和使用 Maven 的工具，它允许在没有预先安装 Maven 的情况下运行和构建 Maven 项目。

Maven 官方文档是这样介绍 Maven Wrapper 的：

> The Maven Wrapper is an easy way to ensure a user of your Maven build has everything necessary to run your Maven build.
> 
> Maven Wrapper 是一种简单的方法，可以确保 Maven 构建的用户拥有运行 Maven 构建所需的一切。

Maven Wrapper 可以确保构建过程使用正确的 Maven 版本，非常方便。要使用 Maven Wrapper，请在项目目录中运行以下命令：

```
mvn wrapper:wrapper
```

此命令会在我们的项目中生成 Maven Wrapper 文件。

现在我们可以使用 `./mvnw` （或 Windows 上的 `./mvnw.cmd`）而不是 `mvn` 来执行 Maven 命令。

## [通过持续集成实现构建自动化](https://javaguide.cn/tools/maven/maven-best-practices.html#%E9%80%9A%E8%BF%87%E6%8C%81%E7%BB%AD%E9%9B%86%E6%88%90%E5%AE%9E%E7%8E%B0%E6%9E%84%E5%BB%BA%E8%87%AA%E5%8A%A8%E5%8C%96)

将 Maven 项目与持续集成 (CI) 系统（例如 Jenkins 或 GitHub Actions）集成，可确保自动构建、测试和部署我们的代码。
CI 有助于及早发现问题并在整个团队中提供一致的构建流程。以下是 Maven 项目的简单 GitHub Actions 工作流程示例：

```yml
name: Java CI with Maven

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v2

    - name: Set up JDK 11
      uses: actions/setup-java@v2
      with:
        java-version: '11'
        distribution: 'adopt'

    - name: Build with Maven
      run: ./mvnw clean install
```

## [利用 Maven 插件获得附加功能](https://javaguide.cn/tools/maven/maven-best-practices.html#%E5%88%A9%E7%94%A8-maven-%E6%8F%92%E4%BB%B6%E8%8E%B7%E5%BE%97%E9%99%84%E5%8A%A0%E5%8A%9F%E8%83%BD)

有许多 Maven 插件可用于扩展 Maven 的功能。一些流行的插件包括（前三个是 Maven 自带的插件，后三个是第三方提供的插件）：

- maven-surefire-plugin：配置并执行单元测试。
- maven-failsafe-plugin：配置并执行集成测试。
- maven-javadoc-plugin：生成 Javadoc 格式的项目文档。
- maven-checkstyle-plugin：强制执行编码标准和最佳实践。
- jacoco-maven-plugin: 单测覆盖率。
- sonar-maven-plugin：分析代码质量。
- ……

jacoco-maven-plugin 使用示例：
```html
<build>
  <plugins>
    <plugin>
      
      <groupId>org.jacoco</groupId>
      <artifactId>jacoco-maven-plugin</artifactId>
      <version>0.8.8</version>
      
      <executions>
      
        <execution>
          <goals>
            <goal>prepare-agent</goal>
          </goals>
        </execution>
    
		<execution>
          <id>generate-code-coverage-report</id>
          <phase>test</phase>
          <goals>
            <goal>report</goal>
          </goals>
        </execution>

        
      </executions>
    </plugin>
    
  </plugins>
</build>
```

如果这些已有的插件无法满足我们的需求，我们还可以自定义插件。
探索可用的插件并在 `pom.xml` 文件中配置它们以增强我们的开发过程。

## [总结](https://javaguide.cn/tools/maven/maven-best-practices.html#%E6%80%BB%E7%BB%93)

Maven 是一个强大的工具，可以简化 Java 项目的构建过程和依赖关系管理。通过遵循这些最佳实践和技巧，我们可以优化 Maven 的使用并改善我们的 Java 开发体验。请记住使用标准目录结构，有效管理依赖关系，利用不同环境的配置文件，并将项目与持续集成系统集成，以确保构建一致。