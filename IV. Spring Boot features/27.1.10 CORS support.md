### 27.1.10 CORS支持
[跨域资源共享](http://en.wikipedia.org/wiki/Cross-origin_resource_sharing)（CORS）是一个[大多数浏览器](http://caniuse.com/#feat=cors)都实现了的[W3C标准](http://www.w3.org/TR/cors/)，它允许你以灵活的方式指定跨域请求如何被授权，而不是采用那些不安全，性能低的方式，比如IFRAME或JSONP。

从4.2版本开始，Spring MVC对[CORS](http://docs.spring.io/spring/docs/4.3.3.RELEASE/spring-framework-reference/htmlsingle/#cors)提供开箱即用的支持。不用添加任何特殊配置，只需要在Spring Boot应用的controller方法上注解[`@CrossOrigin`](http://docs.spring.io/spring/docs/4.3.3.RELEASE/javadoc-api/org/springframework/web/bind/annotation/CrossOrigin.html)，并添加[CORS配置](http://docs.spring.io/spring/docs/4.3.3.RELEASE/spring-framework-reference/htmlsingle/#_controller_method_cors_configuration)。通过注册一个自定义`addCorsMappings(CorsRegistry)`方法的`WebMvcConfigurer` bean可以指定[全局CORS配置](http://docs.spring.io/spring/docs/4.3.3.RELEASE/spring-framework-reference/htmlsingle/#_global_cors_configuration)：
```java
@Configuration
public class MyConfiguration {

    @Bean
    public WebMvcConfigurer corsConfigurer() {
        return new WebMvcConfigurerAdapter() {
            @Override
            public void addCorsMappings(CorsRegistry registry) {
                registry.addMapping("/api/**");
            }
        };
    }
}
```
