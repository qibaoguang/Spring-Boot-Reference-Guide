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



















