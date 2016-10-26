###27.1.3 自定义JSON序列化器和反序列化器

如果使用Jackson序列化，反序列化JSON数据，你可能想编写自己的`JsonSerializer`和`JsonDeserializer`类。自定义序列化器（serializers）通常[通过Module注册到Jackson](http://wiki.fasterxml.com/JacksonHowToCustomDeserializers)，但Spring Boot提供了`@JsonComponent`注解这一替代方式，它能轻松的将序列化器注册为Spring Beans。
