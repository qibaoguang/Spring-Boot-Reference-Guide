###20.2.1 排除资源

某些资源的变化没必要触发重启，比如Thymeleaf模板可以随时编辑。默认情况下，位于`/META-INF/maven`，`/META-INF/resources`，`/resources`，`/static`，`/public`或`/templates`下的资源变更不会触发重启，但会触发实时加载（live reload）。你可以使用`spring.devtools.restart.exclude`属性自定义这些排除规则，比如，为了只排除`/static`和`/public`，你可以这样设置：
```properties
spring.devtools.restart.exclude=static/**,public/**
```

**注** 如果你想保留默认属性，并添加其他的排除规则，可以使用`spring.devtools.restart.additional-exclude`属性作为代替。
