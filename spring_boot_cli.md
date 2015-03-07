### Spring Boot CLI

Spring Boot CLI是一个命令行工具，如果想使用Spring进行快速开发可以使用它。它允许你运行Groovy脚本，这意味着你可以使用熟悉的类Java语法，并且没有那么多的模板代码。你也可以启动一个新的工程或为Spring Boot CLI编写自己的命令。

### 安装CLI

你可以手动安装Spring Boot CLI，也可以使用GVM（Groovy环境管理工具）或Homebrew，MacPorts（如果你是一个OSX用户）。参考"Getting started"的[Section 10.2, “Installing the Spring Boot CLI” ](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#getting-started-installing-the-cli)可以看到全面的安装指令。

### 使用CLI

一旦安装好CLI，你可以输入`spring`来运行它。如果你不使用任何参数运行`spring`，将会展现一个简单的帮助界面：
```shell
$ spring
usage: spring [--help] [--version]
       <command> [<args>]

Available commands are:

  run [options] <files> [--] [args]
    Run a spring groovy script

  ... more command help is shown here
```
你可以使用`help`获取任何支持命令的详细信息。例如：
```shell
$ spring help run
spring run - Run a spring groovy script

usage: spring run [options] <files> [--] [args]

Option                     Description
------                     -----------
--autoconfigure [Boolean]  Add autoconfigure compiler
                             transformations (default: true)
--classpath, -cp           Additional classpath entries
-e, --edit                 Open the file with the default system
                             editor
--no-guess-dependencies    Do not attempt to guess dependencies
--no-guess-imports         Do not attempt to guess imports
-q, --quiet                Quiet logging
-v, --verbose              Verbose logging of dependency
                             resolution
--watch                    Watch the specified file for changes
```
`version`命令提供一个检查你正在使用的Spring Boot版本的快速方式：
```shell
$ spring version
Spring CLI v1.3.0.BUILD-SNAPSHOT
```
* 使用CLI运行应用

你可以使用`run`命令编译和运行Groovy源代码。Spring Boot CLI完全自包含，以致于你不需要安装任何外部的Groovy。

下面是一个使用Groovy编写的"hello world" web应用：
hello.grooy
```java
@RestController
class WebApplication {

    @RequestMapping("/")
    String home() {
        "Hello World!"
    }

}
```
想要编译和运行应用，输入：
```shell
$ spring run hello.groovy
```
想要给应用传递命令行参数，你需要使用一个`--`来将它们和"spring"命令参数区分开来。例如：
```shell
$ spring run hello.groovy -- --server.port=9000
```
想要设置JVM命令行参数，你可以使用`JAVA_OPTS`环境变量，例如：
```shell
$ JAVA_OPTS=-Xmx1024m spring run hello.groovy
```
- 推断"grab"依赖

标准的Groovy包含一个`@Grab`注解，它允许你声明对第三方库的依赖。这项有用的技术允许Groovy以和Maven或Gradle相同的方式下载jars，但不需要使用构建工具。

Spring Boot进一步延伸了该技术，它会基于你的代码尝试推导你"grab"哪个库。例如，由于WebApplication代码上使用了`@RestController`注解，"Tomcat"和"Spring MVC"将被获取（grabbed）。

下面items被用作"grab hints"：

|items|Grabs|
|-----|:-----|
|JdbcTemplate,NamedParameterJdbcTemplate,DataSource|JDBC应用|
|@EnableJms|JMS应用|
|@EnableCaching|Caching abstraction|
|@Test|JUnit|
|@EnableRabbit|RabbitMQ|
|@EnableReactor|Project Reactor|
|继承Specification|Spock test|
|@EnableBatchProcessing|Spring Batch|
|@MessageEndpoint,@EnableIntegrationPatterns|Spring Integration|
|@EnableDeviceResolver|Spring Mobile|
|@Controller,@RestController,@EnableWebMvc|Spring MVC + Embedded Tomcat|
|@EnableWebSecurity|Spring Security|
|@EnableTransactionManagement|Spring Transaction Management|

**注**：想要理解自定义是如何生效，可以查看Spring Boot CLI源码中的[CompilerAutoConfiguration](http://github.com/spring-projects/spring-boot/tree/master/spring-boot-cli/src/main/java/org/springframework/boot/cli/compiler/CompilerAutoConfiguration.java)子类。

- 推断"grab"坐标

Spring Boot扩展Groovy标准"@Grab"注解使其能够允许你指定一个没有group或version的依赖，例如`@Grab('freemarker')`。
artifact’s的组和版本是通过查看Spring Boot的依赖元数据推断出来的。注意默认的元数据是和你使用的CLI版本绑定的－只有在你迁移到一个CLI新版本时它才会改变，这样当你的依赖改变时你就可以控制了。在[附录](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#appendix-dependency-versions)的表格中可以查看默认元数据包含的依赖和它们的版本。

- 默认import语句

为了帮助你减少Groovy代码量，一些`import`语句被自动包含进来了。注意上面的示例中引用`@Component`，`@RestController`和`@RequestMapping`而没有使用全限定名或`import`语句。

**注**：很多Spring注解在不使用`import`语句的情况下可以正常工作。尝试运行你的应用，看一下在添加imports之前哪些会失败。

- 自动创建main方法

跟等效的Java应用不同，你不需要在Groovy脚本中添加一个`public static void main(String[] args)`方法。Spring Boot 会使用你编译后的代码自动创建一个SpringApplication。

- 自定义"grab"元数据

Spring Boot提供一个新的`@GrabMetadata`注解，你可以使用它提供自定义的依赖元数据，以覆盖Spring Boot的默认配置。该元数据通过使用提供一个或多个配置文件坐标的注解来指定（使用一个属性标识符"type"部署到Maven仓库）.。配置文件中的每个实体必须遵循`group:module=version`的格式。

例如，下面的声明：
```java
`@GrabMetadata("com.example.custom-versions:1.0.0")`
```
将会加载Maven仓库处于`com/example/custom-versions/1.0.0/`下的`custom-versions-1.0.0.properties`文件。

可以通过注解指定多个属性文件，它们会以声明的顺序被使用。例如：
```java
`@GrabMetadata(["com.example.custom-versions:1.0.0",
        "com.example.more-versions:1.0.0"])`
```
意味着位于`more-versions`的属性将覆盖位于`custom-versions`的属性。

你可以在任何能够使用`@Grab`的地方使用`@GrabMetadata`，然而，为了确保元数据的顺序一致，你在应用程序中最多只能使用一次`@GrabMetadata`。[Spring IO Platform](http://platform.spring.io/)是一个非常有用的依赖元数据源(Spring Boot的超集)，例如：
```java
@GrabMetadata('io.spring.platform:platform-versions:1.0.4.RELEASE')
```
* 测试你的代码

`test`命令允许你编译和运行应用程序的测试用例。常规使用方式如下：
```shell
$ spring test app.groovy tests.groovy
Total: 1, Success: 1, : Failures: 0
Passed? true
```
在这个示例中，`test.groovy`包含JUnit `@Test`方法或Spock `Specification`类。所有的普通框架注解和静态方法在不使用import导入的情况下，仍旧可以使用。

下面是我们使用的`test.groovy`文件（含有一个JUnit测试）：
```java
class ApplicationTests {

    @Test
    void homeSaysHello() {
        assertEquals("Hello World!", new WebApplication().home())
    }

}
```
**注**：如果有多个测试源文件，你可以倾向于使用一个test目录来组织它们。

* 多源文件应用



* 应用打包
* 初始化新工程
* 使用内嵌shell
* 为CLI添加扩展
* 
