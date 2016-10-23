### 24.7.5 @ConfigurationProperties vs. @Value

`@Value`是Spring容器的一个核心特性，它没有提供跟type-safe Configuration Properties相同的特性。下面的表格总结了`@ConfigurationProperties`和`@Value`支持的特性：

|特性|`@ConfigurationProperties`|`@Value`|
|---|---|---|
|[Relaxed绑定](http://docs.spring.io/spring-boot/docs/1.4.1.RELEASE/reference/htmlsingle/#boot-features-external-config-relaxed-binding)|Yes|No|
|[Meta-data支持](http://docs.spring.io/spring-boot/docs/1.4.1.RELEASE/reference/htmlsingle/#configuration-metadata)|Yes|No|
|`SpEL`表达式|No|Yes|

如果你为自己的组件定义了一系列的配置keys，我们建议你将它们以`@ConfigurationProperties`注解的POJO进行分组。由于`@Value`不支持relaxed绑定，所以如果你使用环境变量提供属性值的话，它就不是很好的选择。最后，尽管`@Value`可以写`SpEL`表达式，但这些表达式不会处理来自[Application属性文件](http://docs.spring.io/spring-boot/docs/1.4.1.RELEASE/reference/htmlsingle/#boot-features-external-config-application-property-files)的属性。
