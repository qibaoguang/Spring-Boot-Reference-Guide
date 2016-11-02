### 29.5.3 自定义jOOQ
通过在`application.properties`中设置`spring.jooq.sql-dialect`属性，你可以自定义jOOQ使用的SQL方言（dialect）。例如，设置方言为Postgres：
```properties
spring.jooq.sql-dialect=Postgres
```
定义自己的`@Bean`，在jOOQ`Configuration`创建时使用，可以实现更高级的定制。你可以为以下jOOQ类型定义beans：

* `ConnectionProvider`
* `TransactionProvider`
* `RecordMapperProvider`
* `RecordListenerProvider`
* `ExecuteListenerProvider`
* `VisitListenerProvider`

如果想全面控制jOOQ配置，你甚至可以创建自己的`org.jooq.Configuration` `@Bean`。
