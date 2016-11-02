### 29.5.2 使用DSLContext
jOOQ提供的流式（fluent）API是通过`org.jooq.DSLContext`接口初始化的，Spring Boot将自动配置一个`DSLContext`为Spring Bean，并将它跟应用的`DataSource`连接起来。想要使用`DSLContext`，只需`@Autowire`注入它：
```java
@Component
public class JooqExample implements CommandLineRunner {

    private final DSLContext create;

    @Autowired
    public JooqExample(DSLContext dslContext) {
        this.create = dslContext;
    }

}
```
**注** jOOQ手册倾向于使用一个名为`create`的变量持有`DSLContext`，示例中也是这样做的。

然后你就可以使用`DSLContext`构造查询：
```java
public List<GregorianCalendar> authorsBornAfter1980() {
    return this.create.selectFrom(AUTHOR)
        .where(AUTHOR.DATE_OF_BIRTH.greaterThan(new GregorianCalendar(1980, 0, 1)))
        .fetch(AUTHOR.DATE_OF_BIRTH);
}
```
