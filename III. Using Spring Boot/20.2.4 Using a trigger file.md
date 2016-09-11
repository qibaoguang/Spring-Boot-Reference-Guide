###20.2.4 使用触发器文件

如果使用一个IDE连续不断地编译变化的文件，你可能倾向于只在特定时间触发重启，触发器文件可以帮你实现该功能。触发器文件是一个特殊的文件，只有修改它才能实际触发一个重启检测。改变该文件只会触发检测，实际的重启只会在Devtools发现它必须这样做的时候，触发器文件可以手动更新，也可以通过IDE插件更新。

使用`spring.devtools.restart.trigger-file`属性可以指定触发器文件。

**注** 你可能想将`spring.devtools.restart.trigger-file`属性设置为[全局设置](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#using-boot-devtools-globalsettings)，这样所有的工程表现都会相同。
