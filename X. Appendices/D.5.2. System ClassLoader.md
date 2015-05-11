### 附录D.5.2. 系统ClassLoader

启动的应用在加载类时应该使用`Thread.getContextClassLoader()`（多数库和框架都默认这样做）。尝试通过`ClassLoader.getSystemClassLoader()`加载嵌套的类将失败。请注意`java.util.Logging`总是使用系统类加载器，由于这个原因你需要考虑一个不同的日志实现。
