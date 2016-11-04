###30.8.1 连接Couchbase
通过添加Couchbase SDK和一些配置，你可以很容易获取一个`Bucket`和`Cluster`，`spring.couchbase.*`属性可用于自定义该连接。通常，你需要提供启动hosts，bucket name和password：
```properties
spring.couchbase.bootstrap-hosts=my-host-1,192.168.1.123
spring.couchbase.bucket.name=my-bucket
spring.couchbase.bucket.password=secret
```
**注** 你至少需要提供启动host(s)，在这种情况下，bucket name默认为`default`，password默认为空字符串。另外，你可以定义自己的`org.springframework.data.couchbase.config.CouchbaseConfigurer` `@Bean`来把控所有配置。

你也可以自定义一些`CouchbaseEnvironment`设置，例如，以下配置改变打开新`Bucket`的超时时间（timeout），还启用了SSL支持：
```properties
spring.couchbase.env.timeouts.connect=3000
spring.couchbase.env.ssl.key-store=/location/of/keystore.jks
spring.couchbase.env.ssl.key-store-password=secret
```
具体查看`spring.couchbase.env.*`属性。
