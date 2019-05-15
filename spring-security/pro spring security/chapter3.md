# spring security架构

spring security的关键组件：

- XML命名空间
- Servlet过滤器
- AuthenticationManager
- AuthenticationProvider
- UserDetailsService
- UserDetails
- SecurityContextHolder
- SecurityInterceptor
- AccessdecisionManager
- Authentication
- COnfigAttribute
- ACL
- Taglib

## security拦截器

security拦截器是spring security框架的核心。

- AbstractSecurityInterceptor
- FilterSecurityInterceptor：URL拦截器
- MethodSecurityInterceptor：方法调用拦截器

## XML命名空间


