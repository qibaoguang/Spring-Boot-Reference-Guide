###23.1 启动失败
如果应用启动失败，注册的`FailureAnalyzers`就有机会提供一个特定的错误信息，及具体的解决该问题的动作。例如，如果在`8080`端口启动一个web应用，而该端口已被占用，那你应该可以看到类似如下的内容：
```properties
***************************
APPLICATION FAILED TO START
***************************

Description:

Embedded servlet container failed to start. Port 8080 was already in use.

Action:

Identify and stop the process that's listening on port 8080 or configure this application to listen on another port.
```
**注** Spring Boot提供很多的`FailureAnalyzer`实现，你[自己实现](http://docs.spring.io/spring-boot/docs/1.4.1.RELEASE/reference/htmlsingle/#howto-failure-analyzer)也很容易。

如果没有可用于处理该异常的失败分析器（failure analyzers），你需要展示完整的auto-configuration报告以便更好的查看出问题的地方，因此你需要启用`org.springframework.boot.autoconfigure.logging.AutoConfigurationReportLoggingInitializer`的[debug](http://docs.spring.io/spring-boot/docs/1.4.1.RELEASE/reference/htmlsingle/#boot-features-external-config)属性，或开启[DEBUG日志级别](http://docs.spring.io/spring-boot/docs/1.4.1.RELEASE/reference/htmlsingle/#boot-features-custom-log-levels)。

例如，使用`java -jar`运行应用时，你可以通过如下命令启用`debug`属性：
```shell
$ java -jar myproject-0.0.1-SNAPSHOT.jar --debug
```
