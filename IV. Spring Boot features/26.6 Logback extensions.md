### 26.6 Logback扩展
Spring Boot包含很多有用的Logback扩展，你可以在`logback-spring.xml`配置文件中使用它们。

**注** 你不能在标准的`logback.xml`配置文件中使用扩展，因为它加载的太早了，不过可以使用`logback-spring.xml`，或指定`logging.config`属性。
