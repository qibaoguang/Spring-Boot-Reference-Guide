###20.2.5 自定义restart类加载器

正如以上[Restart vs Reload](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#using-spring-boot-restart-vs-reload)章节讨论的，重启功能是通过两个类加载器实现的。对于大部分应用来说是没问题的，但有时候它可能导致类加载问题。

默认情况，在IDE里打开的项目会通过'restart'类加载器加载，其他常规的`.jar`文件会使用'basic'类加载器加载。如果你工作在一个多模块的项目下，并且不是每个模块都导入IDE里，你可能需要自定义一些东西。你需要创建一个`META-INF/spring-devtools.properties`文件，`spring-devtools.properties`文件可以包含`restart.exclude.`，`restart.include.`前缀的属性。`include`元素定义了那些需要加载进'restart'类加载器中的实体，`exclude`元素定义了那些需要加载进'basic'类加载器中的实体，这些属性的值是一个将应用到classpath的正则表达式。

例如：
```properties
restart.include.companycommonlibs=/mycorp-common-[\\w-]+\.jar
restart.include.projectcommon=/mycorp-myproj-[\\w-]+\.jar
```
**注** 所有属性的keys必须唯一，只要以`restart.include.`或`restart.exclude.`开头都会考虑进去。所有来自classpath的`META-INF/spring-devtools.properties`都会被加载，你可以将文件打包进工程或工程使用的库里。
