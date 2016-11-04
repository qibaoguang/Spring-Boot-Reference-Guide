###31.1.4 Hazelcast
Spring Boot为Hazelcast提供[通常的支持](http://docs.spring.io/spring-boot/docs/1.4.1.RELEASE/reference/htmlsingle/#boot-features-hazelcast)，如果`HazelcastInstance`被自动配置，那它将自动包装进一个`CacheManager`。

如果出于某些原因，需要使用另一个不同的`HazelcastInstance`，你可以请求Spring Boot创建一个单独的实例，并只用于该`CacheManager`：
```properties
spring.cache.hazelcast.config=classpath:config/my-cache-hazelcast.xml
```
**注** 如果以这种方式创建一个单独的`HazelcastInstance`，它将不会注册到应用上下文中。
