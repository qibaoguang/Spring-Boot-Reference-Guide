### B.1.2. Property属性

`properties`数组中包含的JSON对象可由以下属性构成：

|名称|类型|目的|
|----|:----|:----|
|name|String|property的全名，格式为小写虚线分割的形式（比如`server.servlet-path`）。该属性是强制性的|
|type|String|property数据类型的类名。例如`java.lang.String`。该属性可以用来指导用户他们可以输入值的类型。为了保持一致，原生类型使用它们的包装类代替，比如`boolean`变成了`java.lang.Boolean`。|
