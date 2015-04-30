### 附录B.2 使用注解处理器产生自己的元数据

通过使用`spring-boot-configuration-processor` jar, 你可以从被`@ConfigurationProperties`注解的节点轻松的产生自己的配置元数据文件。该jar包含一个在你的项目编译时会被调用的Java注解处理器。想要使用该处理器，你只需简单添加`spring-boot-configuration-processor`依赖，例如使用Maven你需要添加：
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
```
使用Gradle时，你可以使用[propdeps-plugin](https://github.com/spring-projects/gradle-plugins/tree/master/propdeps-plugin)并指定：
```gradle
dependencies {
		optional "org.springframework.boot:spring-boot-configuration-processor"
	}

	compileJava.dependsOn(processResources)
}
```
**注**：你需要将`compileJava.dependsOn(processResources)`添加到构建中，以确保资源在代码编译之前处理。如果没有该指令，任何`additional-spring-configuration-metadata.json`文件都不会被处理。
