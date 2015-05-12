### 附录D.3.2. 暴露的存档

一些PaaS实现可能选择在运行前先解压存档。例如，Cloud Foundry就是这样操作的。你可以运行一个解压的存档，只需简单的启动合适的启动器：
```shell
$ unzip -q myapp.jar
$ java org.springframework.boot.loader.JarLaunche
```

