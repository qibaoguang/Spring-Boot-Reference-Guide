### 29.4 使用H2的web控制台
[H2数据库](http://www.h2database.com/)提供一个[基于浏览器的控制台](http://www.h2database.com/html/quickstart.html#h2_console)，Spring Boot可以为你自动配置。如果以下条件满足，则控制台会被自动配置：

* 你正在开发一个web应用。
* 添加`com.h2database:h2`依赖。
* 你正在使用[Spring Boot开发者工具](http://docs.spring.io/spring-boot/docs/1.4.1.RELEASE/reference/htmlsingle/#using-boot-devtools)。

**注** 如果你没有使用Spring Boot的开发者工具，仍想利用H2的控制台，可以设置`spring.h2.console.enabled`属性值为`true`。H2控制台应该只用于开发期间，所以确保生产环境没有设置`spring.h2.console.enabled`。
