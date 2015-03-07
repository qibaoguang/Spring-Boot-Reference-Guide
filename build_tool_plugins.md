### 构建工具插件

Spring Boot为Maven和Gradle提供构建工具插件。该插件提供各种各样的特性，包括打包可执行jars。本节提供关于插件的更多详情及用于扩展一个不支持的构建系统所需的帮助信息。如果你是刚刚开始，那可能需要先阅读[Part III, “Using Spring Boot”](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#using-boot)章节的[“Chapter 13, Build systems”](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#using-boot-build-systems)。

### Spring Boot Maven插件

[Spring Boot Maven插件](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#using-boot-build-systems)为Maven提供Spring Boot支持，它允许你打包可执行jar或war存档，然后就地运行应用。为了使用它，你需要使用Maven 3.2 （或更高版本）。

**注**：参考[Spring Boot Maven Plugin Site](http://docs.spring.io/spring-boot/docs/1.3.0.BUILD-SNAPSHOT/maven-plugin/)可以获取全部的插件文档。

* 包含该插件

想要使用Spring Boot Maven插件只需简单地在你的pom.xml的`plugins`部分包含相应的XML：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <!-- ... -->
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <version>1.3.0.BUILD-SNAPSHOT</version>
                <executions>
                    <execution>
                        <goals>
                            <goal>repackage</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project>
```
该配置会在Maven生命周期的`package`阶段重新打包一个jar或war。下面的示例显示在`target`目录下既有重新打包后的jar，也有原始的jar：
```shell
$ mvn package
$ ls target/*.jar
target/myproject-1.0.0.jar target/myproject-1.0.0.jar.original
```
如果不包含像上面那样的`<execution/>`，你可以自己运行该插件（但只有在package目标也被使用的情况）。例如：
```shell
$ mvn package spring-boot:repackage
$ ls target/*.jar
target/myproject-1.0.0.jar target/myproject-1.0.0.jar.original
```
如果使用一个里程碑或快照版本，你还需要添加正确的pluginRepository元素：
```xml
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
```
* 打包可执行jar和war文件

一旦`spring-boot-maven-plugin`被包含到你的pom.xml中，它就会自动尝试使用`spring-boot:repackage`目标重写存档以使它们能够执行。为了构建一个jar或war，你应该使用常规的packaging元素配置你的项目：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <!-- ... -->
    <packaging>jar</packaging>
    <!-- ... -->
</project>
```
生成的存档在`package`阶段会被Spring Boot增强。你想启动的main类即可以通过指定一个配置选项，也可以通过为manifest添加一个`Main-Class`属性这种常规的方式实现。如果你没有指定一个main类，该插件会搜索带有`public static void main(String[] args)`方法的类。

为了构建和运行一个项目的artifact，你可以输入以下命令：
```shell
$ mvn package
$ java -jar target/mymodule-0.0.1-SNAPSHOT.jar
```
为了构建一个即是可执行的，又能部署到一个外部容器的war文件，你需要标记内嵌容器依赖为"provided"，例如：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <!-- ... -->
    <packaging>war</packaging>
    <!-- ... -->
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
            <scope>provided</scope>
        </dependency>
        <!-- ... -->
    </dependencies>
</project>
```
**注**：具体参考[“Section 74.1, “Create a deployable war file”” ](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#howto-create-a-deployable-war-file)章节。 

在[插件信息页面](http://docs.spring.io/spring-boot/docs/1.3.0.BUILD-SNAPSHOT/maven-plugin/)有高级的配置选项和示例。

### Spring Boot Gradle插件

Spring Boot Gradle插件为Gradle提供Spring Boot支持，它允许你打包可执行jar或war存档，运行Spring Boot应用，对于"神圣的"依赖可以在你的build.gradle文件中省略版本信息。

* 包含该插件

想要使用Spring Boot Gradle插件，你只需简单的包含一个`buildscript`依赖，并应用`spring-boot`插件：
```shell
buildscript {
    dependencies {
        classpath("org.springframework.boot:spring-boot-gradle-plugin:1.3.0.BUILD-SNAPSHOT")
    }
}
apply plugin: 'spring-boot'
```
如果想使用一个里程碑或快照版本，你可以添加相应的repositories引用：
```shell
buildscript {
    repositories {
        maven.url "http://repo.spring.io/snapshot"
        maven.url "http://repo.spring.io/milestone"
    }
    // ...
}
```
* 声明不带版本的依赖






