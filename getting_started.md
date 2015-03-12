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

下列内嵌容器支持开箱即用：

|名称|Servlet版本|Java版本|
|--------|:-------|:-------|
|Tomcat 8|3.1|Java 7+|
|Tomcat 7|3.0|Java 6+|
|Jetty 9|3.1|Java 7+|
|Jetty 8|3.0|Java 6+|
|Undertow 1.1|3.1|Java 7+|

你也可以将Spring Boot应用部署到任何兼容Servlet 3.0+的容器。

### Spring Boot安装

* Spring Boot CLI安装
* 从Spring Boot早期版本升级
* 开发你的第一个Spring Boot应用
* 
