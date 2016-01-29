Spring Boot特性
===============
### SpringApplication
SpringApplication类提供了一种从main()方法启动Spring应用的便捷方式。在很多情况下，你只需委托给SpringApplication.run这个静态方法：
```java
public static void main(String[] args){
    SpringApplication.run(MySpringConfiguration.class, args);
}
```
* 自定义Banner
  
通过在classpath下添加一个banner.txt或设置banner.location来指定相应的文件可以改变启动过程中打印的banner。如果这个文件有特殊的编码，你可以使用banner.encoding设置它（默认为UTF-8）。

在banner.txt中可以使用如下的变量：

| 变量        | 描述     | 
| ----------- | :--------|
|${application.version}|MANIFEST.MF中声明的应用版本号，例如1.0|
|${application.formatted-version}|MANIFEST.MF中声明的被格式化后的应用版本号（被括号包裹且以v作为前缀），用于显示，例如(v1.0)|
|${spring-boot.version}|正在使用的Spring Boot版本号，例如1.2.2.BUILD-SNAPSHOT|
|${spring-boot.formatted-version}|正在使用的Spring Boot被格式化后的版本号（被括号包裹且以v作为前缀）,  用于显示，例如(v1.2.2.BUILD-SNAPSHOT)|

**注**：如果想以编程的方式产生一个banner，可以使用SpringBootApplication.setBanner(…)方法。使用org.springframework.boot.Banner接口，实现你自己的printBanner()方法。

* 自定义SpringApplication

如果默认的SpringApplication不符合你的口味，你可以创建一个本地的实例并自定义它。例如，关闭banner你可以这样写：
```java
public static void main(String[] args){
    SpringApplication app = new SpringApplication(MySpringConfiguration.class);
    app.setShowBanner(false);
    app.run(args);
}
```
**注**：传递给SpringApplication的构造器参数是spring beans的配置源。在大多数情况下，这些将是@Configuration类的引用，但它们也可能是XML配置或要扫描包的引用。

