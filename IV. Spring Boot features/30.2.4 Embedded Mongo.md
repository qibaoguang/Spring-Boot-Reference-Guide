### 30.2.4 内嵌的Mongo
Spring Boot为[内嵌Mongo](https://github.com/flapdoodle-oss/de.flapdoodle.embed.mongo)提供自动配置，你需要添加`de.flapdoodle.embed:de.flapdoodle.embed.mongo`依赖才能使用它。

`spring.data.mongodb.port`属性可用来配置Mongo监听的端口，将该属性值设为0，表示使用一个随机分配的可用端口。通过`MongoAutoConfiguration`创建的`MongoClient`将自动配置为使用随机分配的端口。

如果classpath下存在SLF4J依赖，Mongo产生的输出将自动路由到一个名为`org.springframework.boot.autoconfigure.mongo.embedded.EmbeddedMongo`的logger。

想要完全控制Mongo实例的配置和日志路由，你可以声明自己的`IMongodConfig`和`IRuntimeConfig` beans。
