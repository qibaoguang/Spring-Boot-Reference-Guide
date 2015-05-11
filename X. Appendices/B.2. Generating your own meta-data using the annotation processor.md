### 附录B.2. 使用注解处理器产生自己的元数据

通过使用`spring-boot-configuration-processor` jar， 你可以从被`@ConfigurationProperties`注解的节点轻松的产生自己的配置元数据文件。该jar包含一个在你的项目编译时会被调用的Java注解处理器。想要使用该处理器，你只需简单添加`spring-boot-configuration-processor`依赖，例如使用Maven你需要添加：
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

该处理器会处理被`@ConfigurationProperties`注解的类和方法，description属性用于产生配置类字段值的Javadoc说明。

**注**：你应该使用简单的文本来设置`@ConfigurationProperties`字段的Javadoc，因为在没有被添加到JSON之前它们是不被处理的。


属性是通过判断是否存在标准的getters和setters来发现的，对于集合类型有特殊处理（即使只出现一个getter）。该注解处理器也支持使用lombok的`@Data`, `@Getter`和`@Setter`注解。


