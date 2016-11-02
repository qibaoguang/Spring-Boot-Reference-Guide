### 30.3.2 使用内嵌模式

**注** Neo4j的内嵌模式从属于不同的许可，在将它集成到应用之前确保复查下。

如果将`org.neo4j:neo4j-ogm-embedded-driver`依赖添加到应用中，Spring Boot会自动配置一个进程内（in-process）的内嵌Neo4j实例，当应用关闭时，该实例不会持久化任何数据。设置`spring.data.neo4j.embedded.enabled=false`可显式关闭该模式，你也可以启用内嵌模式的持久化特性：
```properties
spring.data.neo4j.uri=file://var/tmp/graph.db
```
