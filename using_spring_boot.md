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





