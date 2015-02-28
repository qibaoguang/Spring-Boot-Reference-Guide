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
2. 健康信息

健康信息可以用来检查应用的运行状态。它经常被监控软件用来提醒人们生产系统是否停止。health端点暴露的默认信息取决于端点是如何被访问的。对于一个非安全，未认证的连接只返回一个简单的'status'信息。对于一个安全或认证过的连接其他详细信息也会展示（具体参考[Section 41.6, “HTTP Health endpoint access restrictions” ]()）。

健康信息是从你的ApplicationContext中定义的所有[HealthIndicator](http://github.com/spring-projects/spring-boot/tree/master/spring-boot-actuator/src/main/java/org/springframework/boot/actuate/health/HealthIndicator.java) beans收集过来的。Spring Boot包含很多auto-configured的HealthIndicators，你也可以写自己的。

3. 安全与HealthIndicators

HealthIndicators返回的信息常常性质上有点敏感。例如，你可能不想将数据库服务器的详情发布到外面。因此，在使用一个未认证的HTTP连接时，默认只会暴露健康状态（health status）。如果想将所有的健康信息暴露出去，你可以把endpoints.health.sensitive设置为false。

为防止'拒绝服务'攻击，Health响应会被缓存。你可以使用`endpoints.health.time-to-live`属性改变默认的缓存时间（1000毫秒）。

- 自动配置的HealthIndicators

下面的HealthIndicators会被Spring Boot自动配置（在合适的时候）：

|名称|描述|
|----|:-----|
|[DiskSpaceHealthIndicator](http://github.com/spring-projects/spring-boot/tree/master/spring-boot-actuator/src/main/java/org/springframework/boot/actuate/health/DiskSpaceHealthIndicator.java)|低磁盘空间检测|
|[DataSourceHealthIndicator](http://github.com/spring-projects/spring-boot/tree/master/spring-boot-actuator/src/main/java/org/springframework/boot/actuate/health/DataSourceHealthIndicator.java)|检查是否能从DataSource获取连接|
|[MongoHealthIndicator](http://github.com/spring-projects/spring-boot/tree/master/spring-boot-actuator/src/main/java/org/springframework/boot/actuate/health/MongoHealthIndicator.java)|检查一个Mongo数据库是否可用（up）|
|[RabbitHealthIndicator](http://github.com/spring-projects/spring-boot/tree/master/spring-boot-actuator/src/main/java/org/springframework/boot/actuate/health/RabbitHealthIndicator.java)|检查一个Rabbit服务器是否可用（up）|
|[RedisHealthIndicator](http://github.com/spring-projects/spring-boot/tree/master/spring-boot-actuator/src/main/java/org/springframework/boot/actuate/health/RedisHealthIndicator.java)|检查一个Redis服务器是否可用（up）|
|[SolrHealthIndicator](http://github.com/spring-projects/spring-boot/tree/master/spring-boot-actuator/src/main/java/org/springframework/boot/actuate/health/SolrHealthIndicator.java)|检查一个Solr服务器是否可用（up）|

- 编写自定义HealthIndicators

想提供自定义健康信息，你可以注册实现了[HealthIndicator](http://github.com/spring-projects/spring-boot/tree/master/spring-boot-actuator/src/main/java/org/springframework/boot/actuate/health/HealthIndicator.java)接口的Spring beans。你需要提供一个health()方法的实现，并返回一个Health响应。Health响应需要包含一个status和可选的用于展示的详情。
```java
import org.springframework.boot.actuate.health.HealthIndicator;
import org.springframework.stereotype.Component;

@Component
public class MyHealth implements HealthIndicator {

    @Override
    public Health health() {
        int errorCode = check(); // perform some specific health check
        if (errorCode != 0) {
            return Health.down().withDetail("Error Code", errorCode).build();
        }
        return Health.up().build();
    }

}
```
除了Spring Boot预定义的[Status](http://github.com/spring-projects/spring-boot/tree/master/spring-boot-actuator/src/main/java/org/springframework/boot/actuate/health/Status.java)类型，Health也可以返回一个代表新的系统状态的自定义Status。在这种情况下，需要提供一个[HealthAggregator](http://github.com/spring-projects/spring-boot/tree/master/spring-boot-actuator/src/main/java/org/springframework/boot/actuate/health/HealthAggregator.java)接口的自定义实现，或使用management.health.status.order属性配置默认的实现。

例如，假设一个新的，代码为FATAL的Status被用于你的一个HealthIndicator实现中。为了配置严重程度，你需要将下面的配置添加到application属性文件中：
```java
management.health.status.order: DOWN, OUT_OF_SERVICE, UNKNOWN, UP
```
如果使用HTTP访问health端点，你可能想要注册自定义的status，并使用HealthMvcEndpoint进行映射。例如，你可以将FATAL映射为HttpStatus.SERVICE_UNAVAILABLE。

4. 自定义应用info信息

通过设置Spring属性info.*，你可以定义info端点暴露的数据。所有在info关键字下的Environment属性都将被自动暴露。例如，你可以将下面的配置添加到application.properties：
```java
info.app.name=MyService
info.app.description=My awesome service
info.app.version=1.0.0
```
- 在构建时期自动扩展info属性

你可以使用已经存在的构建配置自动扩展info属性，而不是对在项目构建配置中存在的属性进行硬编码。这在Maven和Gradle都是可能的。

**使用Maven自动扩展属性**

对于Maven项目，你可以使用资源过滤来自动扩展info属性。如果使用spring-boot-starter-parent，你可以通过`@..@`占位符引用Maven的'project properties'。
```java
project.artifactId=myproject
project.name=Demo
project.version=X.X.X.X
project.description=Demo project for info endpoint
info.build.artifact=@project.artifactId@
info.build.name=@project.name@
info.build.description=@project.description@
info.build.version=@project.version@
```
**注**：在上面的示例中，我们使用project.*来设置一些值以防止由于某些原因Maven的资源过滤没有开启。Maven目标`spring-boot:run`直接将`src/main/resources`添加到classpath下（出于热加载的目的）。这就绕过了资源过滤和自动扩展属性的特性。你可以使用`exec:java`替换该目标或自定义插件的配置，具体参考[plugin usage page](http://docs.spring.io/spring-boot/docs/1.3.0.BUILD-SNAPSHOT/maven-plugin/usage.html)。

如果你不使用starter parent，在你的pom.xml你需要添加（处于<build/>元素内）：
```xml
<resources>
    <resource>
        <directory>src/main/resources</directory>
        <filtering>true</filtering>
    </resource>
</resources>
```
和（处于<plugins/>内）：
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-resources-plugin</artifactId>
    <version>2.6</version>
    <configuration>
        <delimiters>
            <delimiter>@</delimiter>
        </delimiters>
    </configuration>
</plugin>
```
**使用Gradle自动扩展属性**

通过配置Java插件的processResources任务，你也可以自动使用来自Gradle项目的属性扩展info属性。
```java
processResources {
    expand(project.properties)
}
```
然后你可以通过占位符引用Gradle项目的属性：
```java
info.build.name=${name}
info.build.description=${description}
info.build.version=${version}
```
- Git提交信息

info端点的另一个有用特性是，当项目构建完成后，它可以发布关于你的git源码仓库状态的信息。如果在你的jar中包含一个git.properties文件，git.branch和git.commit属性将被加载。

对于Maven用户，`spring-boot-starter-parent` POM包含一个能够产生git.properties文件的预配置插件。只需要简单的将下面的声明添加到你的POM中：
```xml
<build>
    <plugins>
        <plugin>
            <groupId>pl.project13.maven</groupId>
            <artifactId>git-commit-id-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```
对于Gradle用户可以使用一个相似的插件[gradle-git](https://github.com/ajoberstar/gradle-git)，尽管为了产生属性文件可能需要稍微多点工作。

* 基于HTTP的监控和管理









