### 附录D.2.1 对标准Java "JarFile"的兼容性

Spring Boot Loader努力保持对已有代码和库的兼容。`org.springframework.boot.loader.jar.JarFile`继承自`java.util.jar.JarFile`，可以作为降级替换。
