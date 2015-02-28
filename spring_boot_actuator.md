###Spring Boot执行器：Production-ready特性

Spring Boot包含很多其他的特性，它们可以帮你监控和管理发布到生产环境的应用。你可以选择使用HTTP端点，JMX或远程shell（SSH或Telnet）来管理和监控应用。审计（Auditing），健康（health）和数据采集（metrics gathering）会自动应用到你的应用。

* 开启production-ready特性

[spring-boot-actuator](http://github.com/spring-projects/spring-boot/tree/master/spring-boot-actuator)模块提供了Spring Boot所有的production-ready特性。启用该特性的最简单方式就是添加对spring-boot-starter-actuator ‘Starter POM’的依赖。

**执行器（Actuator）的定义**：执行器是一个制造业术语，指的是用于移动或控制东西的一个机械装置。一个很小的改变就能让执行器产生大量的运动。

基于Maven的项目想要添加执行器只需添加下面的'starter'依赖：
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
</dependencies>
```
对于Gradle，使用下面的声明：
```java
dependencies {
    compile("org.springframework.boot:spring-boot-starter-actuator")
}
```
* 端点

执行器端点允许你监控应用及与应用进行交互。Spring Boot包含很多内置的端点，你也可以添加自己的。例如，health端点提供了应用的基本健康信息。

端点暴露的方式取决于你采用的技术类型。大部分应用选择HTTP监控，端点的ID映射到一个URL。例如，默认情况下，health端点将被映射到/health。

下面的端点都是可用的：

| ID | 描述　|敏感（Sensitive）|
| ---- | :----- | :----- |
|autoconfig|显示一个auto-configuration的报告，该报告展示所有auto-configuration候选者及它们被应用或未被应用的原因|true|
|beans|显示一个应用中所有Spring Beans的完整列表|true|
|configprops|显示一个所有@ConfigurationProperties的整理列表|true|
|dump|执行一个线程转储|true|
|env|暴露来自Spring　ConfigurableEnvironment的属性|true|
|health|展示应用的健康信息（当使用一个未认证连接访问时显示一个简单的'status'，使用认证连接访问则显示全部信息详情）|false|
|info|显示任意的应用信息|false|
|metrics|展示当前应用的'指标'信息|true|
|mappings|显示一个所有@RequestMapping路径的整理列表|true|
|shutdown|允许应用以优雅的方式关闭（默认情况下不启用）|true|
|trace|显示trace信息（默认为最新的一些HTTP请求）|true|

**注**：根据一个端点暴露的方式，sensitive参数可能会被用做一个安全提示。例如，在使用HTTP访问sensitive端点时需要提供用户名/密码（如果没有启用web安全，可能会简化为禁止访问该端点）。

1. 自定义端点

使用Spring属性可以自定义端点。你可以设置端点是否开启（enabled），是否敏感（sensitive），甚至它的id。例如，下面的application.properties改变了敏感性和beans端点的id，也启用了shutdown。
```java
endpoints.beans.id=springbeans
endpoints.beans.sensitive=false
endpoints.shutdown.enabled=true
```
**注**：前缀'endpoints + . + name'被用来唯一的标识被配置的端点。

默认情况下，除了shutdown外的所有端点都是启用的。如果希望指定选择端点的启用，你可以使用endpoints.enabled属性。例如，下面的配置禁用了除info外的所有端点：
```java
endpoints.enabled=false
endpoints.info.enabled=true
```










