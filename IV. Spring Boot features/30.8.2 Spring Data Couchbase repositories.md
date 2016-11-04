###30.8.2 Spring Data Couchbase仓库
Spring Data包含的仓库也支持Couchbase，具体可查看Spring Data Couchbase的[参考文档](http://docs.spring.io/spring-data/couchbase/docs/current/reference/html/)。

你可以注入一个自动配置的`CouchbaseTemplate`实例，就像注入其他Spring Bean那样，只要默认的`CouchbaseConfigurer`可以使用。如果想关闭Spring Data Couchbase的自动配置，你可以提供自己的`org.springframework.data.couchbase.config.AbstractCouchbaseDataConfiguration`实现。
```java
@Component
public class MyBean {

    private final CouchbaseTemplate template;

    @Autowired
    public MyBean(CouchbaseTemplate template) {
        this.template = template;
    }

    // ...

}
```
如果添加你自己的`CouchbaseTemplate`类型的`@Bean`，且名称为`couchbaseTemplate`，那它将替换默认实例。
