### B.1.1 Group属性

`groups`数组包含的JSON对象可以由以下属性组成：

|名称|类型|目的|
|----|:----:|:----:|
|name|String|group的全名，该属性是强制性的|
|type|String|group数据类型的类名。例如，如果group是基于一个被`@ConfigurationProperties`注解的类，该属性将包含该类的全限定名。如果基于一个`@Bean`方法，它将是该方法的返回类型。如果该类型未知，则该属性将被忽略|
