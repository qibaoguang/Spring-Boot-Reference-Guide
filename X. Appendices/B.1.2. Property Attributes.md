### B.1.2. Property属性

`properties`数组中包含的JSON对象可由以下属性构成：

|名称|类型|目的|
|----|:----|:----|
|name|String|property的全名，格式为小写虚线分割的形式（比如`server.servlet-path`）。该属性是强制性的|
|type|String|property数据类型的类名。例如`java.lang.String`。该属性可以用来指导用户他们可以输入值的类型。为了保持一致，原生类型使用它们的包装类代替，比如`boolean`变成了`java.lang.Boolean`。注意，这个类可能是个从一个字符串转换而来的复杂类型。如果类型未知则该属性会被忽略|
|description|String|一个简短的组的描述，用于展示给用户。如果没有描述可用则该属性会被忽略。推荐使用一个简短的段落描述，开头提供一个简洁的总结，最后一行以句号结束|
|sourceType|String|贡献property的来源类名。例如，如果property来自一个被`@ConfigurationProperties`注解的类，该属性将包括该类的全限定名。如果来源类型未知则该属性会被忽略|
|defaultValue|Object|当property没有定义时使用的默认值。如果property类型是个数组则该属性也可以是个数组。如果默认值未知则该属性会被忽略|
|deprecated|boolean|指定该property是否过期。如果该字段没有过期或该信息未知则该属性会被忽略|
