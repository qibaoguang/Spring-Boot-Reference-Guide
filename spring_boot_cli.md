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

- 

* 测试你的代码



* 多源文件应用
* 应用打包
* 初始化新工程
* 使用内嵌shell
* 为CLI添加扩展
* 
