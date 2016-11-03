### 30.6.2 使用Spring Data连接Elasticsearch
你可以注入一个自动配置的`ElasticsearchTemplate`或Elasticsearch `Client`实例，就想其他Spring Bean那样。该实例默认内嵌一个本地，内存型服务器（在Elasticsearch中被称为`Node`），并使用当前工作目录作为服务器的home目录。在这个步骤中，首先要做的是告诉Elasticsearch将文件存放到什么地方：
```properties
spring.data.elasticsearch.properties.path.home=/foo/bar
```
另外，你可以通过设置`spring.data.elasticsearch.cluster-nodes`（逗号分隔的‘host:port’列表）来切换为远程服务器：
```properties
spring.data.elasticsearch.cluster-nodes=localhost:9300
```
```java
@Component
public class MyBean {

    private ElasticsearchTemplate template;

    @Autowired
    public MyBean(ElasticsearchTemplate template) {
        this.template = template;
    }

    // ...

}
```
如果添加自己的`ElasticsearchTemplate`类型的`@Bean`，它将覆盖默认实例。
