### 29.5.1 代码生成
为了使用jOOQ类型安全的查询，你需要从数据库schema生成Java类，具体可参考[jOOQ用户指南](http://www.jooq.org/doc/3.6/manual-single-page/#jooq-in-7-steps-step3)。如果正在使用`jooq-codegen-maven`插件（也使用`spring-boot-starter-parent` “parent POM”），你可以安全的省略插件的`<version>`标签，也可以使用Spring Boot定义的版本变量（比如`h2.version`）来声明插件的数据库依赖，示例如下：
```xml
<plugin>
    <groupId>org.jooq</groupId>
    <artifactId>jooq-codegen-maven</artifactId>
    <executions>
        ...
    </executions>
    <dependencies>
        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <version>${h2.version}</version>
        </dependency>
    </dependencies>
    <configuration>
        <jdbc>
            <driver>org.h2.Driver</driver>
            <url>jdbc:h2:~/yourdatabase</url>
        </jdbc>
        <generator>
            ...
        </generator>
    </configuration>
</plugin>
```
