
- [Spring Boot 零基礎入門 (11) - Spring AOP 簡介 - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10327252)
- [Spring Boot 零基礎入門 (12) - Spring AOP 的用法 - @Aspect - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10329009)
- [Day 23 - Spring AOP - iT 邦幫忙：：一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10279178)



- [【Spring Boot】第 20 課－切面導向程式設計（AOP）](https://chikuwa-tech-study.blogspot.com/2021/06/spring-boot-aop-introduction.html)
經由 @Pointcut 標記，我們可決定哪些方法要套用這些 Advice。由於 Spring AOP 函式庫的特性，被 Pointcut 匹配到的方法，它們所屬的類別會被 Spring「動態代理」（dynamic proxy），從而產生出元件的代理類別。並將 Advice 的內容帶入，最後建立成元件（bean）。讀者可想成「原類別 + Advice = 代理類別 」，代理類別仍具有原類別的行為。

上述的動態代理會帶來什麼影響呢？當我們在程式中呼叫另一個元件的方法，即 A 元件呼叫 B 元件時，Advice 才會執行。這是因為注入到 A 元件的 B 元件屬於代理物件，只有代理物件才會具備 Advice。若在 B 元件中呼叫自己的方法，並不會執行 Advice，要特別注意。



透過 @Pointcut 標記的表示式，我們以指定套件的方式匹配一些方法。
![[IMG-Java Spring AOP-20250805201158446.png]]

我們也可以自己設計標記（annotation）冠在方法上，並以指定標記的方式讓來匹配。
接著於 Advice 方法中取得標記中的資訊，實現更有變化的處理方式。
![[IMG-Java Spring AOP-20250805201159326.png]]



[Spring AOP 白話文，淺談 Spring AOP 的學習分享。什麼是 Spring AOP？Aspect-Oriented… | by Jun | appxtech | Medium](https://medium.com/appxtech/spring-aop%E7%99%BD%E8%A9%B1%E6%96%87-%E6%B7%BA%E8%AB%87spring-aop%E7%9A%84%E5%AD%B8%E7%BF%92%E5%88%86%E4%BA%AB-1985489d008)
什麼是 Spring AOP？簡單的說, 就是在程式的前或後 (或前後) 執行其他的程式
而說的更白話一點，就是可以在不更改別人的程式的情況下，可以加入並執行自己的程式。


