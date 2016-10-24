### 26.6.2 Environment属性
`<springProperty>`标签允许你从Spring `Environment`读取属性，以便在Logback中使用。如果你想在logback配置获取`application.properties`中的属性值，该功能就很有用。该标签工作方式跟Logback标准`<property>`标签类似，但不是直接指定`value`值，你需要定义属性的`source`（来自`Environment`），也可以指定存储属性作用域的`scope`。如果`Environment`没有相应属性，你可以通过`defaultValue`设置默认值。
```xml
<springProperty scope="context" name="fluentHost" source="myapp.fluentd.host"
        defaultValue="localhost"/>
<appender name="FLUENT" class="ch.qos.logback.more.appenders.DataFluentAppender">
    <remoteHost>${fluentHost}</remoteHost>
    ...
</appender>
```
**注** `RelaxedPropertyResolver`用于获取`Environment`属性，如果以中划线的方式指定`source`（`my-property-name`），则所有relaxed变体都会进行尝试（`myPropertyName`，`MY_PROPERTY_NAME `等）。
