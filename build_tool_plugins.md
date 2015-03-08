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

`spring-boot`插件会为你的构建注册一个自定义的Gradle `ResolutionStrategy`，它允许你在声明对"神圣"的artifacts的依赖时获取版本号。为了充分使用该功能，只需要想通常那样声明依赖，但将版本号设置为空：
```java
dependencies {
    compile("org.springframework.boot:spring-boot-starter-web")
    compile("org.thymeleaf:thymeleaf-spring4")
    compile("nz.net.ultraq.thymeleaf:thymeleaf-layout-dialect")
}
```
**注**：你声明的`spring-boot` Gradle插件的版本决定了"blessed"依赖的实际版本（确保可以重复构建）。你最好总是将`spring-boot` gradle插件版本设置为你想用的Spring Boot实际版本。提供的版本详细信息可以在[附录](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#appendix-dependency-versions)中找到。

`spring-boot`插件对于没有指定版本的依赖只会提供一个版本。如果不想使用插件提供的版本，你可以像平常那样在声明依赖的时候指定版本。例如：
```java
dependencies {
    compile("org.thymeleaf:thymeleaf-spring4:2.1.1.RELEASE")
}
```
* 自定义版本管理

如果你需要不同于Spring Boot的"blessed"依赖，有可能的话可以自定义`ResolutionStrategy`使用的版本。替代的版本元数据使用`versionManagement`配置。例如：
```java
dependencies {
    versionManagement("com.mycorp:mycorp-versions:1.0.0.RELEASE@properties")
    compile("org.springframework.data:spring-data-hadoop")
}
```
版本信息需要作为一个`.properties`文件发布到一个仓库中。对于上面的示例，`mycorp-versions.properties`文件可能包含以下内容：
```java
org.springframework.data\:spring-data-hadoop=2.0.0.RELEASE
```
属性文件优先于Spring Boot默认设置，如果有必要的话可以覆盖版本号。

* 默认排除规则

Gradle处理"exclude rules"的方式和Maven稍微有些不同，在使用starter POMs时这可能会引起无法预料的结果。特别地，当一个依赖可以通过不同的路径访问时，对该依赖声明的exclusions将不会生效。例如，如果一个starter POM声明以下内容：
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <version>4.0.5.RELEASE</version>
        <exclusions>
            <exclusion>
                <groupId>commons-logging</groupId>
                <artifactId>commons-logging</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>4.0.5.RELEASE</version>
    </dependency>
</dependencies>
```
`commons-logging` jar不会被Gradle排除，因为通过没有`exclusion`元素的`spring-context`可以传递性的拉取到它（spring-context → spring-core → commons-logging）。

为了确保正确的排除被实际应用，Spring Boot Gradle插件将自动添加排除规则。所有排除被定义在`spring-boot-dependencies` POM，并且针对"starter" POMs的隐式规则也会被添加。

如果不想自动应用排除规则，你可以使用以下配置：
```java
springBoot {
    applyExcludeRules=false
}
```
* 打包可执行jar和war文件

一旦`spring-boot`插件被应用到你的项目，它将使用`bootRepackage`任务自动尝试重写存档以使它们能够执行。为了构建一个jar或war，你需要按通常的方式配置项目。

你想启动的main类既可以通过一个配置选项指定，也可以通过向manifest添加一个`Main-Class`属性。如果你没有指定main类，该插件会搜索带有`public static void main(String[] args)`方法的类。

为了构建和运行一个项目artifact，你可以输入以下内容：
```shell
$ gradle build
$ java -jar build/libs/mymodule-0.0.1-SNAPSHOT.jar
```
为了构建一个即能执行也可以部署到外部容器的war包，你需要将内嵌容器依赖标记为"providedRuntime"，比如：
```java
...
apply plugin: 'war'

war {
    baseName = 'myapp'
    version =  '0.5.0'
}

repositories {
    jcenter()
    maven { url "http://repo.spring.io/libs-snapshot" }
}

configurations {
    providedRuntime
}

dependencies {
    compile("org.springframework.boot:spring-boot-starter-web")
    providedRuntime("org.springframework.boot:spring-boot-starter-tomcat")
    ...
}
```
**注**：具体参考[“Section 74.1, “Create a deployable war file””](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#howto-create-a-deployable-war-file)。

* 就地（in-place）运行项目

为了在不先构建jar的情况下运行项目，你可以使用"bootRun"任务：
```shell
$ gradle bootRun
```
默认情况下，以这种方式运行项目可以让你的静态classpath资源（比如，默认位于`src/main/resources`下）在应用运行期间被重新加载。使静态资源可以重新加载意味着`bootRun`任务不会使用`processResources`任务的输出，比如，当调用`bootRun`时，你的应用将以资源未处理的形式来使用它们。

你可以禁止直接使用静态classpath资源。这意味着资源不再是可重新加载的，但`processResources`任务的输出将会被使用。想要这样做，只需将`bootRun`任务的`addResources`设为false：
```java
bootRun {
    addResources = false
}
```
* Spring Boot插件配置

Gradle插件自动扩展你的构建脚本DSL，它为脚本添加一个`springBoot`元素以此作为Boot插件的全局配置。你可以像配置其他Gradle扩展那样为`springBoot`设置相应的属性（下面有配置选项列表）。
```gradle
springBoot {
    backupSource = false
}
```

* Repackage配置
* 




* 
