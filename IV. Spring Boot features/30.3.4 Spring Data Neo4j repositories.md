### 30.3.4 Spring Data Neo4j仓库
Spring Data包含的仓库也支持Neo4j，实际上，Spring Data JPA和Spring Data Neo4j使用相同的常用设施，所以你可以采用先前JPA的示例，假设`City`现在是一个Neo4j OGM `@NodeEntity`而不是JPA `@Entity`，它将以同样的方式工作。

**注** 你可以使用`@EntityScan`注解定义实体扫描路径。

将以下两个注解添加到你的Spring configuration，可以启用repository支持（还有可选的对`@Transactional`的支持）：
```java
@EnableNeo4jRepositories(basePackages = "com.example.myapp.repository")
@EnableTransactionManagement
```
