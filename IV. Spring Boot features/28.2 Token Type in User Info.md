###28.2 User Info中的Token类型

Google和其他一些第三方身份（identity）提供商对发送给user info端点的请求头中设置的token类型名有严格要求。默认的`Bearer`满足大多数提供商要求，如果需要你可以设置`security.oauth2.resource.token-type`来改变它。