你也可以使用application.properties文件来配置SpringApplication。具体参考[Externalized 配置](#Externalized 配置)。查看配置选项的完整列表，可参考[SpringApplication Javadoc](http://docs.spring.io/spring-boot/docs/1.2.2.BUILD-SNAPSHOT/api/org/springframework/boot/SpringApplication.html).
    
* 流畅的构建API

如果你需要创建一个分层的ApplicationContext（多个具有父子关系的上下文），或你只是喜欢使用流畅的构建API，你可以使用SpringApplicationBuilder。SpringApplicationBuilder允许你以链式方式调用多个方法，包括可以创建层次结构的parent和child方法。
```java
new SpringApplicationBuilder()
    .showBanner(false)
    .sources(Parent.class)
    .child(Application.class)
    .run(args);
```
**注**：创建ApplicationContext层次时有些限制，比如，Web组件(components)必须包含在子上下文(child context)中，且相同的Environment即用于父上下文也用于子上下文中。具体参考[SpringApplicationBuilder javadoc](http://docs.spring.io/spring-boot/docs/1.2.2.BUILD-SNAPSHOT/api/org/springframework/boot/builder/SpringApplicationBuilder.html)

* Application事件和监听器

除了常见的Spring框架事件，比如[ContextRefreshedEvent](http://docs.spring.io/spring/docs/4.1.4.RELEASE/javadoc-api/org/springframework/context/event/ContextRefreshedEvent.html)，一个SpringApplication也发送一些额外的应用事件。一些事件实际上是在ApplicationContext被创建前触发的。

你可以使用多种方式注册事件监听器，最普通的是使用SpringApplication.addListeners(…)方法。在你的应用运行时，应用事件会以下面的次序发送：

1. 在运行开始，但除了监听器注册和初始化以外的任何处理之前，会发送一个ApplicationStartedEvent。
2. 在Environment将被用于已知的上下文，但在上下文被创建前，会发送一个ApplicationEnvironmentPreparedEvent。
3. 在refresh开始前，但在bean定义已被加载后，会发送一个ApplicationPreparedEvent。
4. 启动过程中如果出现异常，会发送一个ApplicationFailedEvent。

**注**：你通常不需要使用应用程序事件，但知道它们的存在会很方便（在某些场合可能会使用到）。在Spring内部，Spring Boot使用事件处理各种各样的任务。

* Web环境

一个SpringApplication将尝试为你创建正确类型的ApplicationContext。在默认情况下，使用AnnotationConfigApplicationContext或AnnotationConfigEmbeddedWebApplicationContext取决于你正在开发的是否是web应用。

用于确定一个web环境的算法相当简单（基于是否存在某些类）。如果需要覆盖默认行为，你可以使用setWebEnvironment(boolean webEnvironment)。通过调用setApplicationContextClass(…)，你可以完全控制ApplicationContext的类型。

**注**：当JUnit测试里使用SpringApplication时，调用setWebEnvironment(false)是可取的。

* 命令行启动器

如果你想获取原始的命令行参数，或一旦SpringApplication启动，你需要运行一些特定的代码，你可以实现CommandLineRunner接口。在所有实现该接口的Spring beans上将调用run(String… args)方法。
```java
import org.springframework.boot.*
import org.springframework.stereotype.*

@Component
public class MyBean implements CommandLineRunner {
    public void run(String... args) {
        // Do something...
    }
}
```
如果一些CommandLineRunner beans被定义必须以特定的次序调用，你可以额外实现org.springframework.core.Ordered接口或使用org.springframework.core.annotation.Order注解。

* Application退出

每个SpringApplication在退出时为了确保ApplicationContext被优雅的关闭，将会注册一个JVM的shutdown钩子。所有标准的Spring生命周期回调（比如，DisposableBean接口或@PreDestroy注解）都能使用。

此外，如果beans想在应用结束时返回一个特定的退出码（exit code），可以实现org.springframework.boot.ExitCodeGenerator接口。

### 外化配置

Spring Boot允许外化（externalize）你的配置，这样你能够在不同的环境下使用相同的代码。你可以使用properties文件，YAML文件，环境变量和命令行参数来外化配置。使用@Value注解，可以直接将属性值注入到你的beans中，并通过Spring的Environment抽象或绑定到结构化对象来访问。

Spring Boot使用一个非常特别的PropertySource次序来允许对值进行合理的覆盖，需要以下面的次序考虑属性：

1. 命令行参数
2. 来自于java:comp/env的JNDI属性
3. Java系统属性（System.getProperties()）
4. 操作系统环境变量
5. 只有在random.*里包含的属性会产生一个RandomValuePropertySource
6. 在打包的jar外的应用程序配置文件（application.properties，包含YAML和profile变量）
7. 在打包的jar内的应用程序配置文件（application.properties，包含YAML和profile变量）
8. 在@Configuration类上的@PropertySource注解
9. 默认属性（使用SpringApplication.setDefaultProperties指定）

下面是一个具体的示例（假设你开发一个使用name属性的@Component）：
```java
import org.springframework.stereotype.*
import org.springframework.beans.factory.annotation.*

@Component
public class MyBean {
    @Value("${name}")
    private String name;
    // ...
}
```
你可以将一个application.properties文件捆绑到jar内，用来提供一个合理的默认name属性值。当运行在生产环境时，可以在jar外提供一个application.properties文件来覆盖name属性。对于一次性的测试，你可以使用特定的命令行开关启动（比如，java -jar app.jar --name="Spring"）。

RandomValuePropertySource在注入随机值（比如，密钥或测试用例）时很有用。它能产生整数，longs或字符串，比如：
```java
my.secret=${random.value}
my.number=${random.int}
my.bignumber=${random.long}
my.number.less.than.ten=${random.int(10)}
my.number.in.range=${random.int[1024,65536]}
```
random.int*语法是OPEN value (,max) CLOSE，此处OPEN，CLOSE可以是任何字符，并且value，max是整数。如果提供max，那么value是最小的值，max是最大的值（不包含在内）。

* 访问命令行属性

默认情况下，SpringApplication将任何可选的命令行参数（以'--'开头，比如，--server.port=9000）转化为property，并将其添加到Spring Environment中。如上所述，命令行属性总是优先于其他属性源。

如果你不想将命令行属性添加到Environment里，你可以使用SpringApplication.setAddCommandLineProperties(false)来禁止它们。

* Application属性文件

SpringApplication将从以下位置加载application.properties文件，并把它们添加到Spring Environment中：

1. 当前目录下的一个/config子目录
2. 当前目录
3. 一个classpath下的/config包
4. classpath根路径（root）

这个列表是按优先级排序的（列表中位置高的将覆盖位置低的）。

**注**：你可以使用YAML（'.yml'）文件替代'.properties'。

如果不喜欢将application.properties作为配置文件名，你可以通过指定spring.config.name环境属性来切换其他的名称。你也可以使用spring.config.location环境属性来引用一个明确的路径（目录位置或文件路径列表以逗号分割）。
```shell
$ java -jar myproject.jar --spring.config.name=myproject
//or
$ java -jar myproject.jar --spring.config.location=classpath:/default.properties,classpath:/override.properties
```
如果spring.config.location包含目录（相对于文件），那它们应该以/结尾（在加载前，spring.config.name产生的名称将被追加到后面）。不管spring.config.location是什么值，默认的搜索路径classpath:,classpath:/config,file:,file:config/总会被使用。以这种方式，你可以在application.properties中为应用设置默认值，然后在运行的时候使用不同的文件覆盖它，同时保留默认配置。

**注**：如果你使用环境变量而不是系统配置，大多数操作系统不允许以句号分割（period-separated）的key名称，但你可以使用下划线（underscores）代替（比如，使用SPRING_CONFIG_NAME代替spring.config.name）。如果你的应用运行在一个容器中，那么JNDI属性（java:comp/env）或servlet上下文初始化参数可以用来取代环境变量或系统属性，当然也可以使用环境变量或系统属性。

* 特定的Profile属性

除了application.properties文件，特定配置属性也能通过命令惯例application-{profile}.properties来定义。特定Profile属性从跟标准application.properties相同的路径加载，并且特定profile文件会覆盖默认的配置。

* 属性占位符

当application.properties里的值被使用时，它们会被存在的Environment过滤，所以你能够引用先前定义的值（比如，系统属性）。
```java
app.name=MyApp
app.description=${app.name} is a Spring Boot application
```
**注**：你也能使用相应的技巧为存在的Spring Boot属性创建'短'变量，具体参考[Section 63.3, “Use ‘short’ command line arguments”](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#howto-use-short-command-line-arguments)。

* 使用YAML代替Properties

[YAML](http://yaml.org/)是JSON的一个超集，也是一种方便的定义层次配置数据的格式。无论你何时将[SnakeYAML ](http://code.google.com/p/snakeyaml/)库放到classpath下，SpringApplication类都会自动支持YAML作为properties的替换。

**注**：如果你使用'starter POMs'，spring-boot-starter会自动提供SnakeYAML。

* 加载YAML

Spring框架提供两个便利的类用于加载YAML文档，YamlPropertiesFactoryBean会将YAML作为Properties来加载，YamlMapFactoryBean会将YAML作为Map来加载。

示例：
```json
environments:
    dev:
        url: http://dev.bar.com
        name: Developer Setup
    prod:
        url: http://foo.bar.com
        name: My Cool App
```
上面的YAML文档会被转化到下面的属性中：
```java
environments.dev.url=http://dev.bar.com
environments.dev.name=Developer Setup
environments.prod.url=http://foo.bar.com
environments.prod.name=My Cool App
```
YAML列表被表示成使用[index]间接引用作为属性keys的形式，例如下面的YAML：
```json
my:
   servers:
       - dev.bar.com
       - foo.bar.com
```
将会转化到下面的属性中:
```java
my.servers[0]=dev.bar.com
my.servers[1]=foo.bar.com
```
使用Spring DataBinder工具绑定那样的属性（这是@ConfigurationProperties做的事），你需要确定目标bean中有个java.util.List或Set类型的属性，并且需要提供一个setter或使用可变的值初始化它，比如，下面的代码将绑定上面的属性：
```java
@ConfigurationProperties(prefix="my")
public class Config {
    private List<String> servers = new ArrayList<String>();
    public List<String> getServers() {
        return this.servers;
    }
}
```
* 在Spring环境中使用YAML暴露属性

YamlPropertySourceLoader类能够用于将YAML作为一个PropertySource导出到Sprig Environment。这允许你使用熟悉的@Value注解和占位符语法访问YAML属性。

* Multi-profile YAML文档

你可以在单个文件中定义多个特定配置（profile-specific）的YAML文档，并通过一个spring.profiles key标示应用的文档。例如：
```json
server:
    address: 192.168.1.100
---
spring:
    profiles: development
server:
    address: 127.0.0.1
---
spring:
    profiles: production
server:
    address: 192.168.1.120
```
在上面的例子中，如果development配置被激活，那server.address属性将是127.0.0.1。如果development和production配置（profiles）没有启用，则该属性的值将是192.168.1.100。

* YAML缺点

YAML文件不能通过@PropertySource注解加载。所以，在这种情况下，如果需要使用@PropertySource注解的方式加载值，那就要使用properties文件。

* 类型安全的配置属性

使用@Value("${property}")注解注入配置属性有时可能比较笨重，特别是需要使用多个properties或你的数据本身有层次结构。为了控制和校验你的应用配置，Spring Boot提供一个允许强类型beans的替代方法来使用properties。

示例：
```java
@Component
@ConfigurationProperties(prefix="connection")
public class ConnectionSettings {
    private String username;
    private InetAddress remoteAddress;
    // ... getters and setters
}
```
当@EnableConfigurationProperties注解应用到你的@Configuration时，任何被@ConfigurationProperties注解的beans将自动被Environment属性配置。这种风格的配置特别适合与SpringApplication的外部YAML配置进行配合使用。
```json
# application.yml
connection:
    username: admin
    remoteAddress: 192.168.1.1
# additional configuration as required
```
为了使用@ConfigurationProperties beans，你可以使用与其他任何bean相同的方式注入它们。
```java
@Service
public class MyService {
    @Autowired
    private ConnectionSettings connection;
     //...
    @PostConstruct
    public void openConnection() {
        Server server = new Server();
        this.connection.configure(server);
    }
}
```
你可以通过在@EnableConfigurationProperties注解中直接简单的列出属性类来快捷的注册@ConfigurationProperties bean的定义。
```java
@Configuration
@EnableConfigurationProperties(ConnectionSettings.class)
public class MyConfiguration {
}
```
**注**：使用@ConfigurationProperties能够产生可被IDEs使用的元数据文件。具体参考[Appendix B, Configuration meta-data](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#configuration-metadata)。

* 第3方配置

正如使用@ConfigurationProperties注解一个类，你也可以在@Bean方法上使用它。当你需要绑定属性到不受你控制的第三方组件时，这种方式非常有用。

为了从Environment属性配置一个bean，将@ConfigurationProperties添加到它的bean注册过程：
```java
@ConfigurationProperties(prefix = "foo")
@Bean
public FooComponent fooComponent() {
    ...
}
```
和上面ConnectionSettings的示例方式相同，任何以foo为前缀的属性定义都会被映射到FooComponent上。

* 松散的绑定（Relaxed binding）

Spring Boot使用一些宽松的规则用于绑定Environment属性到@ConfigurationProperties beans，所以Environment属性名和bean属性名不需要精确匹配。常见的示例中有用的包括虚线分割（比如，context--path绑定到contextPath）和将环境属性转为大写字母（比如，PORT绑定port）。

示例：
```java
@Component
@ConfigurationProperties(prefix="person")
public class ConnectionSettings {
    private String firstName;
}
```
下面的属性名都能用于上面的@ConfigurationProperties类：

| 属性        | 说明   |
| --------    | :----- |
|person.firstName|标准驼峰规则|
|person.first-name|虚线表示，推荐用于.properties和.yml文件中|
|PERSON_FIRST_NAME|大写形式，使用系统环境变量时推荐|

Spring会尝试强制外部的应用属性在绑定到@ConfigurationProperties beans时类型是正确的。如果需要自定义类型转换，你可以提供一个ConversionService bean（bean id为conversionService）或自定义属性编辑器（通过一个CustomEditorConfigurer bean）。

* @ConfigurationProperties校验 

Spring Boot将尝试校验外部的配置，默认使用JSR-303（如果在classpath路径中）。你可以轻松的为你的@ConfigurationProperties类添加JSR-303 javax.validation约束注解：
```java
@Component
@ConfigurationProperties(prefix="connection")
public class ConnectionSettings {
    @NotNull
    private InetAddress remoteAddress;
    // ... getters and setters
}
```
你也可以通过创建一个叫做configurationPropertiesValidator的bean来添加自定义的Spring Validator。

**注**：spring-boot-actuator模块包含一个暴露所有@ConfigurationProperties beans的端点。简单地将你的web浏览器指向/configprops或使用等效的JMX端点。具体参考[Production ready features](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#production-ready-endpoints)。

### Profiles
Spring Profiles提供了一种隔离应用程序配置的方式，并让这些配置只能在特定的环境下生效。任何@Component或@Configuration都能被@Profile标记，从而限制加载它的时机。
```java
@Configuration
@Profile("production")
public class ProductionConfiguration {
    // ...
}
```
以正常的Spring方式，你可以使用一个spring.profiles.active的Environment属性来指定哪个配置生效。你可以使用平常的任何方式来指定该属性，例如，可以将它包含到你的application.properties中：
```java
spring.profiles.active=dev,hsqldb
```
或使用命令行开关：
```shell
--spring.profiles.active=dev,hsqldb
```
* 添加激活的配置(profiles)

spring.profiles.active属性和其他属性一样都遵循相同的排列规则，最高的PropertySource获胜。也就是说，你可以在application.properties中指定生效的配置，然后使用命令行开关替换它们。

有时，将特定的配置属性添加到生效的配置中而不是替换它们是有用的。spring.profiles.include属性可以用来无条件的添加生效的配置。SpringApplication的入口点也提供了一个用于设置额外配置的Java API（比如，在那些通过spring.profiles.active属性生效的配置之上）：参考setAdditionalProfiles()方法。

示例：当一个应用使用下面的属性，并用`--spring.profiles.active=prod`开关运行，那proddb和prodmq配置也会生效：
```java
---
my.property: fromyamlfile
---
spring.profiles: prod
spring.profiles.include: proddb,prodmq
```
**注**：spring.profiles属性可以定义到一个YAML文档中，用于决定什么时候该文档被包含进配置中。具体参考[Section 63.6, “Change configuration depending on the environment”](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#howto-change-configuration-depending-on-the-environment)

* 以编程方式设置profiles

在应用运行前，你可以通过调用SpringApplication.setAdditionalProfiles(…)方法，以编程的方式设置生效的配置。使用Spring的ConfigurableEnvironment接口激动配置也是可行的。

* Profile特定配置文件

application.properties（或application.yml）和通过@ConfigurationProperties引用的文件这两种配置特定变种都被当作文件来加载的，具体参考[Section 23.3, “Profile specific properties”](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#boot-features-external-config-profile-specific-properties)。

### 日志
Spring Boot内部日志系统使用的是[Commons Logging](http://commons.apache.org/logging)，但开放底层的日志实现。默认为会[Java Util Logging](http://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html), [Log4J](http://logging.apache.org/log4j/), [Log4J2](http://logging.apache.org/log4j/2.x/)和[Logback](http://logback.qos.ch/)提供配置。每种情况下都会预先配置使用控制台输出，也可以使用可选的文件输出。

默认情况下，如果你使用'Starter POMs'，那么就会使用Logback记录日志。为了确保那些使用Java Util Logging, Commons Logging, Log4J或SLF4J的依赖库能够正常工作，正确的Logback路由也被包含进来。

**注**：如果上面的列表看起来令人困惑，不要担心，Java有很多可用的日志框架。通常，你不需要改变日志依赖，Spring Boot默认的就能很好的工作。

* 日志格式

Spring Boot默认的日志输出格式如下：
```java
2014-03-05 10:57:51.112  INFO 45469 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet Engine: Apache Tomcat/7.0.52
2014-03-05 10:57:51.253  INFO 45469 --- [ost-startStop-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2014-03-05 10:57:51.253  INFO 45469 --- [ost-startStop-1] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 1358 ms
2014-03-05 10:57:51.698  INFO 45469 --- [ost-startStop-1] o.s.b.c.e.ServletRegistrationBean        : Mapping servlet: 'dispatcherServlet' to [/]
2014-03-05 10:57:51.702  INFO 45469 --- [ost-startStop-1] o.s.b.c.embedded.FilterRegistrationBean  : Mapping filter: 'hiddenHttpMethodFilter' to: [/*]
```
输出的节点（items）如下：

1. 日期和时间 - 精确到毫秒，且易于排序。
2. 日志级别 - ERROR, WARN, INFO, DEBUG 或 TRACE。
3. Process ID。
4. 一个用于区分实际日志信息开头的---分隔符。
5. 线程名 - 包括在方括号中（控制台输出可能会被截断）。
6. 日志名 - 通常是源class的类名（缩写）。
7. 日志信息。

* 控制台输出

默认的日志配置会在写日志消息时将它们回显到控制台。默认，ERROR, WARN和INFO级别的消息会被记录。可以在启动应用时，通过`--debug`标识开启控制台的DEBUG级别日志记录。
```shell
$ java -jar myapp.jar --debug
```
如果你的终端支持ANSI，为了增加可读性将会使用彩色的日志输出。你可以设置`spring.output.ansi.enabled`为一个[支持的值](http://docs.spring.io/spring-boot/docs/1.2.2.BUILD-SNAPSHOT/api/org/springframework/boot/ansi/AnsiOutput.Enabled.html)来覆盖自动检测。

* 文件输出

默认情况下，Spring Boot只会将日志记录到控制台而不会写进日志文件。如果除了输出到控制台你还想写入到日志文件，那你需要设置`logging.file`或`logging.path`属性（例如在你的application.properties中）。

下表显示如何组合使用`logging.*`：

|logging.file|logging.path| 示例 | 描述  |
| --------   | :-----  | :-----  | :-----|
|  (none)    | (none)  |         | 只记录到控制台 |
|Specific file|(none)|my.log|写到特定的日志文件里，名称可以是一个精确的位置或相对于当前目录|
|(none)|Specific folder|/var/log|写到特定文件夹下的spring.log里，名称可以是一个精确的位置或相对于当前目录|

日志文件每达到10M就会被轮换（分割），和控制台一样，默认记录ERROR, WARN和INFO级别的信息。

* 日志级别

所有支持的日志系统在Spring的Environment（例如在application.properties里）都有通过'logging.level.*=LEVEL'（'LEVEL'是TRACE, DEBUG, INFO, WARN, ERROR, FATAL, OFF中的一个）设置的日志级别。

示例：application.properties
```java
logging.level.org.springframework.web: DEBUG
logging.level.org.hibernate: ERROR
```
* 自定义日志配置

通过将适当的库添加到classpath，可以激活各种日志系统。然后在classpath的根目录(root)或通过Spring Environment的`logging.config`属性指定的位置提供一个合适的配置文件来达到进一步的定制（注意由于日志是在ApplicationContext被创建之前初始化的，所以不可能在Spring的@Configuration文件中，通过@PropertySources控制日志。系统属性和平常的Spring Boot外部配置文件能正常工作）。

根据你的日志系统，下面的文件会被加载：

| 日志系统        | 定制   |
| --------   | :-----:  | 
|Logback|logback.xml|
|Log4j|log4j.properties或log4j.xml|
|Log4j2|log4j2.xml|
|JDK (Java Util Logging)|logging.properties|

为了帮助定制一些其他的属性，从Spring的Envrionment转换到系统属性：

| Spring Environment| System Property| 评价 |
| --------   | :-----:  | :----:  |
|logging.file|LOG_FILE|如果定义，在默认的日志配置中使用|
|logging.path|LOG_PATH|如果定义，在默认的日志配置中使用|
|PID|PID|当前的处理进程(process)ID（如果能够被发现且还没有作为操作系统环境变量被定义）|

所有支持的日志系统在解析它们的配置文件时都能查询系统属性。具体可以参考spring-boot.jar中的默认配置。

**注**：在运行可执行的jar时，Java Util Logging有类加载问题，我们建议你尽可能避免使用它。

### 开发Web应用
Spring Boot非常适合开发web应用程序。你可以使用内嵌的Tomcat，Jetty或Undertow轻轻松松地创建一个HTTP服务器。大多数的web应用都使用spring-boot-starter-web模块进行快速搭建和运行。

* Spring Web MVC框架

Spring Web MVC框架（通常简称为"Spring MVC"）是一个富"模型，视图，控制器"的web框架。
Spring MVC允许你创建特定的@Controller或@RestController beans来处理传入的HTTP请求。
使用@RequestMapping注解可以将控制器中的方法映射到相应的HTTP请求。

示例：
```java
@RestController
@RequestMapping(value="/users")
public class MyRestController {

    @RequestMapping(value="/{user}", method=RequestMethod.GET)
    public User getUser(@PathVariable Long user) {
        // ...
    }

    @RequestMapping(value="/{user}/customers", method=RequestMethod.GET)
    List<Customer> getUserCustomers(@PathVariable Long user) {
        // ...
    }

    @RequestMapping(value="/{user}", method=RequestMethod.DELETE)
    public User deleteUser(@PathVariable Long user) {
        // ...
    }
}
```
* Spring MVC自动配置

Spring Boot为Spring MVC提供适用于多数应用的自动配置功能。在Spring默认基础上，自动配置添加了以下特性：

1. 引入ContentNegotiatingViewResolver和BeanNameViewResolver beans。
2. 对静态资源的支持，包括对WebJars的支持。
3. 自动注册Converter，GenericConverter，Formatter beans。
4. 对HttpMessageConverters的支持。
5. 自动注册MessageCodeResolver。
6. 对静态index.html的支持。
7. 对自定义Favicon的支持。

如果想全面控制Spring MVC，你可以添加自己的@Configuration，并使用@EnableWebMvc对其注解。如果想保留Spring Boot MVC的特性，并只是添加其他的[MVC配置](http://docs.spring.io/spring/docs/4.1.4.RELEASE/spring-framework-reference/htmlsingle#mvc)(拦截器，formatters，视图控制器等)，你可以添加自己的WebMvcConfigurerAdapter类型的@Bean（不使用@EnableWebMvc注解）。

* HttpMessageConverters

Spring MVC使用HttpMessageConverter接口转换HTTP请求和响应。合理的缺省值被包含的恰到好处（out of the box），例如对象可以自动转换为JSON（使用Jackson库）或XML（如果Jackson XML扩展可用则使用它，否则使用JAXB）。字符串默认使用UTF-8编码。

如果需要添加或自定义转换器，你可以使用Spring Boot的HttpMessageConverters类：
```java
import org.springframework.boot.autoconfigure.web.HttpMessageConverters;
import org.springframework.context.annotation.*;
import org.springframework.http.converter.*;

@Configuration
public class MyConfiguration {

    @Bean
    public HttpMessageConverters customConverters() {
        HttpMessageConverter<?> additional = ...
        HttpMessageConverter<?> another = ...
        return new HttpMessageConverters(additional, another);
    }
}
```
任何在上下文中出现的HttpMessageConverter bean将会添加到converters列表，你可以通过这种方式覆盖默认的转换器（converters）。

* MessageCodesResolver

Spring MVC有一个策略，用于从绑定的errors产生用来渲染错误信息的错误码：MessageCodesResolver。如果设置`spring.mvc.message-codes-resolver.format`属性为`PREFIX_ERROR_CODE`或`POSTFIX_ERROR_CODE`（具体查看`DefaultMessageCodesResolver.Format`枚举值），Spring Boot会为你创建一个MessageCodesResolver。

* 静态内容

默认情况下，Spring Boot从classpath下一个叫/static（/public，/resources或/META-INF/resources）的文件夹或从ServletContext根目录提供静态内容。这使用了Spring MVC的ResourceHttpRequestHandler，所以你可以通过添加自己的WebMvcConfigurerAdapter并覆写addResourceHandlers方法来改变这个行为（加载静态文件）。

在一个单独的web应用中，容器默认的servlet是开启的，如果Spring决定不处理某些请求，默认的servlet作为一个回退（降级）将从ServletContext根目录加载内容。大多数时候，这不会发生（除非你修改默认的MVC配置），因为Spring总能够通过DispatcherServlet处理请求。

此外，上述标准的静态资源位置有个例外情况是[Webjars内容](http://www.webjars.org/)。任何在/webjars/**路径下的资源都将从jar文件中提供，只要它们以Webjars的格式打包。

**注**：如果你的应用将被打包成jar，那就不要使用src/main/webapp文件夹。尽管该文件夹是一个共同的标准，但它仅在打包成war的情况下起作用，并且如果产生一个jar，多数构建工具都会静悄悄的忽略它。

* 模板引擎

正如REST web服务，你也可以使用Spring MVC提供动态HTML内容。Spring MVC支持各种各样的模板技术，包括Velocity, FreeMarker和JSPs。很多其他的模板引擎也提供它们自己的Spring MVC集成。

Spring Boot为以下的模板引擎提供自动配置支持：

1. [FreeMarker](http://freemarker.org/docs/)
2. [Groovy](http://beta.groovy-lang.org/docs/groovy-2.3.0/html/documentation/markup-template-engine.html)
3. [Thymeleaf](http://www.thymeleaf.org/)
4. [Velocity](http://velocity.apache.org/)

**注**：如果可能的话，应该忽略JSPs，因为在内嵌的servlet容器使用它们时存在一些[已知的限制](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#boot-features-jsp-limitations)。

当你使用这些引擎的任何一种，并采用默认的配置，你的模板将会从src/main/resources/templates目录下自动加载。

**注**：IntelliJ IDEA根据你运行应用的方式会对classpath进行不同的整理。在IDE里通过main方法运行你的应用跟从Maven或Gradle或打包好的jar中运行相比会导致不同的顺序。这可能导致Spring Boot不能从classpath下成功地找到模板。如果遇到这个问题，你可以在IDE里重新对classpath进行排序，将模块的类和资源放到第一位。或者，你可以配置模块的前缀为classpath*:/templates/，这样会查找classpath下的所有模板目录。

* 错误处理

Spring Boot默认提供一个/error映射用来以合适的方式处理所有的错误，并且它在servlet容器中注册了一个全局的
错误页面。对于机器客户端（相对于浏览器而言，浏览器偏重于人的行为），它会产生一个具有详细错误，HTTP状态，异常信息的JSON响应。对于浏览器客户端，它会产生一个白色标签样式（whitelabel）的错误视图，该视图将以HTML格式显示同样的数据（可以添加一个解析为erro的View来自定义它）。为了完全替换默认的行为，你可以实现ErrorController，并注册一个该类型的bean定义，或简单地添加一个ErrorAttributes类型的bean以使用现存的机制，只是替换显示的内容。

如果在某些条件下需要比较多的错误页面，内嵌的servlet容器提供了一个统一的Java DSL（领域特定语言）来自定义错误处理。
示例：
```java
@Bean
public EmbeddedServletContainerCustomizer containerCustomizer(){
    return new MyCustomizer();
}

// ...
private static class MyCustomizer implements EmbeddedServletContainerCustomizer {
    @Override
    public void customize(ConfigurableEmbeddedServletContainer container) {
        container.addErrorPages(new ErrorPage(HttpStatus.BAD_REQUEST, "/400"));
    }
}
```
你也可以使用常规的Spring MVC特性来处理错误，比如[@ExceptionHandler方法](http://docs.spring.io/spring/docs/4.1.4.RELEASE/spring-framework-reference/htmlsingle/#mvc-exceptionhandlers)和[@ControllerAdvice](http://docs.spring.io/spring/docs/4.1.4.RELEASE/spring-framework-reference/htmlsingle/#mvc-ann-controller-advice)。ErrorController将会捡起任何没有处理的异常。

N.B. 如果你为一个路径注册一个ErrorPage，最终被一个过滤器（Filter）处理（对于一些非Spring web框架，像Jersey和Wicket这很常见），然后过滤器需要显式注册为一个ERROR分发器（dispatcher）。
```java
@Bean
public FilterRegistrationBean myFilter() {
    FilterRegistrationBean registration = new FilterRegistrationBean();
    registration.setFilter(new MyFilter());
    ...
    registration.setDispatcherTypes(EnumSet.allOf(DispatcherType.class));
    return registration;
}
```
**注**：默认的FilterRegistrationBean没有包含ERROR分发器类型。

* Spring HATEOAS

如果你正在开发一个使用超媒体的RESTful API，Spring Boot将为Spring HATEOAS提供自动配置，这在多数应用中都工作良好。自动配置替换了对使用@EnableHypermediaSupport的需求，并注册一定数量的beans来简化构建基于超媒体的应用，这些beans包括一个LinkDiscoverer和配置好的用于将响应正确编排为想要的表示的ObjectMapper。ObjectMapper可以根据spring.jackson.*属性或一个存在的Jackson2ObjectMapperBuilder bean进行自定义。

通过使用@EnableHypermediaSupport，你可以控制Spring HATEOAS的配置。注意这会禁用上述的对ObjectMapper的自定义。

* JAX-RS和Jersey

如果喜欢JAX-RS为REST端点提供的编程模型，你可以使用可用的实现替代Spring MVC。如果在你的应用上下文中将Jersey 1.x和Apache Celtix的Servlet或Filter注册为一个@Bean，那它们工作的相当好。Jersey 2.x有一些原生的Spring支持，所以我们会在Spring Boot为它提供自动配置支持，连同一个启动器（starter）。

想要开始使用Jersey 2.x只需要加入spring-boot-starter-jersey依赖，然后你需要一个ResourceConfig类型的@Bean，用于注册所有的端点（endpoints）。
```java
@Component
public class JerseyConfig extends ResourceConfig {
    public JerseyConfig() {
        register(Endpoint.class);
    }
}
```
所有注册的端点都应该被@Components和HTTP资源annotations（比如@GET）注解。
```java
@Component
@Path("/hello")
public class Endpoint {
    @GET
    public String message() {
        return "Hello";
    }
}
```
由于Endpoint是一个Spring组件（@Component），所以它的生命周期受Spring管理，并且你可以使用@Autowired添加依赖及使用@Value注入外部配置。Jersey servlet将被注册，并默认映射到/*。你可以将@ApplicationPath添加到ResourceConfig来改变该映射。

默认情况下，Jersey将在一个ServletRegistrationBean类型的@Bean中被设置成名称为jerseyServletRegistration的Servlet。通过创建自己的相同名称的bean，你可以禁止或覆盖这个bean。你也可以通过设置`spring.jersey.type=filter`来使用一个Filter代替Servlet（在这种情况下，被覆盖或替换的@Bean是jerseyFilterRegistration）。该servlet有@Order属性，你可以通过`spring.jersey.filter.order`进行设置。不管是Servlet还是Filter注册都可以使用spring.jersey.init.*定义一个属性集合作为初始化参数传递过去。

这里有一个[Jersey示例](http://github.com/spring-projects/spring-boot/tree/master/spring-boot-samples/spring-boot-sample-jersey)，你可以查看如何设置相关事项。

* 内嵌servlet容器支持

Spring Boot支持内嵌的Tomcat, Jetty和Undertow服务器。多数开发者只需要使用合适的'Starter POM'来获取一个完全配置好的实例即可。默认情况下，内嵌的服务器会在8080端口监听HTTP请求。

**1.Servlets和Filters**

当使用内嵌的servlet容器时，你可以直接将servlet和filter注册为Spring的beans。在配置期间，如果你想引用来自application.properties的值，这是非常方便的。默认情况下，如果上下文只包含单一的Servlet，那它将被映射到根路径（/）。在多Servlet beans的情况下，bean的名称将被用作路径的前缀。过滤器会被映射到/*。
    
如果基于约定（convention-based）的映射不够灵活，你可以使用ServletRegistrationBean和FilterRegistrationBean类实现完全的控制。如果你的bean实现了ServletContextInitializer接口，也可以直接注册它们。

**2.EmbeddedWebApplicationContext**

Spring Boot底层使用了一个新的ApplicationContext类型，用于对内嵌servlet容器的支持。EmbeddedWebApplicationContext是一个特殊类型的WebApplicationContext，它通过搜索一个单一的EmbeddedServletContainerFactory bean来启动自己。通常，TomcatEmbeddedServletContainerFactory，JettyEmbeddedServletContainerFactory或UndertowEmbeddedServletContainerFactory将被自动配置。

**注**：你通常不需要知道这些实现类。大多数应用将被自动配置，并根据你的行为创建合适的ApplicationContext和EmbeddedServletContainerFactory。

**3.自定义内嵌servlet容器**

常见的Servlet容器设置可以通过Spring Environment属性进行配置。通常，你会把这些属性定义到application.properties文件中。
常见的服务器设置包括：

1. server.port - 进来的HTTP请求的监听端口号
2. server.address - 绑定的接口地址
3. server.sessionTimeout - session超时时间

具体参考[ServerProperties](http://github.com/spring-projects/spring-boot/tree/master/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/web/ServerProperties.java)。

**编程方式的自定义**

如果需要以编程的方式配置内嵌的servlet容器，你可以注册一个实现EmbeddedServletContainerCustomizer接口的Spring bean。EmbeddedServletContainerCustomizer提供对ConfigurableEmbeddedServletContainer的访问，ConfigurableEmbeddedServletContainer包含很多自定义的setter方法。
```java
import org.springframework.boot.context.embedded.*;
import org.springframework.stereotype.Component;

@Component
public class CustomizationBean implements EmbeddedServletContainerCustomizer {
    @Override
    public void customize(ConfigurableEmbeddedServletContainer container) {
        container.setPort(9000);
    }
}
```
**直接自定义ConfigurableEmbeddedServletContainer**

如果上面的自定义手法过于受限，你可以自己注册TomcatEmbeddedServletContainerFactory，JettyEmbeddedServletContainerFactory或UndertowEmbeddedServletContainerFactory。
```java
@Bean
public EmbeddedServletContainerFactory servletContainer() {
    TomcatEmbeddedServletContainerFactory factory = new TomcatEmbeddedServletContainerFactory();
    factory.setPort(9000);
    factory.setSessionTimeout(10, TimeUnit.MINUTES);
    factory.addErrorPages(new ErrorPage(HttpStatus.NOT_FOUND, "/notfound.html");
    return factory;
}
```
很多可选的配置都提供了setter方法，也提供了一些受保护的钩子方法以满足你的某些特殊需求。具体参考相关文档。

**4.JSP的限制**

在内嵌的servlet容器中运行一个Spring Boot应用时（并打包成一个可执行的存档archive），容器对JSP的支持有一些限制。

1. tomcat只支持war的打包方式，不支持可执行的jar。
2. 内嵌的Jetty目前不支持JSPs。
3. Undertow不支持JSPs。

这里有个[JSP示例](http://github.com/spring-projects/spring-boot/tree/master/spring-boot-samples/spring-boot-sample-web-jsp)，你可以查看如何设置相关事项。

### 安全
如果Spring Security在classpath下，那么web应用默认对所有的HTTP路径（也称为终点，端点，表示API的具体网址）使用'basic'认证。为了给web应用添加方法级别的保护，你可以添加@EnableGlobalMethodSecurity并使用想要的设置。其他信息参考[Spring Security Reference](http://docs.spring.io/spring-security/site/docs/3.2.5.RELEASE/reference/htmlsingle#jc-method)。

默认的AuthenticationManager有一个单一的user（'user'的用户名和随机密码会在应用启动时以INFO日志级别打印出来）。如下：
```java
Using default security password: 78fa095d-3f4c-48b1-ad50-e24c31d5cf35
```
**注**：如果你对日志配置进行微调，确保`org.springframework.boot.autoconfigure.security`类别能记录INFO信息，否则默认的密码不会被打印。

你可以通过提供`security.user.password`改变默认的密码。这些和其他有用的属性通过[SecurityProperties](http://github.com/spring-projects/spring-boot/tree/master/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/security/SecurityProperties.java)（以security为前缀的属性）被外部化了。

默认的安全配置（security configuration）是在SecurityAutoConfiguration和导入的类中实现的（SpringBootWebSecurityConfiguration用于web安全，AuthenticationManagerConfiguration用于与非web应用也相关的认证配置）。你可以添加一个@EnableWebSecurity bean来彻底关掉Spring Boot的默认配置。为了对它进行自定义，你需要使用外部的属性配置和WebSecurityConfigurerAdapter类型的beans（比如，添加基于表单的登陆）。在[Spring Boot示例](http://github.com/spring-projects/spring-boot/tree/master/spring-boot-samples/)里有一些安全相关的应用可以带你体验常见的用例。

在一个web应用中你能得到的基本特性如下：

1. 一个使用内存存储的AuthenticationManager bean和唯一的user（查看SecurityProperties.User获取user的属性）。
2. 忽略（不保护）常见的静态资源路径（`/css/**, /js/**, /images/**`和 `**/favicon.ico`）。
3. 对其他的路径实施HTTP Basic安全保护。
4. 安全相关的事件会发布到Spring的ApplicationEventPublisher（成功和失败的认证，拒绝访问）。
5. Spring Security提供的常见底层特性（HSTS, XSS, CSRF, 缓存）默认都被开启。

上述所有特性都能打开和关闭，或使用外部的配置进行修改（security.*）。为了覆盖访问规则（access rules）而不改变其他自动配置的特性，你可以添加一个使用@Order(SecurityProperties.ACCESS_OVERRIDE_ORDER)注解的WebSecurityConfigurerAdapter类型的@Bean。

如果Actuator也在使用，你会发现：

1. 即使应用路径不受保护，被管理的路径也会受到保护。
2. 安全相关的事件被转换为AuditEvents（审计事件），并发布给AuditService。
3. 默认的用户有ADMIN和USER的角色。

使用外部属性能够修改Actuator（执行器）的安全特性（management.security.*）。为了覆盖应用程序的访问规则，你可以添加一个WebSecurityConfigurerAdapter类型的@Bean。同时，如果不想覆盖执行器的访问规则，你可以使用@Order(SecurityProperties.ACCESS_OVERRIDE_ORDER)注解该bean，否则使用@Order(ManagementServerProperties.ACCESS_OVERRIDE_ORDER)注解该bean。

### 使用SQL数据库
Spring框架为使用SQL数据库提供了广泛的支持。从使用JdbcTemplate直接访问JDBC到完全的对象关系映射技术，比如Hibernate。Spring Data提供一个额外的功能，直接从接口创建Repository实现，并使用约定从你的方法名生成查询。

* 配置DataSource

Java的javax.sql.DataSource接口提供了一个标准的使用数据库连接的方法。传统做法是，一个DataSource使用一个URL连同相应的证书去初始化一个数据库连接。

**1.对内嵌数据库的支持**

开发应用时使用内存数据库是很实用的。显而易见地，内存数据库不需要提供持久化存储。你不需要在应用启动时填充数据库，也不需要在应用结束时丢弃数据。

Spring Boot可以自动配置的内嵌数据库包括[H2](http://www.h2database.com/), [HSQL](http://hsqldb.org/)和[Derby](http://db.apache.org/derby/)。你不需要提供任何连接URLs，只需要简单的添加你想使用的内嵌数据库依赖。

示例：典型的POM依赖如下：
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
    <groupId>org.hsqldb</groupId>
    <artifactId>hsqldb</artifactId>
    <scope>runtime</scope>
</dependency>
```
**注**：对于自动配置的内嵌数据库，你需要依赖spring-jdbc。在示例中，它通过`spring-boot-starter-data-jpa`被传递地拉过来了。

**2.连接到一个生产环境数据库**

在生产环境中，数据库连接可以使用DataSource池进行自动配置。下面是选取一个特定实现的算法：

- 由于Tomcat数据源连接池的性能和并发，在tomcat可用时，我们总是优先使用它。
- 如果HikariCP可用，我们将使用它。
- 如果Commons DBCP可用，我们将使用它，但在生产环境不推荐使用它。
- 最后，如果Commons DBCP2可用，我们将使用它。

如果你使用spring-boot-starter-jdbc或spring-boot-starter-data-jpa 'starter POMs'，你将会自动获取对tomcat-jdbc的依赖。

**注**：其他的连接池可以手动配置。如果你定义自己的DataSource bean，自动配置不会发生。

DataSource配置通过外部配置文件的spring.datasource.*属性控制。示例中，你可能会在application.properties中声明下面的片段：
```java
spring.datasource.url=jdbc:mysql://localhost/test
spring.datasource.username=dbuser
spring.datasource.password=dbpass
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
```
其他可选的配置可以查看[DataSourceProperties](http://github.com/spring-projects/spring-boot/tree/master/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/jdbc/DataSourceProperties.java)。同时注意你可以通过spring.datasource.*配置任何DataSource实现相关的特定属性：具体参考你使用的连接池实现的文档。

**注**：既然Spring Boot能够从大多数数据库的url上推断出driver-class-name，那么你就不需要再指定它了。对于一个将要创建的DataSource连接池，我们需要能够验证Driver是否可用，所以我们会在做任何事情之前检查它。比如，如果你设置spring.datasource.driverClassName=com.mysql.jdbc.Driver，然后这个类就会被加载。

**3.连接到一个JNDI数据库**

如果正在将Spring Boot应用部署到一个应用服务器，你可能想要用应用服务器内建的特性来配置和管理你的DataSource，并使用JNDI访问它。

spring.datasource.jndi-name属性可以用来替代spring.datasource.url，spring.datasource.username和spring.datasource.password去从一个特定的JNDI路径访问DataSource。比如，下面application.properties中的片段展示了如何获取JBoss定义的DataSource：
```java
spring.datasource.jndi-name=java:jboss/datasources/customers
```
* 使用JdbcTemplate

Spring的JdbcTemplate和NamedParameterJdbcTemplate类是被自动配置的，你可以在自己的beans中通过@Autowire直接注入它们。
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.stereotype.Component;

@Component
public class MyBean {

    private final JdbcTemplate jdbcTemplate;

    @Autowired
    public MyBean(JdbcTemplate jdbcTemplate) {
        this.jdbcTemplate = jdbcTemplate;
    }
    // ...
}
```
* JPA和Spring Data

Java持久化API是一个允许你将对象映射为关系数据库的标准技术。spring-boot-starter-data-jpa POM提供了一种快速上手的方式。它提供下列关键的依赖：

- Hibernate - 一个非常流行的JPA实现。
- Spring Data JPA - 让实现基于JPA的repositories更容易。
- Spring ORMs - Spring框架的核心ORM支持。

**注**：我们不想在这涉及太多关于JPA或Spring Data的细节。你可以参考来自[spring.io](http://spring.io/)的指南[使用JPA获取数据](http://spring.io/guides/gs/accessing-data-jpa/)，并阅读[Spring Data JPA](http://projects.spring.io/spring-data-jpa/)和[Hibernate](http://hibernate.org/orm/documentation/)的参考文档。

**1.实体类**

传统上，JPA实体类被定义到一个persistence.xml文件中。在Spring Boot中，这个文件不是必需的，并被'实体扫描'替代。默认情况下，在你主（main）配置类（被@EnableAutoConfiguration或@SpringBootApplication注解的类）下的所有包都将被查找。

任何被@Entity，@Embeddable或@MappedSuperclass注解的类都将被考虑。一个普通的实体类看起来像下面这样：
```java
package com.example.myapp.domain;

import java.io.Serializable;
import javax.persistence.*;

@Entity
public class City implements Serializable {

    @Id
    @GeneratedValue
    private Long id;

    @Column(nullable = false)
    private String name;

    @Column(nullable = false)
    private String state;

    // ... additional members, often include @OneToMany mappings

    protected City() {
        // no-args constructor required by JPA spec
        // this one is protected since it shouldn't be used directly
    }

    public City(String name, String state) {
        this.name = name;
        this.country = country;
    }

    public String getName() {
        return this.name;
    }

    public String getState() {
        return this.state;
    }
    // ... etc
}
```
**注**：你可以使用@EntityScan注解自定义实体扫描路径。具体参考[Section 67.4, “Separate @Entity definitions from Spring configuration”](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#howto-separate-entity-definitions-from-spring-configuration)。

**2.Spring Data JPA仓库**

Spring Data JPA仓库（repositories）是用来定义访问数据的接口。根据你的方法名，JPA查询会被自动创建。比如，一个CityRepository接口可能声明一个findAllByState(String state)方法，用来查找给定状态的所有城市。

对于比较复杂的查询，你可以使用Spring Data的[Query](http://docs.spring.io/spring-data/jpa/docs/current/api/org/springframework/data/jpa/repository/Query.html)来注解你的方法。

Spring Data仓库通常继承自[Repository](http://docs.spring.io/spring-data/commons/docs/current/api/org/springframework/data/repository/Repository.html)或[CrudRepository](http://docs.spring.io/spring-data/commons/docs/current/api/org/springframework/data/repository/CrudRepository.html)接口。如果你使用自动配置，包括在你的主配置类（被@EnableAutoConfiguration或@SpringBootApplication注解的类）的包下的仓库将会被搜索。
 
下面是一个传统的Spring Data仓库：
```java
package com.example.myapp.domain;

import org.springframework.data.domain.*;
import org.springframework.data.repository.*;

public interface CityRepository extends Repository<City, Long> {

    Page<City> findAll(Pageable pageable);

    City findByNameAndCountryAllIgnoringCase(String name, String country);
}
```
**注**：我们仅仅触及了Spring Data JPA的表面。具体查看它的[参考指南](http://projects.spring.io/spring-data-jpa/)。

**3.创建和删除JPA数据库**

默认情况下，只有在你使用内嵌数据库（H2, HSQL或Derby）时，JPA数据库才会被自动创建。你可以使用spring.jpa.*属性显示的设置JPA。比如，为了创建和删除表你可以将下面的配置添加到application.properties中：
```java
spring.jpa.hibernate.ddl-auto=create-drop
```
**注**：Hibernate自己内部对创建，删除表支持（如果你恰好记得这回事更好）的属性是hibernate.hbm2ddl.auto。使用spring.jpa.properties.*（前缀在被添加到实体管理器之前会被剥离掉），你可以设置Hibernate本身的属性，比如hibernate.hbm2ddl.auto。示例：`spring.jpa.properties.hibernate.globally_quoted_identifiers=true`将传递hibernate.globally_quoted_identifiers到Hibernate实体管理器。

默认情况下，DDL执行（或验证）被延迟到ApplicationContext启动。这也有一个spring.jpa.generate-ddl标识，如果Hibernate自动配置被激活，那该标识就不会被使用，因为ddl-auto设置粒度更细。

### 使用NoSQL技术
Spring Data提供其他项目，用来帮你使用各种各样的NoSQL技术，包括[MongoDB](http://projects.spring.io/spring-data-mongodb/), [Neo4J](http://projects.spring.io/spring-data-neo4j/), [Elasticsearch](https://github.com/spring-projects/spring-data-elasticsearch/), [Solr](http://projects.spring.io/spring-data-solr/), [Redis](http://projects.spring.io/spring-data-redis/), [Gemfire](http://projects.spring.io/spring-data-gemfire/), [Couchbase](http://projects.spring.io/spring-data-couchbase/)和[Cassandra](http://projects.spring.io/spring-data-cassandra/)。Spring Boot为Redis, MongoDB, Elasticsearch, Solr和Gemfire提供自动配置。你可以充分利用其他项目，但你需要自己配置它们。具体查看[projects.spring.io/spring-data.](http://projects.spring.io/spring-data/)中合适的参考文档。

* Redis

[Redis](http://redis.io/)是一个缓存，消息中间件及具有丰富特性的键值存储系统。Spring Boot为[Jedis](https://github.com/xetorthio/jedis/)客户端库和由[Spring Data Redis](https://github.com/spring-projects/spring-data-redis)提供的基于Jedis客户端的抽象提供自动配置。`spring-boot-starter-redis`'Starter POM'为收集依赖提供一种便利的方式。

1. 连接Redis

你可以注入一个自动配置的RedisConnectionFactory，StringRedisTemplate或普通的跟其他Spring Bean相同的RedisTemplate实例。默认情况下，这个实例将尝试使用localhost:6379连接Redis服务器。
```java
@Component
public class MyBean {

    private StringRedisTemplate template;

    @Autowired
    public MyBean(StringRedisTemplate template) {
        this.template = template;
    }
    // ...
}
```
如果你添加一个你自己的任何自动配置类型的@Bean，它将替换默认的（除了RedisTemplate的情况，它是根据bean的名称'redisTemplate'而不是它的类型进行排除的）。如果在classpath路径下存在commons-pool2，默认你会获得一个连接池工厂。

* MongoDB

[MongoDB](http://www.mongodb.com/)是一个开源的NoSQL文档数据库，它使用一个JSON格式的模式（schema）替换了传统的基于表的关系数据。Spring Boot为使用MongoDB提供了很多便利，包括`spring-boot-starter-data-mongodb`'Starter POM'。

**1. 连接MongoDB数据库**

你可以注入一个自动配置的`org.springframework.data.mongodb.MongoDbFactory`来访问Mongo数据库。默认情况下，该实例将尝试使用URL：`mongodb://localhost/test`连接一个MongoDB服务器。
```java
import org.springframework.data.mongodb.MongoDbFactory;
import com.mongodb.DB;

@Component
public class MyBean {

    private final MongoDbFactory mongo;

    @Autowired
    public MyBean(MongoDbFactory mongo) {
        this.mongo = mongo;
    }

    // ...
    public void example() {
        DB db = mongo.getDb();
        // ...
    }
}
```
你可以通过设置`spring.data.mongodb.uri`来改变该url，或指定一个host/port。比如，你可能会在你的application.properties中设置如下的属性：
```java
spring.data.mongodb.host=mongoserver
spring.data.mongodb.port=27017
```
**注**：如果没有指定`spring.data.mongodb.port`，那将使用默认的端口27017。你可以简单的从上面的示例中删除这一行。如果不使用Spring Data Mongo，你可以注入com.mongodb.Mongo beans而不是使用MongoDbFactory。

如果想全面控制MongoDB连接的建立，你也可以声明自己的MongoDbFactory或Mongo，@Beans。

**2. MongoDBTemplate**

Spring Data Mongo提供了一个[MongoTemplate](http://docs.spring.io/spring-data/mongodb/docs/current/api/org/springframework/data/mongodb/core/MongoTemplate.html)类，它的设计和Spring的JdbcTemplate很相似。正如JdbcTemplate一样，Spring Boot会为你自动配置一个bean，你只需简单的注入它即可：
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.mongodb.core.MongoTemplate;
import org.springframework.stereotype.Component;

@Component
public class MyBean {

    private final MongoTemplate mongoTemplate;

    @Autowired
    public MyBean(MongoTemplate mongoTemplate) {
        this.mongoTemplate = mongoTemplate;
    }
    // ...
}
```
具体参考MongoOperations Javadoc。

**3. Spring Data MongoDB仓库**

Spring Data的仓库包括对MongoDB的支持。正如上面讨论的JPA仓库，基本的原则是查询会自动基于你的方法名创建。

实际上，不管是Spring Data JPA还是Spring Data MongoDB都共享相同的基础设施。所以你可以使用上面的JPA示例，并假设那个City现在是一个Mongo数据类而不是JPA　@Entity，它将以同样的方式工作。
```java
package com.example.myapp.domain;

import org.springframework.data.domain.*;
import org.springframework.data.repository.*;

public interface CityRepository extends Repository<City, Long> {

    Page<City> findAll(Pageable pageable);

    City findByNameAndCountryAllIgnoringCase(String name, String country);

}
```
* Gemfire

[Spring Data Gemfire](https://github.com/spring-projects/spring-data-gemfire)为使用[Pivotal Gemfire](http://www.pivotal.io/big-data/pivotal-gemfire#details)数据管理平台提供了方便的，Spring友好的工具。Spring Boot提供了一个用于聚集依赖的`spring-boot-starter-data-gemfire`'Starter POM'。目前不支持Gemfire的自动配置，但你可以使用一个[单一的注解](https://github.com/spring-projects/spring-data-gemfire/blob/master/src/main/java/org/springframework/data/gemfire/repository/config/EnableGemfireRepositories.java)使Spring Data仓库支持它。

* Solr

[Apache Solr](http://lucene.apache.org/solr/)是一个搜索引擎。Spring Boot为solr客户端库及[Spring Data Solr](https://github.com/spring-projects/spring-data-solr)提供的基于solr客户端库的抽象提供了基本的配置。Spring Boot提供了一个用于聚集依赖的`spring-boot-starter-data-solr`'Starter POM'。
 
**1. 连接Solr**

你可以像其他Spring beans一样注入一个自动配置的SolrServer实例。默认情况下，该实例将尝试使用`localhost:8983/solr`连接一个服务器。
```java
@Component
public class MyBean {

    private SolrServer solr;

    @Autowired
    public MyBean(SolrServer solr) {
        this.solr = solr;
    }
    // ...
}
```
如果你添加一个自己的SolrServer类型的@Bean，它将会替换默认的。

**2. Spring Data Solr仓库**

Spring Data的仓库包括了对Apache Solr的支持。正如上面讨论的JPA仓库，基本的原则是查询会自动基于你的方法名创建。

实际上，不管是Spring Data JPA还是Spring Data Solr都共享相同的基础设施。所以你可以使用上面的JPA示例，并假设那个City现在是一个@SolrDocument类而不是JPA　@Entity，它将以同样的方式工作。

**注**：具体参考[Spring Data Solr文档](http://projects.spring.io/spring-data-solr/)。

* Elasticsearch

[Elastic Search](http://www.elasticsearch.org/)是一个开源的，分布式，实时搜索和分析引擎。Spring Boot为Elasticsearch及[Spring Data Elasticsearch](https://github.com/spring-projects/spring-data-elasticsearch)提供的基于它的抽象提供了基本的配置。Spring Boot提供了一个用于聚集依赖的`spring-boot-starter-data-elasticsearch`'Starter POM'。
  
**1. 连接Elasticsearch**

你可以像其他Spring beans那样注入一个自动配置的ElasticsearchTemplate或Elasticsearch客户端实例。默认情况下，该实例将尝试连接到一个本地内存服务器（在Elasticsearch项目中的一个NodeClient），但你可以通过设置`spring.data.elasticsearch.clusterNodes`为一个以逗号分割的host:port列表来将其切换到一个远程服务器（比如，TransportClient）。
```java
@Component
public class MyBean {

    private ElasticsearchTemplate template;

    @Autowired
    public MyBean(ElasticsearchTemplate template) {
        this.template = template;
    }
    // ...
}
```
如果你添加一个你自己的ElasticsearchTemplate类型的@Bean，它将替换默认的。

**2. Spring Data Elasticseach仓库**

Spring Data的仓库包括了对Elasticsearch的支持。正如上面讨论的JPA仓库，基本的原则是查询会自动基于你的方法名创建。

实际上，不管是Spring Data JPA还是Spring Data　Elasticsearch都共享相同的基础设施。所以你可以使用上面的JPA示例，并假设那个City现在是一个Elasticsearch @Document类而不是JPA　@Entity，它将以同样的方式工作。

**注**：具体参考[Spring Data Elasticsearch文档](http://docs.spring.io/spring-data/elasticsearch/docs/)。
  
### 消息

Spring Framework框架为集成消息系统提供了扩展（extensive）支持：从使用JmsTemplate简化JMS　API，到实现一个完整异步消息接收的底层设施。Spring AMQP提供一个相似的用于'高级消息队列协议'的特征集，并且Spring Boot也为RabbitTemplate和RabbitMQ提供了自动配置选项。Spring Websocket提供原生的STOMP消息支持，并且Spring Boot通过starters和一些自动配置也提供了对它的支持。

* JMS

javax.jms.ConnectionFactory接口提供了一个标准的用于创建一个javax.jms.Connection的方法，javax.jms.Connection用于和JMS代理（broker）交互。尽管为了使用JMS，Spring需要一个ConnectionFactory，但通常你不需要直接使用它，而是依赖于上层消息抽象（具体参考Spring框架的[相关章节](http://docs.spring.io/spring/docs/4.1.4.RELEASE/spring-framework-reference/htmlsingle/#jms)）。Spring Boot也会自动配置发送和接收消息需要的设施（infrastructure）。
 
 1. HornetQ支持

如果在classpath下发现HornetQ，Spring Boot会自动配置ConnectionFactory。如果需要代理，将会开启一个内嵌的，已经自动配置好的代理（除非显式设置mode属性）。支持的modes有：embedded（显式声明使用一个内嵌的代理，如果该代理在classpath下不可用将导致一个错误），native（使用netty传输协议连接代理）。当后者被配置，Spring Boot配置一个连接到一个代理的ConnectionFactory，该代理运行在使用默认配置的本地机器上。

**注**：如果使用spring-boot-starter-hornetq，连接到一个已存在的HornetQ实例所需的依赖都会被提供，同时还有用于集成JMS的Spring基础设施。将org.hornetq:hornetq-jms-server添加到你的应用中，你就可以使用embedded模式。

HornetQ配置被spring.hornetq.*中的外部配置属性所控制。例如，你可能在application.properties声明以下片段：
```java
spring.hornetq.mode=native
spring.hornetq.host=192.168.1.210
spring.hornetq.port=9876
```
当内嵌代理时，你可以选择是否启用持久化，并且列表中的目标都应该是可用的。这些可以通过一个以逗号分割的列表来指定一些默认的配置项，或定义org.hornetq.jms.server.config.JMSQueueConfiguration或org.hornetq.jms.server.config.TopicConfiguration类型的bean(s)来配置更高级的队列和主题。具体参考[HornetQProperties](http://github.com/spring-projects/spring-boot/tree/master/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/jms/hornetq/HornetQProperties.java)。

没有涉及JNDI查找，目标是通过名字解析的，名字即可以使用HornetQ配置中的name属性，也可以是配置中提供的names。

  2. ActiveQ支持

如果发现ActiveMQ在classpath下可用，Spring Boot会配置一个ConnectionFactory。如果需要代理，将会开启一个内嵌的，已经自动配置好的代理（只要配置中没有指定代理URL）。

ActiveMQ配置是通过spring.activemq.*中的外部配置来控制的。例如，你可能在application.properties中声明下面的片段：
```java
spring.activemq.broker-url=tcp://192.168.1.210:9876
spring.activemq.user=admin
spring.activemq.password=secret
```
具体参考[ActiveMQProperties](http://github.com/spring-projects/spring-boot/tree/master/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/jms/activemq/ActiveMQProperties.java)。

默认情况下，如果目标还不存在，ActiveMQ将创建一个，所以目标是通过它们提供的名称解析出来的。

  3. 使用JNDI ConnectionFactory

如果你在一个应用服务器中运行你的应用，Spring Boot将尝试使用JNDI定位一个JMS ConnectionFactory。默认情况会检查java:/JmsXA和java:/
XAConnectionFactory。如果需要的话，你可以使用spring.jms.jndi-name属性来指定一个替代位置。
```java
spring.jms.jndi-name=java:/MyConnectionFactory
```
  4. 发送消息

Spring的JmsTemplate会被自动配置，你可以将它直接注入到你自己的beans中：
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jms.core.JmsTemplate;
import org.springframework.stereotype.Component;
@Component
public class MyBean {
private final JmsTemplate jmsTemplate;
@Autowired
public MyBean(JmsTemplate jmsTemplate) {
this.jmsTemplate = jmsTemplate;
}
// ...
}
```

**注**：[JmsMessagingTemplate](http://docs.spring.io/spring/docs/4.1.4.RELEASE/javadoc-api/org/springframework/jms/core/JmsMessagingTemplate.html)(Spring4.1新增的)也可以使用相同的方式注入。

  5. 接收消息

当JMS基础设施能够使用时，任何bean都能够被@JmsListener注解，以创建一个监听者端点。如果没有定义JmsListenerContainerFactory，一个默认的将会被自动配置。下面的组件在someQueue目标上创建一个监听者端点。
```java
@Component
public class MyBean {
@JmsListener(destination = "someQueue")
public void processMessage(String content) {
// ...
}
}
```
具体查看[@EnableJms javadoc](http://docs.spring.io/spring/docs/4.1.4.RELEASE/javadoc-api/org/springframework/jms/annotation/EnableJms.html)。

### 发送邮件

Spring框架使用JavaMailSender接口为发送邮件提供了一个简单的抽象，并且Spring Boot也为它提供了自动配置和一个starter模块。
具体查看[JavaMailSender参考文档](http://docs.spring.io/spring/docs/4.1.4.RELEASE/spring-framework-reference/htmlsingle/#mail)。

如果spring.mail.host和相关的库（通过spring-boot-starter-mail定义）都存在，一个默认的JavaMailSender将被创建。该sender可以通过spring.mail命名空间下的配置项进一步自定义，具体参考[MailProperties](http://github.com/spring-projects/spring-boot/tree/master/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/mail/MailProperties.java)。

### 使用JTA处理分布式事务

Spring Boot使用一个[Atomkos](http://www.atomikos.com/)或[Bitronix](http://docs.codehaus.org/display/BTM/Home)的内嵌事务管理器来支持跨多个XA资源的分布式JTA事务。当部署到一个恰当的J2EE应用服务器时也会支持JTA事务。

当发现一个JTA环境时，Spring Boot将使用Spring的JtaTransactionManager来管理事务。自动配置的JMS，DataSource和JPA　beans将被升级以支持XA事务。你可以使用标准的Spring idioms，比如@Transactional，来参与到一个分布式事务中。如果你处于JTA环境里，但仍旧想使用本地事务，你可以将spring.jta.enabled属性设置为false来禁用JTA自动配置功能。

* 使用一个Atomikos事务管理器

Atomikos是一个非常流行的开源事务管理器，它可以嵌入到你的Spring Boot应用中。你可以使用`spring-boot-starter-jta-atomikos`Starter POM去获取正确的Atomikos库。Spring Boot会自动配置Atomikos，并将合适的depends-on应用到你的Spring Beans上，确保它们以正确的顺序启动和关闭。

默认情况下，Atomikos事务日志将被记录在应用home目录（你的应用jar文件放置的目录）下的transaction-logs文件夹中。你可以在application.properties文件中通过设置spring.jta.log-dir属性来自定义该目录。以spring.jta.开头的属性能用来自定义Atomikos的UserTransactionServiceIml实现。具体参考[AtomikosProperties javadoc](http://docs.spring.io/spring-boot/docs/1.2.2.BUILD-SNAPSHOT/api/org/springframework/boot/jta/atomikos/AtomikosProperties.html)。

**注**：为了确保多个事务管理器能够安全地和相应的资源管理器配合，每个Atomikos实例必须设置一个唯一的ID。默认情况下，该ID是Atomikos实例运行的机器上的IP地址。为了确保生产环境中该ID的唯一性，你需要为应用的每个实例设置不同的spring.jta.transaction-manager-id属性值。

* 使用一个Bitronix事务管理器

Bitronix是另一个流行的开源JTA事务管理器实现。你可以使用`spring-boot-starter-jta-bitronix`starter POM为项目添加合适的Birtronix依赖。和Atomikos类似，Spring Boot将自动配置Bitronix，并对beans进行后处理（post-process）以确保它们以正确的顺序启动和关闭。

默认情况下，Bitronix事务日志将被记录到应用home目录下的transaction-logs文件夹中。通过设置spring.jta.log-dir属性，你可以自定义该目录。以spring.jta.开头的属性将被绑定到bitronix.tm.Configuration　bean，你可以通过这完成进一步的自定义。具体参考[Bitronix文档](http://btm.codehaus.org/api/2.0.1/bitronix/tm/Configuration.html)。

**注**：为了确保多个事务管理器能够安全地和相应的资源管理器配合，每个Bitronix实例必须设置一个唯一的ID。默认情况下，该ID是Bitronix实例运行的机器上的IP地址。为了确保生产环境中该ID的唯一性，你需要为应用的每个实例设置不同的spring.jta.transaction-manager-id属性值。

* 使用一个J2EE管理的事务管理器

如果你将Spring Boot应用打包为一个war或ear文件，并将它部署到一个J2EE的应用服务器中，那你就能使用应用服务器内建的事务管理器。Spring Boot将尝试通过查找常见的JNDI路径（java:comp/UserTransaction, java:comp/TransactionManager等）来自动配置一个事务管理器。如果使用应用服务器提供的事务服务，你通常需要确保所有的资源都被应用服务器管理，并通过JNDI暴露出去。Spring Boot通过查找JNDI路径java:/JmsXA或java:/XAConnectionFactory获取一个ConnectionFactory来自动配置JMS，并且你可以使用spring.datasource.jndi-name属性配置你的DataSource。

* 混合XA和non-XA的JMS连接

当使用JTA时，主要的JMS ConnectionFactory bean将是XA　aware，并参与到分布式事务中。有些情况下，你可能需要使用non-XA的ConnectionFactory去处理一些JMS消息。例如，你的JMS处理逻辑可能比XA超时时间长。

如果想使用一个non-XA的ConnectionFactory，你可以注入nonXaJmsConnectionFactory　bean而不是@Primary jmsConnectionFactory　bean。为了保持一致，jmsConnectionFactory　bean将以别名xaJmsConnectionFactor来被使用。

示例如下：
```java
// Inject the primary (XA aware) ConnectionFactory
@Autowired
private ConnectionFactory defaultConnectionFactory;
// Inject the XA aware ConnectionFactory (uses the alias and injects the same as above)
@Autowired
@Qualifier("xaJmsConnectionFactory")
private ConnectionFactory xaConnectionFactory;
// Inject the non-XA aware ConnectionFactory
@Autowired
@Qualifier("nonXaJmsConnectionFactory")
private ConnectionFactory nonXaConnectionFactory;
```
* 支持可替代的内嵌事务管理器

[XAConnectionFactoryWrapper](http://github.com/spring-projects/spring-boot/tree/master/spring-boot/src/main/java/org/springframework/boot/jta/XAConnectionFactoryWrapper.java)和[XADataSourceWrapper](http://github.com/spring-projects/spring-boot/tree/master/spring-boot/src/main/java/org/springframework/boot/jta/XADataSourceWrapper.java)接口用于支持可替换的内嵌事务管理器。该接口用于包装XAConnectionFactory和XADataSource　beans，并将它们暴露为普通的ConnectionFactory和DataSource　beans，这样在分布式事务中可以透明使用。

### Spring集成

Spring集成提供基于消息和其他协议的，比如HTTP，TCP等的抽象。如果Spring集成在classpath下可用，它将会通过@EnableIntegration注解被初始化。如果classpath下'spring-integration-jmx'可用，则消息处理统计分析将被通过JMX发布出去。具体参考[IntegrationAutoConfiguration类](http://github.com/spring-projects/spring-boot/tree/master/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/integration/IntegrationAutoConfiguration.java)。

### 基于JMX的监控和管理

Java管理扩展（JMX）提供了一个标准的用于监控和管理应用的机制。默认情况下，Spring Boot将创建一个id为‘mbeanServer’的MBeanServer，并导出任何被Spring JMX注解（@ManagedResource,@ManagedAttribute,@ManagedOperation）的beans。具体参考[JmxAutoConfiguration类](http://github.com/spring-projects/spring-boot/tree/master/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/jmx/JmxAutoConfiguration.java)。

### 测试

Spring Boot提供很多有用的测试应用的工具。spring-boot-starter-test POM提供Spring Test，JUnit，Hamcrest和Mockito的依赖。在spring-boot核心模块org.springframework.boot.test包下也有很多有用的测试工具。

* 测试作用域依赖

如果使用spring-boot-starter-test ‘Starter POM’（在test作用域内），你将发现下列被提供的库：
- Spring Test - 对Spring应用的集成测试支持
- JUnit - 事实上的（de-facto）标准，用于Java应用的单元测试。
- Hamcrest - 一个匹配对象的库（也称为约束或前置条件），它允许assertThat等JUnit类型的断言。
- Mockito - 一个Java模拟框架。

这也有一些我们写测试用例时经常用到的库。如果它们不能满足你的要求，你可以随意添加其他的测试用的依赖库。

* 测试Spring应用

依赖注入最大的优点就是它能够让你的代码更容易进行单元测试。你只需简单的通过new操作符实例化对象，而不需要涉及Spring。你也可以使用模拟对象替换真正的依赖。

你常常需要在进行单元测试后，开始集成测试（在这个过程中只需要涉及到Spring的ApplicationContext）。在执行集成测试时，不需要部署应用或连接到其他基础设施是非常有用的。

Spring框架包含一个dedicated测试模块，用于这样的集成测试。你可以直接声明对org.springframework:spring-test的依赖，或使用spring-boot-starter-test ‘Starter POM’以透明的方式拉取它。

如果你以前没有使用过spring-test模块，可以查看Spring框架参考文档中的[相关章节](http://docs.spring.io/spring/docs/4.1.4.RELEASE/spring-framework-reference/htmlsingle/#testing)。

* 测试Spring Boot应用

一个Spring Boot应用只是一个Spring ApplicationContext，所以在测试它时除了正常情况下处理一个vanilla Spring　context外不需要做其他特别事情。唯一需要注意的是，如果你使用SpringApplication创建上下文，外部配置，日志和Spring Boot的其他特性只会在默认的上下文中起作用。

Spring Boot提供一个@SpringApplicationConfiguration注解用来替换标准的spring-test　@ContextConfiguration注解。如果使用@SpringApplicationConfiguration来设置你的测试中使用的ApplicationContext，它最终将通过SpringApplication创建，并且你将获取到Spring Boot的其他特性。

示例如下：
```java
@RunWith(SpringJUnit4ClassRunner.class)
@SpringApplicationConfiguration(classes = SampleDataJpaApplication.class)
public class CityRepositoryIntegrationTests {
@Autowired
CityRepository repository;
// ...
}	
```
**提示**：上下文加载器会通过查找@WebIntegrationTest或@WebAppConfiguration注解来猜测你想测试的是否是web应用（例如，是否使用MockMVC，MockMVC和@WebAppConfiguration是spring-test的一部分）。

如果想让一个web应用启动，并监听它的正常的端口，你可以使用HTTP来测试它（比如，使用RestTemplate），并使用@WebIntegrationTest注解你的测试类（或它的一个父类）。这很有用，因为它意味着你可以对你的应用进行全栈测试，但在一次HTTP交互后，你需要在你的测试类中注入相应的组件并使用它们断言应用的内部状态。

示例：
```java
@RunWith(SpringJUnit4ClassRunner.class)
@SpringApplicationConfiguration(classes = SampleDataJpaApplication.class)
@WebIntegrationTest
public class CityRepositoryIntegrationTests {
@Autowired
CityRepository repository;
RestTemplate restTemplate = new TestRestTemplate();
// ... interact with the running server
}
```
**注**：Spring测试框架在每次测试时会缓存应用上下文。因此，只要你的测试共享相同的配置，不管你实际运行多少测试，开启和停止服务器只会发生一次。

你可以为@WebIntegrationTest添加环境变量属性来改变应用服务器端口号，比如@WebIntegrationTest("server.port:9000")。此外，你可以将server.port和management.port属性设置为０来让你的集成测试使用随机的端口号，例如：
```java
@RunWith(SpringJUnit4ClassRunner.class)
@SpringApplicationConfiguration(classes = MyApplication.class)
@WebIntegrationTest({"server.port=0", "management.port=0"})
public class SomeIntegrationTests {
// ...
}
```
可以查看[Section 64.4, “Discover the HTTP port at runtime”]()，它描述了如何在测试期间发现分配的实际端口。

1. 使用Spock测试Spring Boot应用

如果期望使用Spock测试一个Spring Boot应用，你应该将Spock的spock-spring模块依赖添加到应用的构建中。spock-spring将Spring的测试框架集成到了Spock里。

注意你不能使用上述提到的@SpringApplicationConfiguration注解，因为[Spock找不到@ContextConfiguration元注解](https://code.google.com/p/spock/issues/detail?id=349)。为了绕过该限制，你应该直接使用@ContextConfiguration注解，并使用Spring Boot特定的上下文加载器来配置它。
```java
@ContextConfiguration(loader = SpringApplicationContextLoader.class)
class ExampleSpec extends Specification {
// ...
}
```
**注**：上面描述的注解在Spock中可以使用，比如，你可以使用@WebIntegrationTest注解你的Specification以满足测试需要。

* 测试工具

打包进spring-boot的一些有用的测试工具类。

1. ConfigFileApplicationContextInitializer

ConfigFileApplicationContextInitializer是一个ApplicationContextInitializer，可以用来测试加载Spring Boot的application.properties文件。当不需要使用@SpringApplicationConfiguration提供的全部特性时，你可以使用它。

```java
@ContextConfiguration(classes = Config.class,initializers = ConfigFileApplicationContextInitializer.class)
```　　
2. EnvironmentTestUtils

EnvironmentTestUtils允许你快速添加属性到一个ConfigurableEnvironment或ConfigurableApplicationContext。只需简单的使用key=value字符串调用它：
```java
EnvironmentTestUtils.addEnvironment(env, "org=Spring", "name=Boot");
```
3. OutputCapture

OutputCapture是一个JUnit Rule，用于捕获System.out和System.err输出。只需简单的将捕获声明为一个@Rule，并使用toString()断言：
```java
import org.junit.Rule;
import org.junit.Test;
import org.springframework.boot.test.OutputCapture;
import static org.hamcrest.Matchers.*;
import static org.junit.Assert.*;

public class MyTest {
@Rule
public OutputCapture capture = new OutputCapture();
@Test
public void testName() throws Exception {
System.out.println("Hello World!");
assertThat(capture.toString(), containsString("World"));
}
}
```

4. TestRestTemplate

TestRestTemplate是一个方便进行集成测试的Spring RestTemplate子类。你会获取到一个普通的模板或一个发送基本HTTP认证（使用用户名和密码）的模板。在任何情况下，这些模板都表现出对测试友好：不允许重定向（这样你可以对响应地址进行断言），忽略cookies（这样模板就是无状态的），对于服务端错误不会抛出异常。推荐使用Apache HTTP Client(4.3.2或更好的版本)，但不强制这样做。如果在classpath下存在Apache HTTP Client，TestRestTemplate将以正确配置的client进行响应。
```java
public class MyTest {
RestTemplate template = new TestRestTemplate();
@Test
public void testRequest() throws Exception {
HttpHeaders headers = template.getForEntity("http://myhost.com", String.class).getHeaders();
assertThat(headers.getLocation().toString(), containsString("myotherhost"));
}
}
```

### 开发自动配置和使用条件

如果你在一个开发者共享库的公司工作，或你在从事一个开源或商业型的库，你可能想要开发自己的auto-configuration。Auto-configuration类能够在外部的jars中绑定，并仍能被Spring Boot发现。

* 理解auto-configured beans

从底层来讲，auto-configured是使用标准的@Configuration实现的类，另外的@Conditional注解用来约束在什么情况下使用auto-configuration。通常auto-configuration类使用@ConditionalOnClass和@ConditionalOnMissingBean注解。这是为了确保只有在相关的类被发现，和你没有声明自己的@Configuration时才应用auto-configuration。

你可以浏览spring-boot-autoconfigure的源码，查看我们提供的@Configuration类（查看META-INF/spring.factories文件）。

* 定位auto-configuration候选者

Spring Boot会检查你发布的jar中是否存在META-INF/spring.factories文件。该文件应该列出以EnableAutoConfiguration为key的配置类：
```java
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
com.mycorp.libx.autoconfigure.LibXAutoConfiguration,\
com.mycorp.libx.autoconfigure.LibXWebAutoConfiguration
```
如果配置需要应用特定的顺序，你可以使用[@AutoConfigureAfter](http://github.com/spring-projects/spring-boot/tree/master/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/AutoConfigureAfter.java)或[@AutoConfigureBefore](http://github.com/spring-projects/spring-boot/tree/master/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/AutoConfigureBefore.java)注解。例如，你想提供web-specific配置，你的类就需要应用在WebMvcAutoConfiguration后面。

* Condition注解

你几乎总是需要在你的auto-configuration类里添加一个或更多的@Condition注解。@ConditionalOnMissingBean注解是一个常见的示例，它经常用于允许开发者覆盖auto-configuration，如果他们不喜欢你提供的默认行为。

Spring Boot包含很多@Conditional注解，你可以在自己的代码中通过注解@Configuration类或单独的@Bean方法来重用它们。

1. Class条件

@ConditionalOnClass和@ConditionalOnMissingClass注解允许根据特定类是否出现来跳过配置。由于注解元数据是使用[ASM](http://asm.ow2.org/)来解析的，你实际上可以使用value属性来引用真正的类，即使该类可能实际上并没有出现在运行应用的classpath下。如果你倾向于使用一个String值来指定类名，你也可以使用name属性。

2. Bean条件

@ConditionalOnBean和@ConditionalOnMissingBean注解允许根据特定beans是否出现来跳过配置。你可以使用value属性来指定beans（by type），也可以使用name来指定beans（by name）。search属性允许你限制搜索beans时需要考虑的ApplicationContext的层次。

**注**：当@Configuration类被解析时@Conditional注解会被处理。Auto-configure @Configuration总是最后被解析（在所有用户定义beans后面），然而，如果你将那些注解用到常规的@Configuration类，需要注意不能引用那些还没有创建好的bean定义。

3. Property条件

@ConditionalOnProperty注解允许根据一个Spring Environment属性来决定是否包含配置。可以使用prefix和name属性指定要检查的配置属性。默认情况下，任何存在的只要不是false的属性都会匹配。你也可以使用havingValue和matchIfMissing属性创建更高级的检测。

4. Resource条件

@ConditionalOnResource注解允许只有在特定资源出现时配置才会被包含。资源可以使用常见的Spring约定命名，例如file:/home/user/test.dat。

5. Web Application条件

@ConditionalOnWebApplication和@ConditionalOnNotWebApplication注解允许根据应用是否为一个'web应用'来决定是否包含配置。一个web应用是任何使用Spring WebApplicationContext，定义一个session作用域或有一个StandardServletEnvironment的应用。

6. SpEL表达式条件

@ConditionalOnExpression注解允许根据[SpEL表达式](http://docs.spring.io/spring/docs/4.1.4.RELEASE/spring-framework-reference/htmlsingle/#expressions)结果来跳过配置。

### WebSockets

Spring Boot为内嵌的Tomcat(8和7)，Jetty 9和Undertow提供WebSockets自动配置。如果你正在将一个war包部署到一个单独的容器，Spring Boot会假设该容器会对它的WebSocket支持相关的配置负责。

Spring框架提供[丰富的WebSocket支持](http://docs.spring.io/spring/docs/4.1.4.RELEASE/spring-framework-reference/htmlsingle/#websocket)，通过spring-boot-starter-websocket模块可以轻易获取到。

