### 附录 B.2.1. 内嵌属性

该注解处理器自动将内部类当做内嵌属性处理。例如，下面的类：
```java
@ConfigurationProperties(prefix="server")
public class ServerProperties {

    private String name;

    private Host host;

    // ... getter and setters

    private static class Host {

        private String ip;

        private int port;

        // ... getter and setters

    }

}
```
