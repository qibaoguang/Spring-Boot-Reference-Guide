### 附录B.1.1 Group属性

`groups`数组包含的JSON对象可以由以下属性组成：

|名称|类型|目的|
|----|:----|:----|
|name|String|group的全名，该属性是强制性的|
|type|String|group数据类型的类名。例如，如果group是基于一个被`@ConfigurationProperties`注解的类，该属性将包含该类的全限定名。如果基于一个`@Bean`方法，它将是该方法的返回类型。如果该类型未知，则该属性将被忽略|
|description|String|一个简短的group描述，用于展示给用户。如果没有可用描述，该属性将被忽略。推荐使用一个简短的段落描述，第一行提供一个简洁的总结，最后一行以句号结尾|
|sourceType|String|贡献该组的来源类名。例如，如果组基于一个被`@ConfigurationProperties`注解的`@Bean`方法，该属性将包含`@Configuration`类的全限定名，该类包含此方法。如果来源类型未知，则该属性将被忽略|
|sourceMethod|String|贡献该组的方法的全名（包含括号及参数类型）。例如，被`@ConfigurationProperties`注解的`@Bean`方法名。如果源方法未知，该属性将被忽略|
