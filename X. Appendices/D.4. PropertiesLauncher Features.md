### D.4. PropertiesLauncher特性

PropertiesLauncher有一些特殊的性质，它们可以通过外部属性来启用（系统属性，环境变量，manifest实体或application.properties）。

|Key|作用|
|----|:-----|
|loader.path|逗号分割的classpath，比如`lib:${HOME}/app/lib`|
|loader.home|其他属性文件的位置，比如[/opt/app](file:///opt/app)（默认为`${user.dir}`）|
|loader.args|main方法的默认参数（以空格分割）|
|loader.main|要启动的main类名称，比如`com.app.Application`|
|loader.config.name|属性文件名，比如loader（默认为application）|
|loader.config.location|属性文件路径，比如`classpath:loader.properties`（默认为application.properties）|
|loader.system|布尔标识，表明所有的属性都应该添加到系统属性中（默认为false）|

Manifest实体keys通过大写单词首字母及将分隔符从"."改为"-"（比如`Loader-Path`）来进行格式化。`loader.main`是个特例，它是通过查找manifest的`Start-Class`，这样也兼容JarLauncher。

环境变量可以大写字母并且用下划线代替句号。

* `loader.home`是其他属性文件（覆盖默认）的目录位置，只要没有指定`loader.config.location`。
* `loader.path`可以包含目录（递归地扫描jar和zip文件），存档路径或通配符模式（针对默认的JVM行为）。
* 占位符在使用前会被系统和环境变量加上属性文件本身的值替换掉。
