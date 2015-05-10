### D.3. 启动可执行jars

`org.springframework.boot.loader.Launcher`类是个特殊的启动类，用于一个可执行jars的主要入口。它实际上就是你jar文件的`Main-Class`，并用来设置一个合适的`URLClassLoader`，最后调用你的`main()`方法。
