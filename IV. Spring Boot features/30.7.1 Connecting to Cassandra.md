### 30.7.1 连接Cassandra
你可以注入一个自动配置的`CassandraTemplate`或Cassandra `Session`实例，就像注入其他Spring Bean那样。`spring.data.cassandra.*`属性可用来自定义该连接，通常你需要提供`keyspace-name`和`contact-points`属性：
```properties
spring.data.cassandra.keyspace-name=mykeyspace
spring.data.cassandra.contact-points=cassandrahost1,cassandrahost2
```
```java
@Component
public class MyBean {

    private CassandraTemplate template;

    @Autowired
    public MyBean(CassandraTemplate template) {
        this.template = template;
    }

    // ...

}
```
如果添加自己的`CassandraTemplate`类型的`@Bean`，它将替换默认实例。
