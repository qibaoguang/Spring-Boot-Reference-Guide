###31.1.5 Infinispan
Infinispan没有默认的配置文件位置，所以需要显式指定：
```properties
spring.cache.infinispan.config=infinispan.xml
```
通过设置`spring.cache.cache-names`属性可以让缓存在启动时就被创建，如果定义了`ConfigurationBuilder` bean，它将用来定义该实例。
