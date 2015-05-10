### 附录D.1.1 可执行jar文件结构

Spring Boot Loader兼容的jar文件应该有以下结构：

```shell
example.jar
 |
 +-META-INF
 |  +-MANIFEST.MF
 +-org
 |  +-springframework
 |     +-boot
 |        +-loader
 |           +-<spring boot loader classes>
 +-com
 |  +-mycompany
 |     + project
 |        +-YouClasses.class
 +-lib
    +-dependency1.jar
    +-dependency2.jar
```
依赖需要放到内部的lib目录下。

