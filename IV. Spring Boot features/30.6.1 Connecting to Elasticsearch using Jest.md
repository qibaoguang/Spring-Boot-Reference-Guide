### 30.6.1 使用Jest连接Elasticsearch
如果添加`Jest`依赖，你可以注入一个自动配置的`JestClient`，默认目标为`http://localhost:9200/`，也可以进一步配置该客户端：
```properties
spring.elasticsearch.jest.uris=http://search.example.com:9200
spring.elasticsearch.jest.read-timeout=10000
spring.elasticsearch.jest.username=user
spring.elasticsearch.jest.password=secret
```
定义一个`JestClient` bean以完全控制注册过程。
