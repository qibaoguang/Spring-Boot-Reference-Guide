### 28.3 自定义User Info RestTemplate
如果设置了`user-info-uri`，资源服务器在内部将使用一个`OAuth2RestTemplate`抓取用于认证的用户信息，这是一个id为`userInfoRestTemplate`的`@Bean`提供的，但你不需要了解这些，只需要用它即可。默认适用于大多数提供商，但偶尔你可能需要添加其他interceptors，或改变request的验证器（authenticator）。想要添加自定义，只需创建一个`UserInfoRestTemplateCustomizer`类型的bean —— 它只有单个方法，在bean创建后，初始化前会调用该方法。此处自定义的rest template仅用于内部执行认证。

**注** 在YAML中设置RSA key时，需要使用管道符分割多行（“|”），记得缩进key value，例如：
```yaml
security:
    oauth2:
        resource:
            jwt:
                keyValue: |
                    -----BEGIN PUBLIC KEY-----
                    MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKC...
                    -----END PUBLIC KEY-----
```
