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

你可以在所有接收文件输入的命令中使用shell通配符。这允许你轻松处理来自一个目录下的多个文件，例如：
```shell
$ spring run *.groovy
```
如果你想将'test'或'spec'代码从主应用代码中分离，这项技术就十分有用了：
```shell
$ spring test app/*.groovy test/*.groovy
```
* 应用打包

你可以使用`jar`命令打包应用程序为一个可执行的jar文件。例如：
```shell
$ spring jar my-app.jar *.groovy
```
最终的jar包括编译应用产生的类和所有依赖，这样你就可以使用`java -jar`来执行它了。该jar文件也包括来自应用classpath的实体。你可以使用`--include`和`--exclude`添加明确的路径（两者都是用逗号分割，同样都接收值为'+'和'-'的前缀，'-'意味着它们将从默认设置中移除）。默认包含（includes）：
```shell
public/**, resources/**, static/**, templates/**, META-INF/**, *
```
默认排除(excludes)：
```shell
.*, repository/**, build/**, target/**, **/*.jar, **/*.groovy
```
查看`spring help jar`可以获得更多信息。

* 初始化新工程

`init`命令允许你使用[start.spring.io](https://start.spring.io/)在不离开shell的情况下创建一个新的项目。例如：
```shell
$ spring init --dependencies=web,data-jpa my-project
Using service at https://start.spring.io
Project extracted to '/Users/developer/example/my-project'
```
这创建了一个`my-project`目录，它是一个基本Maven且依赖`spring-boot-starter-web`和`spring-boot-starter-data-jpa`的项目。你可以使用`--list`参数列出该服务的能力。
```shell
$ spring init --list
=======================================
Capabilities of https://start.spring.io
=======================================

Available dependencies:
-----------------------
actuator - Actuator: Production ready features to help you monitor and manage your application
...
web - Web: Support for full-stack web development, including Tomcat and spring-webmvc
websocket - Websocket: Support for WebSocket development
ws - WS: Support for Spring Web Services

Available project types:
------------------------
gradle-build -  Gradle Config [format:build, build:gradle]
gradle-project -  Gradle Project [format:project, build:gradle]
maven-build -  Maven POM [format:build, build:maven]
maven-project -  Maven Project [format:project, build:maven] (default)

...
```
`init`命令支持很多选项，查看`help`输出可以获得更多详情。例如，下面的命令创建一个使用Java8和war打包的gradle项目：
```shell
$ spring init --build=gradle --java-version=1.8 --dependencies=websocket --packaging=war sample-app.zip
Using service at https://start.spring.io
Content saved to 'sample-app.zip'
```
* 使用内嵌shell

Spring Boot包括完整的BASH和zsh shells的命令行脚本。如果你不使用它们中的任何一个（可能你是一个Window用户），那你可以使用`shell`命令启用一个集成shell。
```shell
$ spring shell
Spring Boot (v1.3.0.BUILD-SNAPSHOT)
Hit TAB to complete. Type \'help' and hit RETURN for help, and \'exit' to quit.
```
从内嵌shell中可以直接运行其他命令：
```shell
$ version
Spring CLI v1.3.0.BUILD-SNAPSHOT
```
内嵌shell支持ANSI颜色输出和tab补全。如果需要运行一个原生命令，你可以使用`$`前缀。点击ctrl-c将退出内嵌shell。

* 为CLI添加扩展

使用`install`命令可以为CLI添加扩展。该命令接收一个或多个格式为`group:artifact:version`的artifact坐标集。例如：
```shell
$ spring install com.example:spring-boot-cli-extension:1.0.0.RELEASE
```
除了安装你提供坐标的artifacts标识外，所有依赖也会被安装。使用`uninstall`可以卸载一个依赖。和`install`命令一样，它接收一个或多个格式为`group:artifact:version`的artifact坐标集。例如：
```shell
$ spring uninstall com.example:spring-boot-cli-extension:1.0.0.RELEASE
```
它会通过你提供的坐标卸载相应的artifacts标识和它们的依赖。

为了卸载所有附加依赖，你可以使用`--all`选项。例如：
```shell
$ spring uninstall --all
```
* 使用Groovy beans DSL开发应用

Spring框架4.0版本对beans{} DSL（借鉴自[Grails](http://grails.org/)）提供原生支持，你可以使用相同的格式在你的Groovy应用程序脚本中嵌入bean定义。有时候这是一个包括外部特性的很好的方式，比如中间件声明。例如：
```java
@Configuration
class Application implements CommandLineRunner {

    @Autowired
    SharedService service

    @Override
    void run(String... args) {
        println service.message
    }

}

import my.company.SharedService

beans {
    service(SharedService) {
        message = "Hello World"
    }
}
```
你可以使用beans{}混合位于相同文件的类声明，只要它们都处于顶级，或如果你喜欢的话，可以将beans DSL放到一个单独的文件中。


