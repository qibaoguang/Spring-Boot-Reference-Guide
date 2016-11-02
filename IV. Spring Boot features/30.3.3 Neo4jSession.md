### 30.3.3 Neo4jSession
Neo4jSession默认的生命周期是应用程序范围，如果运行的是web应用，你可以很轻松的改变它的scope：
```properties
spring.data.neo4j.session.scope=session
```
