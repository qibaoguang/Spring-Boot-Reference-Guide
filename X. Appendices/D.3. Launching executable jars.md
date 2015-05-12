### 附录D.3. 启动可执行jars

`org.springframework.boot.loader.Launcher`类是个特殊的启动类，用于一个可执行jars的主要入口。它实际上就是你jar文件的`Main-Class`，并用来设置一个合适的`URLClassLoader`，最后调用你的`main()`方法。

这里有３个启动器子类，JarLauncher，WarLauncher和PropertiesLauncher。它们的作用是从嵌套的jar或war文件目录中（相对于显示的从classpath）加载资源（.class文件等）。在`[Jar|War]Launcher`情况下，嵌套路径是固定的（`lib/*.jar`和war的`lib-provided/*.jar`），所以如果你需要很多其他jars只需添加到那些位置即可。PropertiesLauncher默认查找你应用存档的`lib/`目录，但你可以通过设置环境变量`LOADER_PATH`或application.properties中的`loader.path`来添加其他的位置（逗号分割的目录或存档列表）。
