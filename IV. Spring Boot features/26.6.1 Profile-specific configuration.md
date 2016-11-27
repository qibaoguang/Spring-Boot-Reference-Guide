### 26.6.1 Profile-specific配置
`<springProfile>`标签可用于根据激活的Spring profiles，选择性的包含或排除配置片段。Profile片段可以放在`<configuration>`元素内的任何地方，使用`name`属性定义哪些profile接受该配置，多个profiles以逗号分隔。
```xml
<springProfile name="staging">
    <!-- configuration to be enabled when the "staging" profile is active -->
</springProfile>

<springProfile name="dev, staging">
    <!-- configuration to be enabled when the "dev" or "staging" profiles are active -->
</springProfile>

<springProfile name="!production">
    <!-- configuration to be enabled when the "production" profile is not active -->
</springProfile>
```
