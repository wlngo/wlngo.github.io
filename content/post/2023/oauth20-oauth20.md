---
title: oauth2.0
date: 2023-04-24 17:56:24.552
updated: 2023-11-20 15:25:02.723
url: /archives/oauth20
categories: 
tags: 
---


spring.security.oauth2.client.registration.gitee.redirect-uri:
[文档](https://docs.spring.io/spring-security/site/docs/5.2.12.RELEASE/reference/html/oauth2.html#oauth2login-sample-redirect-uri)
旧的模板值
```
'{baseUrl}/{action}/oauth2/code/{registrationId}'
```
新的模板值
```
'{baseUrl}/login/oauth2/code/{registrationId}'
```
## Oauth2.0 资料文档
- https://www.jetbrains.com/help/youtrack/devportal/OAuth-authorization-in-hub.html
- https://juejin.cn/post/7151692788071923742#heading-0
- 客户端模式  scope 多个需要 单个空格 间隔开
- https://developer.okta.com/docs/api/openapi/okta-oauth/guides/client-auth/#token-claims-for-client-authentication-with-client-secret-or-private-key-jwt
- https://cloudentity.com/developers/basics/oauth-client-authentication/client-secret-authentication/#authentication-using-client_secret_basic-method
- 注册(RegisteredClientRepository)  token(OAuth2AuthorizationService)
- https://docs.spring.io/spring-security/reference/servlet/oauth2/index.html
- https://spring.io/projects/spring-authorization-server

## Spring Security 权限决策 （AccessDecisionManager）

- 一票通过(AffirmativeBased)
- 一票否决(UnanimousBased)
- 少数服从多数(ConsensusBased)
## Spring Security json 登录（spring boot 3 +）
- https://www.duidaima.com/Group/Topic/JAVA/10494(Spring Security )

## jwks 
https://fullstackladder.dev/blog/2023/01/28/openid-validate-token-with-rs256-and-jwks/