###31. 缓存
Spring框架提供为应用透明添加缓存的支持，核心思想是，将抽象应用到缓存方法，基于缓存中可用信息减少方法的执行。缓存逻辑的应用是透明的，不会干扰调用者。

**注** 具体参考Spring框架指南的[相应章节](http://docs.spring.io/spring/docs/4.3.3.RELEASE/spring-framework-reference/htmlsingle/#cache)。

简而言之，为服务的某个操作添加缓存跟为方法添加相应注解那样简单：
```java
import javax.cache.annotation.CacheResult;

import org.springframework.stereotype.Component;

@Component
public class MathService {

    @CacheResult
    public int computePiDecimal(int i) {
        // ...
    }

}
```
**注** 你既可以使用标准的JSR-107 (JCache)注解，也可以使用Spring自己的缓存注解，这是透明的，我们强烈建议你不要混淆使用。

**注** 透明的[更新](http://docs.spring.io/spring/docs/4.3.3.RELEASE/spring-framework-reference/htmlsingle/#cache-annotations-put)或[驱除](http://docs.spring.io/spring/docs/4.3.3.RELEASE/spring-framework-reference/htmlsingle/#cache-annotations-evict)缓存数据是可以的。
