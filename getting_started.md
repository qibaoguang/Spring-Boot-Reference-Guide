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

### 从Spring Boot早期版本升级


### 开发你的第一个Spring Boot应用
 
