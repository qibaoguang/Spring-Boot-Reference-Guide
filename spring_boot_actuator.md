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

### 基于HTTP的监控和管理

如果你正在开发一个Spring MVC应用，Spring Boot执行器自动将所有启用的端点通过HTTP暴露出去。默认约定使用端点的id作为URL路径，例如，health暴露为/health。

* 保护敏感端点

如果你的项目中添加的有Spring Security，所有通过HTTP暴露的敏感端点都会受到保护。默认情况下会使用基本认证（basic authentication，用户名为user，密码为应用启动时在控制台打印的密码）。

你可以使用Spring属性改变用户名，密码和访问端点需要的安全角色。例如，你可能会在application.properties中添加下列配置：
```java
security.user.name=admin
security.user.password=secret
management.security.role=SUPERUSER
```

**注**：如果你不使用Spring Security，那你的HTTP端点就被公开暴露，你应该慎重考虑启用哪些端点。具体参考[Section 40.1, “Customizing endpoints”](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#production-ready-customizing-endpoints)。

* 自定义管理服务器的上下文路径

有时候将所有的管理端口划分到一个路径下是有用的。例如，你的应用可能已经将`/info`作为他用。你可以用`management.contextPath`属性为管理端口设置一个前缀：
```java
management.context-path=/manage
```
上面的application.properties示例将把端口从`/{id}`改为`/manage/{id}`（比如，/manage/info）。

* 自定义管理服务器的端口

对于基于云的部署，使用默认的HTTP端口暴露管理端点（endpoints）是明智的选择。然而，如果你的应用是在自己的数据中心运行，那你可能倾向于使用一个不同的HTTP端口来暴露端点。

`management.port`属性可以用来改变HTTP端口：
```java
management.port=8081
```
由于你的管理端口经常被防火墙保护，不对外暴露也就不需要保护管理端点，即使你的主要应用是安全的。在这种情况下，classpath下会存在Spring Security库，你可以设置下面的属性来禁用安全管理策略（management security）：
```java
management.security.enabled=false
```
（如果classpath下不存在Spring Security，那也就不需要显示的以这种方式来禁用安全管理策略，它甚至可能会破坏应用程序。）

* 自定义管理服务器的地址

你可以通过设置`management.address`属性来定义管理端点可以使用的地址。这在你只想监听内部或面向生产环境的网络，或只监听来自localhost的连接时非常有用。

下面的application.properties示例不允许远程管理连接：
```java
management.port=8081
management.address=127.0.0.1
```

* 禁用HTTP端点

如果不想使用HTTP暴露端点，你可以将管理端口设置为-1：
`management.port=-1`

* HTTP Health端点访问限制

通过health端点暴露的信息根据是否为匿名访问而不同。默认情况下，当匿名访问时，任何有关服务器的健康详情都被隐藏了，该端点只简单的指示服务器是运行（up）还是停止（down）。此外，当匿名访问时，响应会被缓存一个可配置的时间段以防止端点被用于'拒绝服务'攻击。`endpoints.health.time-to-live`属性被用来配置缓存时间（单位为毫秒），默认为1000毫秒，也就是1秒。

上述的限制可以被禁止，从而允许匿名用户完全访问health端点。想达到这个效果，可以将`endpoints.health.sensitive`设为`false`。

### 基于JMX的监控和管理

Java管理扩展（JMX）提供了一种标准的监控和管理应用的机制。默认情况下，Spring Boot在`org.springframework.boot`域下将管理端点暴露为JMX MBeans。

* 自定义MBean名称

MBean的名称通常产生于端点的id。例如，health端点被暴露为`org.springframework.boot/Endpoint/HealthEndpoint`。

如果你的应用包含多个Spring ApplicationContext，你会发现存在名称冲突。为了解决这个问题，你可以将`endpoints.jmx.uniqueNames`设置为true，这样MBean的名称总是唯一的。

你也可以自定义JMX域，所有的端点都在该域下暴露。这里有个application.properties示例：
```java
endpoints.jmx.domain=myapp
endpoints.jmx.uniqueNames=true
```
* 禁用JMX端点

如果不想通过JMX暴露端点，你可以将`spring.jmx.enabled`属性设置为false：
```java
spring.jmx.enabled=false
```

* 使用Jolokia通过HTTP实现JMX远程管理

Jolokia是一个JMX-HTTP桥，它提供了一种访问JMX beans的替代方法。想要使用Jolokia，只需添加`org.jolokia:jolokia-core`的依赖。例如，使用Maven需要添加下面的配置：
```xml
<dependency>
    <groupId>org.jolokia</groupId>
    <artifactId>jolokia-core</artifactId>
 </dependency>
```
在你的管理HTTP服务器上可以通过`/jolokia`访问Jolokia。

- 自定义Jolokia

Jolokia有很多配置，传统上一般使用servlet参数进行设置。使用Spring Boot，你可以在application.properties中通过把参数加上`jolokia.config.`前缀来设置：
```java
jolokia.config.debug=true
```
- 禁用Jolokia

如果你正在使用Jolokia，但不想让Spring Boot配置它，只需要简单的将`endpoints.jolokia.enabled`属性设置为false：
```java
endpoints.jolokia.enabled=false
```   

### 使用远程shell来进行监控和管理

Spring Boot支持集成一个称为'CRaSH'的Java shell。你可以在CRaSH中使用ssh或telnet命令连接到运行的应用。为了启用远程shell支持，你只需添加`spring-boot-starter-remote-shell`的依赖：
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-remote-shell</artifactId>
 </dependency>
```
**注**：如果想使用telnet访问，你还需添加对`org.crsh:crsh.shell.telnet`的依赖。

* 连接远程shell

默认情况下，远程shell监听端口2000以等待连接。默认用户名为`user`，密码为随机生成的，并且在输出日志中会显示。如果应用使用Spring Security，该shell默认使用[相同的配置](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#boot-features-security)。如果不是，将使用一个简单的认证策略，你可能会看到类似这样的信息：
```java
Using default password for shell access: ec03e16c-4cf4-49ee-b745-7c8255c1dd7e
```
Linux和OSX用户可以使用`ssh`连接远程shell，Windows用户可以下载并安装[PuTTY](http://www.putty.org/)。
```shell
$ ssh -p 2000 user@localhost

user@localhost's password:
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::  (v1.3.0.BUILD-SNAPSHOT) on myhost
```
输入help可以获取一系列命令的帮助。Spring boot提供`metrics`，`beans`，`autoconfig`和`endpoint`命令。

- 远程shell证书

你可以使用`shell.auth.simple.user.name`和`shell.auth.simple.user.password`属性配置自定义的连接证书。也可以使用Spring Security的AuthenticationManager处理登录职责。具体参考Javadoc[CrshAutoConfiguration](http://docs.spring.io/spring-boot/docs/1.3.0.BUILD-SNAPSHOT/api/org/springframework/boot/actuate/autoconfigure/CrshAutoConfiguration.html)和[ShellProperties](http://docs.spring.io/spring-boot/docs/1.3.0.BUILD-SNAPSHOT/api/org/springframework/boot/actuate/autoconfigure/ShellProperties.html)。

* 扩展远程shell

有很多有趣的方式可以用来扩展远程shell。

- 远程shell命令

你可以使用Groovy或Java编写其他的shell命令（具体参考CRaSH文档）。默认情况下，Spring Boot会搜索以下路径的命令：
1. `classpath*:/commands/**`
2. `classpath*:/crash/commands/**`

**注**：可以通过`shell.commandPathPatterns`属性改变搜索路径。

下面是一个从`src/main/resources/commands/hello.groovy`加载的'hello world'命令：
```java
package commands

import org.crsh.cli.Usage
import org.crsh.cli.Command

class hello {

    @Usage("Say Hello")
    @Command
    def main(InvocationContext context) {
        return "Hello"
    }

}
```
Spring Boot将一些额外属性添加到了InvocationContext，你可以在命令中访问它们：

|属性名称|描述|
|------|:------|
|spring.boot.version|Spring Boot的版本|
|spring.version|Spring框架的核心版本|
|spring.beanfactory|获取Spring的BeanFactory|
|spring.environment|获取Spring的Environment|

- 远程shell插件

除了创建新命令，也可以扩展CRaSH shell的其他特性。所有继承`org.crsh.plugin.CRaSHPlugin`的Spring Beans将自动注册到shell。

具体查看[CRaSH参考文档](http://www.crashub.org/)。

### 度量指标（Metrics）

Spring Boot执行器包括一个支持'gauge'和'counter'级别的度量指标服务。'gauge'记录一个单一值；'counter'记录一个增量（增加或减少）。同时，Spring Boot提供一个[PublicMetrics](http://github.com/spring-projects/spring-boot/tree/master/spring-boot-actuator/src/main/java/org/springframework/boot/actuate/endpoint/PublicMetrics.java)接口，你可以实现它，从而暴露以上两种机制不能记录的指标。具体参考[SystemPublicMetrics](http://github.com/spring-projects/spring-boot/tree/master/spring-boot-actuator/src/main/java/org/springframework/boot/actuate/endpoint/SystemPublicMetrics.java)。

所有HTTP请求的指标都被自动记录，所以如果点击`metrics`端点，你可能会看到类似以下的响应：
```javascript
{
    "counter.status.200.root": 20,
    "counter.status.200.metrics": 3,
    "counter.status.200.star-star": 5,
    "counter.status.401.root": 4,
    "gauge.response.star-star": 6,
    "gauge.response.root": 2,
    "gauge.response.metrics": 3,
    "classes": 5808,
    "classes.loaded": 5808,
    "classes.unloaded": 0,
    "heap": 3728384,
    "heap.committed": 986624,
    "heap.init": 262144,
    "heap.used": 52765,
    "mem": 986624,
    "mem.free": 933858,
    "processors": 8,
    "threads": 15,
    "threads.daemon": 11,
    "threads.peak": 15,
    "uptime": 494836,
    "instance.uptime": 489782,
    "datasource.primary.active": 5,
    "datasource.primary.usage": 0.25
}
```
此处我们可以看到基本的`memory`，`heap`，`class loading`，`processor`和`thread pool`信息，连同一些HTTP指标。在该实例中，`root`('/')，`/metrics` URLs分别返回20次，3次`HTTP 200`响应。同时可以看到`root` URL返回了4次`HTTP 401`（unauthorized）响应。双asterix（star-star）来自于被Spring MVC `/**`匹配到的一个请求（通常为一个静态资源）。

`gauge`级别展示了一个请求的最后响应时间。所以，`root`的最后请求被响应耗时2毫秒，`/metrics`耗时3毫秒。

* 系统指标

Spring Boot暴露以下系统指标：
- 系统内存总量（mem），单位:Kb
- 空闲内存数量（mem.free），单位:Kb
- 处理器数量（processors）
- 系统正常运行时间（uptime），单位:毫秒
- 应用上下文（就是一个应用实例）正常运行时间（instance.uptime），单位:毫秒
- 系统平均负载（systemload.average）
- 堆信息（heap，heap.committed，heap.init，heap.used），单位:Kb
- 线程信息（threads，thread.peak，thead.daemon）
- 类加载信息（classes，classes.loaded，classes.unloaded）
- 垃圾收集信息（gc.xxx.count, gc.xxx.time）

* 数据源指标

Spring Boot会为你应用中定义的支持的DataSource暴露以下指标：
- 最大连接数（datasource.xxx.max）
- 最小连接数（datasource.xxx.min）
- 活动连接数（datasource.xxx.active）
- 连接池的使用情况（datasource.xxx.usage）

所有的数据源指标共用`datasoure.`前缀。该前缀对每个数据源都非常合适：
- 如果是主数据源（唯一可用的数据源或存在的数据源中被@Primary标记的）前缀为datasource.primary
- 如果数据源bean名称以dataSource结尾，那前缀就是bean的名称去掉dataSource的部分（例如，batchDataSource的前缀是datasource.batch）
- 其他情况使用bean的名称作为前缀

通过注册一个自定义版本的DataSourcePublicMetrics bean，你可以覆盖部分或全部的默认行为。默认情况下，Spring Boot提供支持所有数据源的元数据；如果你喜欢的数据源恰好不被支持，你可以添加另外的DataSourcePoolMetadataProvider beans。具体参考DataSourcePoolMetadataProvidersConfiguration。

* Tomcat session指标

如果你使用Tomcat作为内嵌的servlet容器，session指标将被自动暴露出去。`httpsessions.active`和`httpsessions.max`提供了活动的和最大的session数量。

* 记录自己的指标

想要记录你自己的指标，只需将[CounterService](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-actuator/src/main/java/org/springframework/boot/actuate/metrics/CounterService.java)或[GaugeService](http://github.com/spring-projects/spring-boot/tree/master/spring-boot-actuator/src/main/java/org/springframework/boot/actuate/metrics/GaugeService.java)注入到你的bean中。CounterService暴露increment，decrement和reset方法；GaugeService提供一个submit方法。

下面是一个简单的示例，它记录了方法调用的次数：
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.actuate.metrics.CounterService;
import org.springframework.stereotype.Service;

@Service
public class MyService {

    private final CounterService counterService;

    @Autowired
    public MyService(CounterService counterService) {
        this.counterService = counterService;
    }

    public void exampleMethod() {
        this.counterService.increment("services.system.myservice.invoked");
    }

}
```
**注**：你可以将任何的字符串用作指标的名称，但最好遵循所选存储或图技术的指南。[Matt Aimonetti’s Blog](http://matt.aimonetti.net/posts/2013/06/26/practical-guide-to-graphite-monitoring/)中有一些好的关于图（Graphite）的指南。

* 添加你自己的公共指标

想要添加额外的，每次指标端点被调用时都会重新计算的度量指标，只需简单的注册其他的PublicMetrics实现bean(s)。默认情况下，端点会聚合所有这样的beans，通过定义自己的MetricsEndpoint可以轻易改变这种情况。

* 指标仓库

通过绑定一个[MetricRepository](http://github.com/spring-projects/spring-boot/tree/master/spring-boot-actuator/src/main/java/org/springframework/boot/actuate/metrics/repository/MetricRepository.java)来实现指标服务。`MetricRepository`负责存储和追溯指标信息。Spring Boot提供一个`InMemoryMetricRepository`和一个`RedisMetricRepository`（默认使用in-memory仓库），不过你可以编写自己的`MetricRepository`。`MetricRepository`接口实际是`MetricReader`接口和`MetricWriter`接口的上层组合。具体参考[Javadoc](http://docs.spring.io/spring-boot/docs/1.3.0.BUILD-SNAPSHOT/api/org/springframework/boot/actuate/metrics/repository/MetricRepository.html)

没有什么能阻止你直接将`MetricRepository`的数据导入应用中的后端存储，但我们建议你使用默认的`InMemoryMetricRepository`（如果担心堆使用情况，你可以使用自定义的Map实例），然后通过一个scheduled export job填充后端仓库（意思是先将数据保存到内存中，然后通过异步job将数据持久化到数据库，可以提高系统性能）。通过这种方式，你可以将指标数据缓存到内存中，然后通过低频率或批量导出来减少网络拥堵。Spring Boot提供一个`Exporter`接口及一些帮你开始的基本实现。

* Dropwizard指标

[Dropwizard ‘Metrics’库](https://dropwizard.github.io/metrics/)的用户会发现Spring Boot指标被发布到了`com.codahale.metrics.MetricRegistry`。当你声明对`io.dropwizard.metrics:metrics-core`库的依赖时会创建一个默认的`com.codahale.metrics.MetricRegistry` Spring bean；如果需要自定义，你可以注册自己的@Bean实例。来自于`MetricRegistry`的指标也是自动通过`/metrics`端点暴露的。

用户可以通过使用合适类型的指标名称作为前缀来创建Dropwizard指标（比如，`histogram.*`, `meter.*`）。

* 消息渠道集成

如果你的classpath下存在'Spring Messaging' jar，一个名为`metricsChannel`的`MessageChannel`将被自动创建（除非已经存在一个）。此外，所有的指标更新事件作为'messages'发布到该渠道上。订阅该渠道的客户端可以进行额外的分析或行动。

### 审计

Spring Boot执行器具有一个灵活的审计框架，一旦Spring Security处于活动状态（默认抛出'authentication success'，'failure'和'access denied'异常），它就会发布事件。这对于报告非常有用，同时可以基于认证失败实现一个锁定策略。

你也可以使用审计服务处理自己的业务事件。为此，你可以将存在的`AuditEventRepository`注入到自己的组件，并直接使用它，或者只是简单地通过Spring `ApplicationEventPublisher`发布`AuditApplicationEvent`（使用`ApplicationEventPublisherAware`）。

### 追踪（Tracing）

对于所有的HTTP请求Spring Boot自动启用追踪。你可以查看`trace`端点，并获取最近一些请求的基本信息：
```javascript
[{
    "timestamp": 1394343677415,
    "info": {
        "method": "GET",
        "path": "/trace",
        "headers": {
            "request": {
                "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8",
                "Connection": "keep-alive",
                "Accept-Encoding": "gzip, deflate",
                "User-Agent": "Mozilla/5.0 Gecko/Firefox",
                "Accept-Language": "en-US,en;q=0.5",
                "Cookie": "_ga=GA1.1.827067509.1390890128; ..."
                "Authorization": "Basic ...",
                "Host": "localhost:8080"
            },
            "response": {
                "Strict-Transport-Security": "max-age=31536000 ; includeSubDomains",
                "X-Application-Context": "application:8080",
                "Content-Type": "application/json;charset=UTF-8",
                "status": "200"
            }
        }
    }
},{
    "timestamp": 1394343684465,
    ...
}]
```
- 自定义追踪

如果需要追踪其他的事件，你可以将一个[TraceRepository](http://github.com/spring-projects/spring-boot/tree/master/spring-boot-actuator/src/main/java/org/springframework/boot/actuate/trace/TraceRepository.java)注入到你的Spring Beans中。`add`方法接收一个将被转化为JSON的`Map`结构，该数据将被记录下来。

默认情况下，使用的`InMemoryTraceRepository`将存储最新的100个事件。如果需要扩展该容量，你可以定义自己的`InMemoryTraceRepository`实例。如果需要，你可以创建自己的替代`TraceRepository`实现。

### 进程监控

在Spring Boot执行器中，你可以找到几个创建有利于进程监控的文件的类：
- `ApplicationPidFileWriter`创建一个包含应用PID的文件（默认位于应用目录，文件名为application.pid）
- `EmbeddedServerPortFileWriter`创建一个或多个包含内嵌服务器端口的文件（默认位于应用目录，文件名为application.port）

默认情况下，这些writers没有被激活，但你可以使用下面描述的任何方式来启用它们。

* 扩展属性

你需要激活`META-INF/spring.factories`文件里的listener(s)：
```java
org.springframework.context.ApplicationListener=\
org.springframework.boot.actuate.system.ApplicationPidFileWriter,
org.springframework.boot.actuate.system.EmbeddedServerPortFileWriter
```

* 以编程方式

你也可以通过调用`SpringApplication.addListeners(…)`方法来激活一个监听器，并传递相应的`Writer`对象。该方法允许你通过`Writer`构造器自定义文件名和路径。
