### 附录B.1. 元数据格式

配置元数据位于jars文件中的`META-INF/spring-configuration-metadata.json`，它们使用一个具有"groups"或"properties"分类节点的简单JSON格式：
```json
{"groups": [
    {
        "name": "server",
        "type": "org.springframework.boot.autoconfigure.web.ServerProperties",
        "sourceType": "org.springframework.boot.autoconfigure.web.ServerProperties"
    }
    ...
],"properties": [
    {
        "name": "server.port",
        "type": "java.lang.Integer",
        "sourceType": "org.springframework.boot.autoconfigure.web.ServerProperties"
    },
    {
        "name": "server.servlet-path",
        "type": "java.lang.String",
        "sourceType": "org.springframework.boot.autoconfigure.web.ServerProperties"
        "defaultValue": "/"
    }
    ...
]}
```
每个"property"是一个配置节点，用户可以使用特定的值指定它。例如，`server.port`和`server.servlet-path`可能在`application.properties`中如以下定义：
```properties
server.port=9090
server.servlet-path=/home
```
"groups"是高级别的节点，它们本身不指定一个值，但为properties提供一个有上下文关联的分组。例如，`server.port`和`server.servlet-path`属性是`server`组的一部分。

**注**：不需要每个"property"都有一个"group"，一些属性可以以自己的形式存在。







