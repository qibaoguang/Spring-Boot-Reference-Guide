### 附录D.3.1 Launcher manifest

你需要指定一个合适的Launcher作为`META-INF/MANIFEST.MF`的`Main-Class`属性。你实际想要启动的类（也就是你编写的包含main方法的类）需要在`Start-Class`属性中定义。

例如，这里有个典型的可执行jar文件的MANIFEST.MF：
```properties
Main-Class: org.springframework.boot.loader.JarLauncher
Start-Class: com.mycompany.project.MyApplication
```
对于一个war文件，它可能是这样的：
```properties
Main-Class: org.springframework.boot.loader.WarLauncher
Start-Class: com.mycompany.project.MyApplication
```
**注**：你不需要在manifest文件中指定`Class-Path`实体，classpath会从嵌套的jars中被推导出来。
