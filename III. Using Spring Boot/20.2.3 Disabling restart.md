###20.2.3 禁用重启

如果不想使用重启特性，你可以通过`spring.devtools.restart.enabled`属性来禁用它，通常情况下可以在`application.properties`文件中设置（依旧会初始化重启类加载器，但它不会监控文件变化）。

如果需要彻底禁用重启支持，比如，不能跟某个特殊库一块工作，你需要在调用`SpringApplication.run(…​)`之前设置一个系统属性，如下：
```java
public static void main(String[] args) {
    System.setProperty("spring.devtools.restart.enabled", "false");
    SpringApplication.run(MyApp.class, args);
}
```
