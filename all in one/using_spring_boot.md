### 使用Spring Boot
本章节将会详细介绍如何使用Spring Boot。它覆盖了构建系统，自动配置和运行/部署选项等主题。我们也覆盖了一些Spring Boot最佳实践。尽管Spring Boot没有什么特别的（只是一个你能消费的库），但仍有一些建议，如果你遵循的话将会让你的开发进程更容易。

如果你刚接触Spring Boot，那最好先读下上一章节的[Getting Started](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#getting-started)指南。

### 构建系统

强烈建议你选择一个支持依赖管理，能消费发布到Maven中央仓库的artifacts的构建系统。我们推荐你选择Maven或Gradle。选择其他构建系统来使用Spring Boot也是可能的（比如Ant），但它们不会被很好的支持。

* Maven

Maven用户可以继承`spring-boot-starter-parent`项目来获取合适的默认设置。该父项目提供以下特性：
- 默认编译级别为Java 1.6
- 源码编码为UTF-8
- 一个依赖管理节点，允许你省略普通依赖的`<version>`标签，继承自`spring-boot-dependencies` POM。
- 合适的[资源过滤](https://maven.apache.org/plugins/maven-resources-plugin/examples/filter.html)
- 合适的插件配置（[exec插件](http://mojo.codehaus.org/exec-maven-plugin/)，[surefire](http://maven.apache.org/surefire/maven-surefire-plugin/)，[Git commit ID](https://github.com/ktoso/maven-git-commit-id-plugin)，[shade](http://maven.apache.org/plugins/maven-shade-plugin/)）
- 针对`application.properties`和`application.yml`的资源过滤

最后一点：由于默认配置文件接收Spring风格的占位符（`${...}`），Maven filtering改用`@..@`占位符（你可以使用Maven属性`resource.delimiter`来覆盖它）。

* 继承starter parent

想配置你的项目继承`spring-boot-starter-parent`只需要简单地设置`parent`为：
```xml
<!-- Inherit defaults from Spring Boot -->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.3.0.BUILD-SNAPSHOT</version>
</parent>
```
**注**：你应该只需要在该依赖上指定Spring Boot版本。如果导入其他的starters，你可以放心的省略版本号。

* 使用没有父POM的Spring Boot

不是每个人都喜欢继承`spring-boot-starter-parent` POM。你可能需要使用公司标准parent，或你可能倾向于显式声明所有Maven配置。

如果你不使用`spring-boot-starter-parent`，通过使用一个`scope=import`的依赖，你仍能获取到依赖管理的好处：
```xml
<dependencyManagement>
     <dependencies>
        <dependency>
            <!-- Import dependency management from Spring Boot -->
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>1.3.0.BUILD-SNAPSHOT</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```
* 改变Java版本 

`spring-boot-starter-parent`选择相当保守的Java兼容策略。如果你遵循我们的建议，使用最新的Java版本，你可以添加一个`java.version`属性：
```xml
<properties>
    <java.version>1.8</java.version>
</properties>
```
* 使用Spring Boot Maven插件

Spring Boot包含一个[Maven插件](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#build-tool-plugins-maven-plugin)，它可以将项目打包成一个可执行jar。如果想使用它，你可以将该插件添加到`<plugins>`节点处：
```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```
**注**：如果使用Spring Boot starter parent pom，你只需要添加该插件而无需配置它，除非你想改变定义在partent中的设置。

* Gradle

Gradle用户可以直接在它们的`dependencies`节点处导入”starter POMs“。跟Maven不同的是，这里没有用于导入共享配置的"超父"（super parent）。
```gradle
apply plugin: 'java'

repositories { jcenter() }
dependencies {
    compile("org.springframework.boot:spring-boot-starter-web:1.3.0.BUILD-SNAPSHOT")
}
```
[spring-boot-gradle-plugin](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#build-tool-plugins-gradle-plugin)插件也是可以使用的，它提供创建可执行jar和从source运行项目的任务。它也添加了一个`ResolutionStrategy`用于让你省略常用依赖的版本号：
```gradle
buildscript {
    repositories { jcenter() }
    dependencies {
        classpath("org.springframework.boot:spring-boot-gradle-plugin:1.3.0.BUILD-SNAPSHOT")
    }
}

apply plugin: 'java'
apply plugin: 'spring-boot'

repositories { jcenter() }
dependencies {
    compile("org.springframework.boot:spring-boot-starter-web")
    testCompile("org.springframework.boot:spring-boot-starter-test")
}
```
* Ant

使用Apache Ant构建一个Spring Boot项目是完全可能的，然而，Spring Boot没有为它提供特殊的支持或插件。Ant脚本可以使用Ivy依赖管理系统来导入starter POMs。

查看[Section 73.8, “Build an executable archive with Ant”](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#howto-build-an-executable-archive-with-ant)获取更多指导。

* Starter POMs

Starter POMs是可以包含到应用中的一个方便的依赖关系描述符集合。你可以获取所有Spring及相关技术的一站式服务，而不需要翻阅示例代码，拷贝粘贴大量的依赖描述符。例如，如果你想使用Spring和JPA进行数据库访问，只需要在你的项目中包含`spring-boot-starter-data-jpa`依赖，然后你就可以开始了。

该starters包含很多你搭建项目，快速运行所需的依赖，并提供一致的，管理的传递依赖集。

**名字有什么含义**：所有的starters遵循一个相似的命名模式：`spring-boot-starter-*`，在这里`*`是一种特殊类型的应用程序。该命名结构旨在帮你找到需要的starter。很多IDEs集成的Maven允许你通过名称搜索依赖。例如，使用相应的Eclipse或STS插件，你可以简单地在POM编辑器中点击`ctrl-space`，然后输入"spring-boot-starter"可以获取一个完整列表。

下面的应用程序starters是Spring Boot在`org.springframework.boot`组下提供的：

**表 13.1. Spring Boot application starters** 

|名称|描述|
|------|:-----|
|spring-boot-starter|核心Spring Boot starter，包括自动配置支持，日志和YAML|
|spring-boot-starter-actuator|生产准备的特性，用于帮你监控和管理应用|
|spring-boot-starter-amqp|对"高级消息队列协议"的支持，通过`spring-rabbit`实现|
|spring-boot-starter-aop|对面向切面编程的支持，包括`spring-aop`和AspectJ|
|spring-boot-starter-batch|对Spring Batch的支持，包括HSQLDB数据库|
|spring-boot-starter-cloud-connectors|对Spring Cloud Connectors的支持，简化在云平台下（例如，Cloud Foundry 和Heroku）服务的连接|
|spring-boot-starter-data-elasticsearch|对Elasticsearch搜索和分析引擎的支持，包括`spring-data-elasticsearch`|
|spring-boot-starter-data-gemfire|对GemFire分布式数据存储的支持，包括`spring-data-gemfire`|
|spring-boot-starter-data-jpa|对"Java持久化API"的支持，包括`spring-data-jpa`，`spring-orm`和Hibernate|
|spring-boot-starter-data-mongodb|对MongoDB NOSQL数据库的支持，包括`spring-data-mongodb`|
|spring-boot-starter-data-rest|对通过REST暴露Spring Data仓库的支持，通过`spring-data-rest-webmvc`实现|
|spring-boot-starter-data-solr|对Apache Solr搜索平台的支持，包括`spring-data-solr`|
|spring-boot-starter-freemarker|对FreeMarker模板引擎的支持|
|spring-boot-starter-groovy-templates|对Groovy模板引擎的支持|
|spring-boot-starter-hateoas|对基于HATEOAS的RESTful服务的支持，通过`spring-hateoas`实现|
|spring-boot-starter-hornetq|对"Java消息服务API"的支持，通过HornetQ实现|
|spring-boot-starter-integration|对普通`spring-integration`模块的支持|
|spring-boot-starter-jdbc|对JDBC数据库的支持|
|spring-boot-starter-jersey|对Jersey RESTful Web服务框架的支持|
|spring-boot-starter-jta-atomikos|对JTA分布式事务的支持，通过Atomikos实现|
|spring-boot-starter-jta-bitronix|对JTA分布式事务的支持，通过Bitronix实现|
|spring-boot-starter-mail|对`javax.mail`的支持|
|spring-boot-starter-mobile|对`spring-mobile`的支持|
|spring-boot-starter-mustache|对Mustache模板引擎的支持|
|spring-boot-starter-redis|对REDIS键值数据存储的支持，包括`spring-redis`|
|spring-boot-starter-security|对`spring-security`的支持|
|spring-boot-starter-social-facebook|对`spring-social-facebook`的支持|
|spring-boot-starter-social-linkedin|对`spring-social-linkedin`的支持|
|spring-boot-starter-social-twitter|对`spring-social-twitter`的支持|
|spring-boot-starter-test|对常用测试依赖的支持，包括JUnit, Hamcrest和Mockito，还有`spring-test`模块|
|spring-boot-starter-thymeleaf|对Thymeleaf模板引擎的支持，包括和Spring的集成|
|spring-boot-starter-velocity|对Velocity模板引擎的支持|
|spring-boot-starter-web|对全栈web开发的支持，包括Tomcat和`spring-webmvc`|
|spring-boot-starter-websocket|对WebSocket开发的支持|
|spring-boot-starter-ws|对Spring Web服务的支持|

除了应用程序的starters，下面的starters可以用于添加[生产准备](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#production-ready)的特性。

**表 13.2. Spring Boot生产准备的starters**

|名称|描述|
|----|:----|
|spring-boot-starter-actuator|添加生产准备特性，比如指标和监控|
|spring-boot-starter-remote-shell|添加远程`ssh` shell支持|

最后，Spring Boot包含一些可用于排除或交换具体技术方面的starters。

**表 13.3. Spring Boot technical starters**

|名称|描述|
|------|:------|
|spring-boot-starter-jetty|导入Jetty HTTP引擎（作为Tomcat的替代）|
|spring-boot-starter-log4j|对Log4J日志系统的支持|
|spring-boot-starter-logging|导入Spring Boot的默认日志系统（Logback）|
|spring-boot-starter-tomcat|导入Spring Boot的默认HTTP引擎（Tomcat）|
|spring-boot-starter-undertow|导入Undertow HTTP引擎（作为Tomcat的替代）|

**注**：查看GitHub上位于`spring-boot-starters`模块内的[README文件](http://github.com/spring-projects/spring-boot/tree/master/spring-boot-starters/README.adoc)，可以获取到一个社区贡献的其他starter POMs列表。

### 组织你的代码

Spring Boot不需要使用任何特殊的代码结构，然而，这里有一些有用的最佳实践。

* 使用"default"包

当类没有包含`package`声明时，它被认为处于`default package`下。通常不推荐使用`default package`，并应该避免使用它。因为对于使用`@ComponentScan`，`@EntityScan`或`@SpringBootApplication`注解的Spring Boot应用来说，来自每个jar的类都会被读取，这会造成一定的问题。

**注**：我们建议你遵循Java推荐的包命名规范，使用一个反转的域名（例如`com.example.project`）。

* 定位main应用类

我们通常建议你将main应用类放在位于其他类上面的根包（root package）中。通常使用`@EnableAutoConfiguration`注解你的main类，并且暗地里为某些项定义了一个基础“search package”。例如，如果你正在编写一个JPA应用，被`@EnableAutoConfiguration`注解的类所在包将被用来搜索`@Entity`项。

使用根包允许你使用`@ComponentScan`注解而不需要定义一个`basePackage`属性。如果main类位于根包中，你也可以使用`@SpringBootApplication`注解。

下面是一个典型的结构：
```shell
com
 +- example
     +- myproject
         +- Application.java
         |
         +- domain
         |   +- Customer.java
         |   +- CustomerRepository.java
         |
         +- service
         |   +- CustomerService.java
         |
         +- web
             +- CustomerController.java
```
`Application.java`文件将声明`main`方法，还有基本的`@Configuration`。
```java
package com.example.myproject;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@EnableAutoConfiguration
@ComponentScan
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

}
```
### 配置类

Spring Boot提倡基于Java的配置。尽管你可以使用一个XML源来调用`SpringApplication.run()`，我们通常建议你使用`@Configuration`类作为主要源。一般定义`main`方法的类也是主要`@Configuration`的一个很好候选。

**注**：很多使用XML配置的Spring配置示例已经被发布到网络上。你应该总是尽可能的使用基于Java的配置。搜索查看`enable*`注解就是一个好的开端。

* 导入其他配置类

你不需要将所有的`@Configuration`放进一个单独的类。`@Import`注解可以用来导入其他配置类。另外，你也可以使用`@ComponentScan`注解自动收集所有的Spring组件，包括`@Configuration`类。

* 导入XML配置

如果你绝对需要使用基于XML的配置，我们建议你仍旧从一个`@Configuration`类开始。你可以使用附加的`@ImportResource`注解加载XML配置文件。

### 自动配置

Spring Boot自动配置（auto-configuration）尝试根据你添加的jar依赖自动配置你的Spring应用。例如，如果你的classpath下存在`HSQLDB`，并且你没有手动配置任何数据库连接beans，那么我们将自动配置一个内存型（in-memory）数据库。

你可以通过将`@EnableAutoConfiguration`或`@SpringBootApplication`注解添加到一个`@Configuration`类上来选择自动配置。

**注**：你只需要添加一个`@EnableAutoConfiguration`注解。我们建议你将它添加到主`@Configuration`类上。

* 逐步替换自动配置

自动配置是非侵占性的，任何时候你都可以定义自己的配置类来替换自动配置的特定部分。例如，如果你添加自己的`DataSource`  bean，默认的内嵌数据库支持将不被考虑。

如果需要找出当前应用了哪些自动配置及应用的原因，你可以使用`--debug`开关启动应用。这将会记录一个自动配置的报告并输出到控制台。

* 禁用特定的自动配置

如果发现应用了你不想要的特定自动配置类，你可以使用`@EnableAutoConfiguration`注解的排除属性来禁用它们。
```java
import org.springframework.boot.autoconfigure.*;
import org.springframework.boot.autoconfigure.jdbc.*;
import org.springframework.context.annotation.*;

@Configuration
@EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class})
public class MyConfiguration {
}
```

### Spring Beans和依赖注入

你可以自由地使用任何标准的Spring框架技术去定义beans和它们注入的依赖。简单起见，我们经常使用`@ComponentScan`注解搜索beans，并结合`@Autowired`构造器注入。

如果使用上面建议的结构组织代码（将应用类放到根包下），你可以添加`@ComponentScan`注解而不需要任何参数。你的所有应用程序组件（`@Component`, `@Service`, `@Repository`, `@Controller`等）将被自动注册为Spring Beans。

下面是一个`@Service` Bean的示例，它使用构建器注入获取一个需要的`RiskAssessor` bean。
```java
package com.example.service;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class DatabaseAccountService implements AccountService {

    private final RiskAssessor riskAssessor;

    @Autowired
    public DatabaseAccountService(RiskAssessor riskAssessor) {
        this.riskAssessor = riskAssessor;
    }

    // ...
}
```
**注**：注意如何使用构建器注入来允许`riskAssessor`字段被标记为`final`，这意味着`riskAssessor`后续是不能改变的。

### 使用@SpringBootApplication注解

很多Spring Boot开发者总是使用`@Configuration`，`@EnableAutoConfiguration`和`@ComponentScan`注解他们的main类。由于这些注解被如此频繁地一块使用（特别是你遵循以上[最佳实践](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#using-boot-structuring-your-code)时），Spring Boot提供一个方便的`@SpringBootApplication`选择。

该`@SpringBootApplication`注解等价于以默认属性使用`@Configuration`，`@EnableAutoConfiguration`和`@ComponentScan`。
```java
package com.example.myproject;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication // same as @Configuration @EnableAutoConfiguration @ComponentScan
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

}
```
### 运行应用程序

将应用打包成jar并使用一个内嵌HTTP服务器的一个最大好处是，你可以像其他方式那样运行你的应用程序。调试Spring Boot应用也很简单；你不需要任何特殊IDE或扩展。

**注**：本章节只覆盖基于jar的打包，如果选择将应用打包成war文件，你最好参考一下服务器和IDE文档。

* 从IDE中运行

你可以从IDE中运行Spring Boot应用，就像一个简单的Java应用，但是，你首先需要导入项目。导入步骤跟你的IDE和构建系统有关。大多数IDEs能够直接导入Maven项目，例如Eclipse用户可以选择`File`菜单的`Import…​` --> `Existing Maven Projects`。

如果不能直接将项目导入IDE，你可以需要使用构建系统生成IDE元数据。Maven有针对[Eclipse](http://maven.apache.org/plugins/maven-eclipse-plugin/)和[IDEA](http://maven.apache.org/plugins/maven-idea-plugin/)的插件；Gradle为[各种IDEs](http://www.gradle.org/docs/current/userguide/ide_support.html)提供插件。

**注**：如果意外地运行一个web应用两次，你将看到一个"端口已在使用中"错误。为了确保任何存在的实例是关闭的，STS用户可以使用`Relaunch`按钮而不是`Run`按钮。

* 作为一个打包后的应用运行

如果使用Spring Boot Maven或Gradle插件创建一个可执行jar，你可以使用`java -jar`运行你的应用。例如：
```shell
$ java -jar target/myproject-0.0.1-SNAPSHOT.jar
```
运行一个打包的程序并开启远程调试支持是可能的，这允许你将调试器附加到打包的应用程序上：
```shell
$ java -Xdebug -Xrunjdwp:server=y,transport=dt_socket,address=8000,suspend=n \
       -jar target/myproject-0.0.1-SNAPSHOT.jar
```
* 使用Maven插件运行

Spring Boot Maven插件包含一个`run`目标，它可以用来快速编译和运行应用程序。应用程序以一种暴露的方式运行，由于即时"热"加载，你可以编辑资源。
```shell
$ mvn spring-boot:run
```
你可能想使用有用的操作系统环境变量：
```shell
$ export MAVEN_OPTS=-Xmx1024m -XX:MaxPermSize=128M -Djava.security.egd=file:/dev/./urandom
```
("egd"设置是通过为Tomcat提供一个更快的会话keys熵源来加速Tomcat的。)

* 使用Gradle插件运行

Spring Boot Gradle插件也包含一个`run`目标，它可以用来以暴露的方式运行你的应用程序。不管你什么时候导入`spring-boot-plugin`，`bootRun`任务总是被添加进去。
```shell
$ gradle bootRun
```
你可能想使用那些有用的操作系统环境变量：
```shell
$ export JAVA_OPTS=-Xmx1024m -XX:MaxPermSize=128M -Djava.security.egd=file:/dev/./urandom
```
* 热交换

由于Spring Boot应用程序只是普通的Java应用，那JVM热交换（hot-swapping）应该能出色的工作。JVM热交换在它能替换的字节码上有些限制，更全面的解决方案可以使用[Spring Loaded](https://github.com/spring-projects/spring-loaded)项目或[JRebel](http://zeroturnaround.com/software/jrebel/)。

关于热交换可以参考[“How-to”](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#howto-hotswapping)章节。

### 打包用于生产的应用程序

可执行jars可用于生产部署。由于它们是自包含的，非常适合基于云的部署。关于其他“生产准备”的特性，比如健康监控，审计和指标REST，或JMX端点，可以考虑添加`spring-boot-actuator`。具体参考[Part V, “Spring Boot Actuator: Production-ready features”](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#production-ready)。





