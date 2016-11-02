### 28.3.2 单点登陆
OAuth2客户端可用于从提供商抓取用户详情，然后转换为Spring Security需要的`Authentication` token。上述提到的资源服务器通过`user-info-uri`属性来支持该功能，这是基于OAuth2的单点登陆（SSO）协议最基本的，Spring Boot提供的`@EnableOAuth2Sso`注解让它更容易实践。通过添加该注解及端点配置（`security.oauth2.client.*`），Github客户端就可以使用`/user/`端点保护它的所有资源了：
```yaml
security:
    oauth2:
...
    resource:
        userInfoUri: https://api.github.com/user
        preferTokenInfo: false
```
由于所有路径默认都处于保护下，也就没有主页展示那些未授权的用户，进而邀请他们去登陆（通过访问`/login`路径，或`security.oauth2.sso.login-path`指定的路径）。

为了自定义访问规则或保护的路径（这样你就可以添加主页），你可以将`@EnableOAuth2Sso`添加到一个`WebSecurityConfigurerAdapter`，该注解会包装它，增强需要的地方以使`/login`路径工作。例如，这里我们允许未授权的用户访问主页`/`，其他的依旧保持默认：
```java
@Configuration
public class WebSecurityConfiguration extends WebSecurityConfigurerAdapter {

    @Override
    public void init(WebSecurity web) {
        web.ignore("/");
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.antMatcher("/**").authorizeRequests().anyRequest().authenticated();
    }

}
```
