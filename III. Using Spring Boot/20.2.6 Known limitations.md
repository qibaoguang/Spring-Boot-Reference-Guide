###20.2.6 已知限制

重启功能不能跟使用标准`ObjectInputStream`反序列化的对象工作，如果需要反序列化数据，你可能需要使用Spring的`ConfigurableObjectInputStream`，并结合`Thread.currentThread().getContextClassLoader()`。

不幸的是，一些第三方库反序列化时没有考虑上下文类加载器，如果发现这样的问题，你需要请求原作者给处理下。
