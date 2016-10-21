### 23.7 访问应用参数

如果需要获取传递给`SpringApplication.run(…)`的应用参数，你可以注入一个`org.springframework.boot.ApplicationArguments`类型的bean。`ApplicationArguments`接口即提供对原始`String[]`参数的访问，也提供对解析成`option`和`non-option`参数的访问：
```java
import org.springframework.boot.*
import org.springframework.beans.factory.annotation.*
import org.springframework.stereotype.*

@Component
public class MyBean {

    @Autowired
    public MyBean(ApplicationArguments args) {
        boolean debug = args.containsOption("debug");
        List<String> files = args.getNonOptionArgs();
        // if run with "--debug logfile.txt" debug=true, files=["logfile.txt"]
    }

}
```
**注** Spring Boot也会注册一个包含Spring `Environment`属性的`CommandLinePropertySource`，这就允许你使用`@Value`注解注入单个的应用参数。
