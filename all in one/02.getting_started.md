### 开始

如果你想从总体上对Spring Boot或Spring入门，本章节就是为你准备的！在这里，我们将回答基本的"what?"，"how?"和"why?"问题。你会发现一个温雅的Spring Boot介绍及安装指南。然后我们构建第一个Spring Boot应用，并讨论一些我们需要遵循的核心原则。

### Spring Boot介绍

Spring Boot使开发独立的，产品级别的基于Spring的应用变得非常简单，你只需"just run"。
我们为Spring平台及第三方库提供开箱即用的设置，这样你就可以有条不紊地开始。多数Spring Boot应用需要很少的Spring配置。

你可以使用Spring Boot创建Java应用，并使用`java -jar`启动它或采用传统的war部署方式。我们也提供了一个运行"spring脚本"的命令行工具。

我们主要的目标是：

- 为所有的Spring开发提供一个从根本上更快的和广泛使用的入门经验。
- 开箱即用，但你可以通过不采用默认设置来摆脱这种方式。
- 提供一系列大型项目常用的非功能性特征（比如，内嵌服务器，安全，指标，健康检测，外部化配置）。
- 绝对不需要代码生成及XML配置。

### 系统要求

默认情况下，Spring Boot 1.3.0.BUILD-SNAPSHOT 需要Java７和Spring框架4.1.3或以上。你可以在Java6下使用Spring Boot，不过需要添加额外配置。具体参考[Section 73.9, “How to use Java 6” ](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#howto-use-java-6)。构建环境明确支持的有Maven（3.2+）和Gradle（1.12+）。

**注**：尽管你可以在Java6或Java7环境下使用Spring Boot，通常我们建议你如果可能的话就使用Java8。

* Servlet容器

下列内嵌容器支持开箱即用（out of the box）：

|名称|Servlet版本|Java版本|
|--------|:-------|:-------|
|Tomcat 8|3.1|Java 7+|
|Tomcat 7|3.0|Java 6+|
|Jetty 9|3.1|Java 7+|
|Jetty 8|3.0|Java 6+|
|Undertow 1.1|3.1|Java 7+|

你也可以将Spring Boot应用部署到任何兼容Servlet 3.0+的容器。

### Spring Boot安装

Spring Boot可以跟典型的Java开发工具一块使用或安装为一个命令行工具。不管怎样，你将需要安装[Java SDK v1.6 ](http://www.java.com/)或更高版本。在开始之前，你需要检查下当前安装的Java版本：
```shell
$ java -version
```
如果你是一个Java新手，或你只是想体验一下Spring Boot，你可能想先尝试[Spring Boot CLI](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#getting-started-installing-the-cli)，否则继续阅读经典地安装指南。

**注**：尽管Spring Boot兼容Java 1.6，如果可能的话，你应该考虑使用Java最新版本。

* 为Java开发者准备的安装指南

你可以以和任何标准Java库相同的方式使用Spring Boot。只需要简单地在你的classpath下包含正确的`spring-boot-*.jar`文件。Spring Boot不需要集成任何特殊的工具，所以你可以使用任何IDE或文本编辑器；Spring Boot应用也没有什么特殊之处，所以你可以像任何其他Java程序那样运行和调试。

尽管你可以拷贝Spring Boot jars，不过，我们通常推荐你使用一个支持依赖管理的构建工具（比如Maven或Gradle）。

* Maven安装

Spring Boot兼容Apache Maven 3.2或更高版本。如果没有安装Maven，你可以参考[maven.apache.org](http://maven.apache.org/)指南。

**注**：在很多操作系统上，你可以通过一个包管理器安装Maven。如果你是一个OSX Homebrew用户，可以尝试`brew install maven`。Ubuntu用户可以运行`sudo apt-get install maven`。

Spring Boot依赖的`groupId`为`org.springframework.boot`。通常你的Maven POM文件需要继承`spring-boot-starter-parent`，然后声明一个或多个[“Starter POMs”](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#using-boot-starter-poms)依赖。Spring Boot也提供了一个用于创建可执行jars的[Maven插件](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#build-tool-plugins-maven-plugin)。

下面是一个典型的`pom.xml`文件：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>myproject</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <!-- Inherit defaults from Spring Boot -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.3.0.BUILD-SNAPSHOT</version>
    </parent>

    <!-- Add typical dependencies for a web application -->
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>

    <!-- Package as an executable jar -->
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

    <!-- Add Spring repositories -->
    <!-- (you don't need this if you are using a .RELEASE version) -->
    <repositories>
        <repository>
            <id>spring-snapshots</id>
            <url>http://repo.spring.io/snapshot</url>
            <snapshots><enabled>true</enabled></snapshots>
        </repository>
        <repository>
            <id>spring-milestones</id>
            <url>http://repo.spring.io/milestone</url>
        </repository>
    </repositories>
    <pluginRepositories>
        <pluginRepository>
            <id>spring-snapshots</id>
            <url>http://repo.spring.io/snapshot</url>
        </pluginRepository>
        <pluginRepository>
            <id>spring-milestones</id>
            <url>http://repo.spring.io/milestone</url>
        </pluginRepository>
    </pluginRepositories>
</project>
```
**注**：`spring-boot-starter-parent`是使用Spring Boot的一个不错的方式，但它不总是合适的。有时你需要继承一个不同的parent POM，或者你可能只是不喜欢我们的默认配置。查看[Section 13.1.2, “Using Spring Boot without the parent POM”](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#using-boot-maven-without-a-parent)获取使用`import`的替代解决方案。

* Gradle安装

Spring Boot兼容Gradle 1.12或更高版本。如果没有安装Gradle，你可以参考[www.gradle.org](http://www.gradle.org/)上的指南。

Spring Boot依赖可以使用`org.springframework.boot` `group`来声明。通常，你的项目将声明一个或多个[“Starter POMs”](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#using-boot-starter-poms)依赖。Spring Boot提供一个用于简化依赖声明和创建可执行jars的有用的[Gradle插件](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#build-tool-plugins-gradle-plugin)。

**注**：当你需要构建一个项目时，Gradle Wrapper提供一个获取Gradle的漂亮方式。它是一个伴随你的代码一块提交的小脚本和库，用于启动构建进程。具体参考[Gradle Wrapper](www.gradle.org/docs/current/userguide/gradle_wrapper.html)。

下面是一个典型的`build.gradle`文件：
```gradle
buildscript {
    repositories {
        jcenter()
        maven { url "http://repo.spring.io/snapshot" }
        maven { url "http://repo.spring.io/milestone" }
    }
    dependencies {
        classpath("org.springframework.boot:spring-boot-gradle-plugin:1.3.0.BUILD-SNAPSHOT")
    }
}

apply plugin: 'java'
apply plugin: 'spring-boot'

jar {
    baseName = 'myproject'
    version =  '0.0.1-SNAPSHOT'
}

repositories {
    jcenter()
    maven { url "http://repo.spring.io/snapshot" }
    maven { url "http://repo.spring.io/milestone" }
}

dependencies {
    compile("org.springframework.boot:spring-boot-starter-web")
    testCompile("org.springframework.boot:spring-boot-starter-test")
}

```
* Spring Boot CLI安装

Spring Boot是一个命令行工具，用于使用Spring进行快速原型搭建。它允许你运行[Groovy](http://groovy.codehaus.org/)脚本，这意味着你可以使用类Java的语法，并且没有那么多的模板代码。

你没有必要为了使用Spring Boot而去用CLI，但它绝对是助力Spring应用的最快方式。

- 手动安装

你可以从Spring软件仓库下载Spring CLI分发包：

1. [spring-boot-cli-1.3.0.BUILD-SNAPSHOT-bin.zip](http://repo.spring.io/snapshot/org/springframework/boot/spring-boot-cli/1.3.0.BUILD-SNAPSHOT/spring-boot-cli-1.3.0.BUILD-SNAPSHOT-bin.zip)
2. [spring-boot-cli-1.3.0.BUILD-SNAPSHOT-bin.tar.gz](http://repo.spring.io/snapshot/org/springframework/boot/spring-boot-cli/1.3.0.BUILD-SNAPSHOT/spring-boot-cli-1.3.0.BUILD-SNAPSHOT-bin.tar.gz)

不稳定的[snapshot分发包](http://repo.spring.io/snapshot/org/springframework/boot/spring-boot-cli/)也能获取到。

下载完成后，遵循解压后的存档里的[INSTALL.txt](http://raw.github.com/spring-projects/spring-boot/master/spring-boot-cli/src/main/content/INSTALL.txt)操作指南进行安装。一般而言，在`.zip`文件的`bin/`目录下存在一个`spring`脚本（Windows下是`spring.bat`），或者使用`java -jar`来运行一个`.jar`文件（该脚本会帮你确定classpath被正确设置）。

- 使用GVM安装

GVM（Groovy环境管理器）可以用来管理多种不同版本的Groovy和Java二进制包，包括Groovy自身和Spring Boot CLI。可以从[gvmtool.net](http://gvmtool.net/)获取`gvm`，并使用以下命令安装Spring Boot：
```shell
$ gvm install springboot
$ spring --version
Spring Boot v1.3.0.BUILD-SNAPSHOT
```
如果你正在为CLI开发新的特性，并想轻松获取你刚构建的版本，可以使用以下命令：
```shell
$ gvm install springboot dev /path/to/spring-boot/spring-boot-cli/target/spring-boot-cli-1.3.0.BUILD-SNAPSHOT-bin/spring-1.3.0.BUILD-SNAPSHOT/
$ gvm use springboot dev
$ spring --version
Spring CLI v1.3.0.BUILD-SNAPSHOT
```
这将会在你的gvm仓库中安装一个名叫`dev`的本地`spring`实例。它指向你的目标构建位置，所以每次你重新构建Spring Boot，`spring`将会是最新的。

你可以通过以下命令来验证：
```shell
$ gvm ls springboot

================================================================================
Available Springboot Versions
================================================================================
> + dev
* 1.3.0.BUILD-SNAPSHOT

================================================================================
+ - local version
* - installed
> - currently in use
================================================================================
```
- 使用OSX Homebrew进行安装

如果你的环境是Mac，并使用[Homebrew](http://brew.sh/)，想要安装Spring Boot CLI只需如下操作：
```shell
$ brew tap pivotal/tap
$ brew install springboot
```
Homebrew将把`spring`安装到`/usr/local/bin`下。

**注**：如果该方案不可用，可能是因为你的brew版本太老了。你只需执行`brew update`并重试即可。

- 使用MacPorts进行安装

如果你的环境是Mac，并使用[MacPorts](http://www.macports.org/)，想要安装Spring Boot CLI只需如下操作：
```shell
$ sudo port install spring-boot-cli
```
- 命令行实现

Spring Boot CLI启动脚本为[BASH](http://en.wikipedia.org/wiki/Bash_%28Unix_shell%29)和[zsh](http://en.wikipedia.org/wiki/Zsh) shells提供完整的命令行实现。你可以在任何shell中`source`脚本（名称也是`spring`），或将它放到你个人或系统范围的bash实现初始化中。在一个Debian系统里，系统范围的脚本位于`/shell-completion/bash`下，当一个新的shell启动时该目录下的所有脚本都被执行。想要手动运行该脚本，例如，你已经使用`GVM`进行安装了：
```shell
$ . ~/.gvm/springboot/current/shell-completion/bash/spring
$ spring <HIT TAB HERE>
  grab  help  jar  run  test  version
```

**注**：如果你使用Homebrew或MacPorts安装Spring Boot CLI，命令行实现脚本会自动注册到你的shell。

- Spring CLI示例快速入门

下面是一个相当简单的web应用，你可以用它测试你的安装是否成功。创建一个名叫`app.groovy`的文件：
```groovy
@RestController
class ThisWillActuallyRun {

    @RequestMapping("/")
    String home() {
        "Hello World!"
    }

}
```
然后简单地从一个shell中运行它：
```shell
$ spring run app.groovy
```
**注**：当你首次运行该应用时将会花费一点时间，因为需要下载依赖。后续运行将会快很多。

在你最喜欢的浏览器中打开[localhost:8080](localhost:8080)，然后你应该看到以下输出：
```java
Hello World!
```
### 从Spring Boot早期版本升级

如果你正在升级一个Spring Boot早期版本，查看下放在[project wiki](http://github.com/spring-projects/spring-boot/wiki)上的"release notes"。你会发现每次发布的更新指南和一个"new and noteworthy"特性列表。

想要升级一个已安装的CLI，你需要使用合适的包管理命令（例如，`brew upgrade`），或如果你是手动安装CLI，按照[standard instructions](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#getting-started-manual-cli-installation)操作并记得更新你的`PATH`环境变量以移除任何老的引用。

### 开发你的第一个Spring Boot应用

让我们使用Java开发一个简单的"Hello World!" web应用，来强调下Spring Boot的一些关键特性。我们将使用Maven构建该项目，因为大多数IDEs都支持它。

**注**：[spring.io](http://spring.io/)网站包含很多使用Spring Boot的"入门"指南。如果你正在找特定问题的解决方案，可以先去那瞅瞅。

在开始前，你需要打开一个终端，检查是否安装可用的Java版本和Maven：
```shell
$ java -version
java version "1.7.0_51"
Java(TM) SE Runtime Environment (build 1.7.0_51-b13)
Java HotSpot(TM) 64-Bit Server VM (build 24.51-b03, mixed mode)
```
```shell
$ mvn -v
Apache Maven 3.2.3 (33f8c3e1027c3ddde99d3cdebad2656a31e8fdf4; 2014-08-11T13:58:10-07:00)
Maven home: /Users/user/tools/apache-maven-3.1.1
Java version: 1.7.0_51, vendor: Oracle Corporation
```
**注**：该示例需要创建自己的文件夹。后续的操作假设你已创建一个合适的文件夹，并且它是你的“当前目录”。

* 创建POM

我们需要以创建一个Maven `pom.xml`文件作为开始。该`pom.xml`是用来构建项目的处方。打开你最喜欢的文本编辑器，然后添加以下内容：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>myproject</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.3.0.BUILD-SNAPSHOT</version>
    </parent>

    <!-- Additional lines to be added here... -->

    <!-- (you don't need this if you are using a .RELEASE version) -->
    <repositories>
        <repository>
            <id>spring-snapshots</id>
            <url>http://repo.spring.io/snapshot</url>
            <snapshots><enabled>true</enabled></snapshots>
        </repository>
        <repository>
            <id>spring-milestones</id>
            <url>http://repo.spring.io/milestone</url>
        </repository>
    </repositories>
    <pluginRepositories>
        <pluginRepository>
            <id>spring-snapshots</id>
            <url>http://repo.spring.io/snapshot</url>
        </pluginRepository>
        <pluginRepository>
            <id>spring-milestones</id>
            <url>http://repo.spring.io/milestone</url>
        </pluginRepository>
    </pluginRepositories>
</project>
```
这会给你一个可运转的构建，你可以通过运行`mvn package`测试它（现在你可以忽略"jar将是空的-没有包含任何内容！"的警告）。

**注**：目前你可以将该项目导入一个IDE（大多数现代的Java IDE都包含对Maven的内建支持）。简单起见，我们将继续使用普通的文本编辑器完成该示例。

* 添加classpath依赖

Spring Boot提供很多"Starter POMs"，这能够让你轻松的将jars添加到你的classpath下。我们的示例程序已经在POM的`partent`节点使用了`spring-boot-starter-parent`。`spring-boot-starter-parent`是一个特殊的starter，它提供了有用的Maven默认设置。同时，它也提供了一个`dependency-management`节点，这样对于”blessed“依赖你可以省略`version`标记。

其他的”Starter POMs“简单的提供依赖，这些依赖可能是你开发特定类型的应用时需要的。由于正在开发一个web应用，我们将添加一个`spring-boot-starter-web`依赖-但在此之前，让我们看下目前所拥有的：
```shell
$ mvn dependency:tree
[INFO] com.example:myproject:jar:0.0.1-SNAPSHOT
```
`mvn dependency:tree`命令以树形表示来打印你的项目依赖。你可以看到`spring-boot-starter-parent`本身并没有提供依赖。编辑我们的`pom.xml`，并在`parent`节点下添加`spring-boot-starter-web`依赖：
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```
如果再次运行`mvn dependency:tree`，你将看到现在有了一些其他依赖，包括Tomcat web服务器和Spring Boot自身。

* 编写代码

为了完成应用程序，我们需要创建一个单独的Java文件。Maven默认会编译`src/main/java`下的源码，所以你需要创建那样的文件结构，然后添加一个名为`src/main/java/Example.java`的文件：
```java
import org.springframework.boot.*;
import org.springframework.boot.autoconfigure.*;
import org.springframework.stereotype.*;
import org.springframework.web.bind.annotation.*;

@RestController
@EnableAutoConfiguration
public class Example {

    @RequestMapping("/")
    String home() {
        return "Hello World!";
    }

    public static void main(String[] args) throws Exception {
        SpringApplication.run(Example.class, args);
    }

}
```
尽管这里没有太多代码，但很多事情正在发生。让我们分步探讨重要的部分。

* @RestController和@RequestMapping注解

我们的`Example`类上使用的第一个注解是`@RestController`。这被称为一个构造型（stereotype）注解。它为阅读代码的人们提供建议。对于Spring，该类扮演了一个特殊角色。在本示例中，我们的类是一个web `@Controller`，所以当处理进来的web请求时，Spring会询问它。

`@RequestMapping`注解提供路由信息。它告诉Spring任何来自"/"路径的HTTP请求都应该被映射到`home`方法。`@RestController`注解告诉Spring以字符串的形式渲染结果，并直接返回给调用者。

**注**：`@RestController`和`@RequestMapping`注解是Spring MVC注解（它们不是Spring Boot的特定部分）。具体查看Spring参考文档的[MVC章节](http://docs.spring.io/spring/docs/4.1.5.RELEASE/spring-framework-reference/htmlsingle#mvc)。

* @EnableAutoConfiguration注解

第二个类级别的注解是`@EnableAutoConfiguration`。这个注解告诉Spring Boot根据添加的jar依赖猜测你想如何配置Spring。由于`spring-boot-starter-web`添加了Tomcat和Spring MVC，所以auto-configuration将假定你正在开发一个web应用并相应地对Spring进行设置。

**Starter POMs和Auto-Configuration**：设计auto-configuration的目的是更好的使用"Starter POMs"，但这两个概念没有直接的联系。你可以自由地挑选starter POMs以外的jar依赖，并且Spring Boot将仍旧尽最大努力去自动配置你的应用。

* main方法

我们的应用程序最后部分是`main`方法。这只是一个标准的方法，它遵循Java对于一个应用程序入口点的约定。我们的main方法通过调用`run`，将业务委托给了Spring Boot的`SpringApplication`类。`SpringApplication`将引导我们的应用，启动Spring，相应地启动被自动配置的Tomcat web服务器。我们需要将`Example.class`作为参数传递给`run`方法来告诉`SpringApplication`谁是主要的Spring组件。为了暴露任何的命令行参数，`args`数组也会被传递过去。

### 运行示例

到此我们的应用应该可以工作了。由于使用了`spring-boot-starter-parent` POM，这样我们就有了一个非常有用的`run`目标，我们可以用它启动程序。在项目根目录下输入`mvn spring-boot:run`来启动应用：
```shell
$ mvn spring-boot:run

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::  (v1.3.0.BUILD-SNAPSHOT)
....... . . .
....... . . . (log output here)
....... . . .
........ Started Example in 2.222 seconds (JVM running for 6.514)
```
如果使用一个浏览器打开[localhost:8080](http://localhost:8080)，你应该可以看到以下输出：
```shell
Hello World!
```
点击`ctrl-c`温雅地关闭应用程序。

### 创建一个可执行jar

让我们通过创建一个完全自包含的可执行jar文件来结束我们的示例，该jar文件可以在生产环境运行。可执行jars（有时候被成为胖jars "fat jars"）是包含你的编译后的类和你的代码运行所需的依赖jar的存档。

**可执行jars和Java**：Java没有提供任何标准的加载内嵌jar文件（即jar文件中还包含jar文件）的方法。如果你想发布一个自包含的应用这就是一个问题。为了解决该问题，很多开发者采用"共享的"jars。一个共享的jar简单地将来自所有jars的类打包进一个单独的“超级jar”。采用共享jar方式的问题是很难区分在你的应用程序中可以使用哪些库。在多个jars中如果存在相同的文件名（但内容不一样）也会是一个问题。Spring Boot采取一个[不同的途径](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#executable-jar)，并允许你真正的内嵌jars。

为了创建可执行的jar，需要将`spring-boot-maven-plugin`添加到我们的`pom.xml`中。在`dependencies`节点下插入以下内容：
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
**注**：`spring-boot-starter-parent` POM包含用于绑定`repackage`目标的`<executions>`配置。如果你不使用parent POM，你将需要自己声明该配置。具体参考[插件文档](http://docs.spring.io/spring-boot/docs/1.3.0.BUILD-SNAPSHOT/maven-plugin/usage.html)。

保存你的`pom.xml`，然后从命令行运行`mvn package`：
```shell
$ mvn package

[INFO] Scanning for projects...
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] Building myproject 0.0.1-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] .... ..
[INFO] --- maven-jar-plugin:2.4:jar (default-jar) @ myproject ---
[INFO] Building jar: /Users/developer/example/spring-boot-example/target/myproject-0.0.1-SNAPSHOT.jar
[INFO]
[INFO] --- spring-boot-maven-plugin:1.3.0.BUILD-SNAPSHOT:repackage (default) @ myproject ---
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
```
如果查看`target`目录，你应该看到`myproject-0.0.1-SNAPSHOT.jar`。该文件应该有10Mb左右的大小。如果想偷看内部结构，你可以运行`jar tvf`：
```shell
$ jar tvf target/myproject-0.0.1-SNAPSHOT.jar
```
在`target`目录下，你应该也能看到一个很小的名为`myproject-0.0.1-SNAPSHOT.jar.original`的文件。这是在Spring Boot重新打包前Maven创建的原始jar文件。

为了运行该应用程序，你可以使用`java -jar`命令：
```shell
$ java -jar target/myproject-0.0.1-SNAPSHOT.jar

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::  (v1.3.0.BUILD-SNAPSHOT)
....... . . .
....... . . . (log output here)
....... . . .
........ Started Example in 2.536 seconds (JVM running for 2.864)
```
和以前一样，点击`ctrl-c`来温柔地退出程序。
