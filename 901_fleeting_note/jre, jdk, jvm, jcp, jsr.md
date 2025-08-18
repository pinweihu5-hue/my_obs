
jvm, java 虛擬機, java virtaul machine
- src code .java -> use **jdk** compiler -> .class, can run it on **jvm**
- run byte code on 不同平台
- 記憶體
- 執行緒管理
- 垃圾回收等職責



jre, java runtime environment

- 包括了 Java SE (Java Platform, Standard Edition) API 與 JVM。
- 使用 Java SE 的 API，在安裝有 JRE 的電腦上就可以直接運行，無需額外在你的程式中再包裝標準程式庫。



JCP／JSR
任何想要提議加入 Java 的功能或特性，必須以 JSR（Java Specification Requests）正式文件的方式提交，JSR 必須經過 JCP 執行委員會（Executive Committee）投票通過，方可成為最終標準文件，有興趣的廠商或組織可以根據 JSR 實現產品。

Java SE 17 的主要規範是在 [JSR 392](https://jcp.org/en/jsr/detail?id=392) 文件之中，而 Java SE 17 中的特定技術，會再規範於特定的 JSR 文件




JDK (Java Development Kit)
JDK 包含了 Java 執行階段環境/JRE(如此你才能執行 Java 程式)、Java 編譯器(javac、javadoc 等工具程式) 和 Java API
要開發 Java 程式的人，必須安裝的是 JDK，如此才有這些工具程式可以使用

JDK 又分為 OracleJDK 以及 OpenJDK

OracleJDK 與 OpenJDK 差異:
- OracleJDK 由 Oracle 公司開發，採用 JRL 協議: 可用於個人研究使用但禁止任何商業用途。
- OpenJDK 是 Java 開發環境 (JDK) 的開源版本，最早是由 Sun 公司開源，再根據 GNU GPL 授權發佈的，而 GPL 是允許商業上使用，但必須移除與 OracleJDK 授權有衝突的程式碼，所以不包括一些部署工具以及軟體套件等。
- OracleJDK 較關注穩定性，它重視更多的企業級使用者，而 OpenJDK 則經常釋出以支援其他效能，這可能導致不穩定。
- OracleJDK 支援長期釋出的更改，而 OpenJDK 僅支援計畫和完成下一個發行版。
- OracleJDK 的建構基於 OpenJDK，因此兩者之間並沒有技術差異。




撰寫 Java 程式的人才需要 JDK
如果你的程式只是想讓朋友執行呢？那他只要裝 JRE 就可以了，不用安裝 JDK，因為他不需要 javac 這些工具程式，但他需要 Java SE API 與 JVM。








![[IMG-jre, jdk, jvm, jcp, jsr-20250805201241189.png]]





