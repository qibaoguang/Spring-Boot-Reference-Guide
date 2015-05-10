### 附录D.2. Spring Boot的"JarFile"类

Spring Boot用于支持加载内嵌jars的核心类是`org.springframework.boot.loader.jar.JarFile`。它允许你从一个标准的jar文件或内嵌的子jar数据中加载jar内容。当首次加载的时候，每个JarEntry的位置被映射到一个偏移于外部jar的物理文件：
```java
myapp.jar
+---------+---------------------+
|         | /lib/mylib.jar      |
| A.class |+---------+---------+|
|         || B.class | B.class ||
|         |+---------+---------+|
+---------+---------------------+
^          ^          ^
0063       3452       3980
```
上面的示例展示了如何在myapp.jar的0063处找到A.class。来自于内嵌jar的B.class实际可以在myapp.jar的3452处找到，B.class可以在3980处找到（图有问题？）。

有了这些信息，我们就可以通过简单的寻找外部jar的合适部分来加载指定的内嵌实体。我们不需要解压存档，也不需要将所有实体读取到内存中。
