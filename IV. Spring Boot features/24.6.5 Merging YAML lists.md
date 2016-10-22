###24.6.5 合并YAML列表

正如[上面](http://docs.spring.io/spring-boot/docs/1.4.1.RELEASE/reference/htmlsingle/#boot-features-external-config-loading-yaml)看到的，所有YAML最终都转换为properties，在通过一个profile覆盖"list"属性时这个过程可能不够直观（counter intuitive）。例如，假设有一个`MyPojo`对象，默认它的`name`和`description`属性都为`null`，下面我们将从`FooProperties`暴露一个`MyPojo`对象列表（list）：
```java
@ConfigurationProperties("foo")
public class FooProperties {

    private final List<MyPojo> list = new ArrayList<>();

    public List<MyPojo> getList() {
        return this.list;
    }

}
```
考虑如下配置：
```properties
foo:
  list:
    - name: my name
      description: my description
---
spring:
  profiles: dev
foo:
  list:
    - name: my another name
```
如果`dev` profile没有激活，`FooProperties.list`将包括一个如上述定义的`MyPojo`实体，即使`dev`生效，该`list`仍旧只包含一个实体（`name`值为`my another name`，`description`值为`null`）。此配置不会向该列表添加第二个`MyPojo`实例，也不会对该项进行合并。

当一个集合定义在多个profiles时，只使用优先级最高的：
```properties
foo:
  list:
    - name: my name
      description: my description
    - name: another name
      description: another description
---
spring:
  profiles: dev
foo:
  list:
     - name: my another name
```
在以上示例中，如果`dev` profile激活，`FooProperties.list`将包含一个`MyPojo`实体（`name`值为`my another name`，`description`值为`null`）。
