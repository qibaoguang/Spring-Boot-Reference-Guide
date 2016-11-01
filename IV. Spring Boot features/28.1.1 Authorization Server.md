### 28.1.1 授权服务器

想要创建一个授权服务器，并授予access tokens，你需要使用`@EnableAuthorizationServer`，并提供`security.oauth2.client.client-id`和`security.oauth2.client.client-secret`配置。

按以上操作后，你就能使用客户端证书创建一个access token，例如：
```shell
$ curl client:secret@localhost:8080/oauth/token -d grant_type=password -d username=user -d password=pwd
```
`/token`端点basic形式的认证证书是`client-id`和`client-secret`，用户证书通常是Spring Security的user详情（Spring Boot中默认是"user"和一个随机的密码）。

想要关闭自动配置，自己配置授权服务器特性，你只需添加一个`AuthorizationServerConfigurer`类型的`@Bean`。
