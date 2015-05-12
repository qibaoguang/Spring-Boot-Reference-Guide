### 附录D.1.2. 可执行war文件结构

Spring Boot Loader兼容的war文件应该遵循以下结构：
```java
example.jar
 |
 +-META-INF
 |  +-MANIFEST.MF
 +-org
 |  +-springframework
 |     +-boot
 |        +-loader
 |           +-<spring boot loader classes>
 +-WEB-INF
    +-classes
    |  +-com
    |     +-mycompany
    |        +-project
    |           +-YouClasses.class
    +-lib
    |  +-dependency1.jar
    |  +-dependency2.jar
    +-lib-provided
       +-servlet-api.jar
       +-dependency3.jar
```
依赖需要放到内嵌的`WEB-INF/lib`目录下。任何运行时需要但部署到普通web容器不需要的依赖应该放到`WEB-INF/lib-provided`目录下。
