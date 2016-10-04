###20.4 全局设置

在`$HOME`文件夹下添加一个`.spring-boot-devtools.properties`的文件可以用来配置全局的devtools设置（注意文件名以"."开头），添加进该文件的任何属性都会应用到你机器上使用该devtools的Spring Boot应用。例如，想使用触发器文件进行重启，可以添加如下配置：

~/.spring-boot-devtools.properties.
```properties
spring.devtools.reload.trigger-file=.reloadtrigger
```
