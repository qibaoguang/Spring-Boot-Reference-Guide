### 27.3.2 Servlet上下文初始化
内嵌servlet容器不会直接执行Servlet 3.0+的`javax.servlet.ServletContainerInitializer`接口，或Spring的`org.springframework.web.WebApplicationInitializer`接口，这样设计的目的是降低war包内运行的第三方库破坏Spring Boot应用的风险。

如果需要在Spring Boot应用中执行servlet上下文初始化，你需要注册一个实现`org.springframework.boot.context.embedded.ServletContextInitializer`接口的bean。`onStartup`方法可以获取`ServletContext`，如果需要的话可以轻松用来适配一个已存在的`WebApplicationInitializer`。

**扫描Servlets, Filters和listeners**

当使用一个内嵌容器时，通过`@ServletComponentScan`可以启用对注解`@WebServlet`，`@WebFilter`和`@WebListener`类的自动注册。

**注** 在独立的容器（非内嵌）中`@ServletComponentScan`不起作用，取为代之的是容器内建的discovery机制。
