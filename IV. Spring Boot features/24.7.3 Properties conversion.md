### 24.7.3 属性转换

将外部应用配置绑定到`@ConfigurationProperties` beans时，Spring会尝试将属性强制转换为正确的类型。如果需要自定义类型转换器，你可以提供一个`ConversionService` bean（bean id为`conversionService`），或自定义属性编辑器（通过`CustomEditorConfigurer` bean），或自定义`Converters`（bean定义时需要注解`@ConfigurationPropertiesBinding`）。

**注** 由于该bean在应用程序生命周期的早期就需要使用，所以确保限制你的`ConversionService`使用的依赖。通常，在创建时期任何你需要的依赖可能都没完全初始化。
