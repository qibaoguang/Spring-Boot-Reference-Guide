###31.1.6 Couchbase
如果Couchbase可用，并配置好了，`CouchbaseCacheManager`将会自动配置，使用`spring.cache.cache-names`属性可以在启动时创建其他缓存。对`Bucket`的操作也是自动配置的，你可以使用customizer在另一个`Bucket`上创建其他缓存：假设你需要在“main” `Bucket`上存放两个缓存（`foo`和`bar`），在另一个`Bucket`上存放一个存活时间为2秒的`biz`缓存。首先，你通过配置创建两个缓存：
```properties
spring.cache.cache-names=foo,bar
```
然后定义其他`@Configuration`来配置另一个`Bucket`和`biz`缓存：
```java
@Configuration
public class CouchbaseCacheConfiguration {

    private final Cluster cluster;

    public CouchbaseCacheConfiguration(Cluster cluster) {
        this.cluster = cluster;
    }

    @Bean
    public Bucket anotherBucket() {
        return this.cluster.openBucket("another", "secret");
    }

    @Bean
    public CacheManagerCustomizer<CouchbaseCacheManager> cacheManagerCustomizer() {
        return c -> {
            c.prepareCache("biz", CacheBuilder.newInstance(anotherBucket())
                    .withExpirationInMillis(2000));
        };
    }

}
```
这个示例配置重用了通过自动配置的`Cluster`。
