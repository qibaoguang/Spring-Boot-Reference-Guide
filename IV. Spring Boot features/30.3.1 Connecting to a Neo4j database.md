### 30.3.1 连接Neo4j数据库
你可以注入一个自动配置的`Neo4jSession`，`Session`，或`Neo4jOperations`实例，就像使用其他Spring Bean那样。该实例默认使用`localhost:7474`连接Neo4j服务器：
```java
@Component
public class MyBean {

    private final Neo4jTemplate neo4jTemplate;

    @Autowired
    public MyBean(Neo4jTemplate neo4jTemplate) {
        this.neo4jTemplate = neo4jTemplate;
    }

    // ...

}
```
添加自己的`org.neo4j.ogm.config.Configuration` `@Bean`，你就能完全控制该配置了。同时，添加一个`Neo4jOperations`类型的`@Bean`可以禁用自动配置。

通过`spring.data.neo4j.*`属性可以配置使用的用户和凭证：
```properties
spring.data.neo4j.uri=http://my-server:7474
spring.data.neo4j.username=neo4j
spring.data.neo4j.password=secret
```
