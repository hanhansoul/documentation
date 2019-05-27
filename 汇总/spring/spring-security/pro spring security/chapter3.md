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

## 过滤器与过滤器链

spring security通过过滤器链模型实现网络应用的安全。spring security过滤器链是基于标准servlet过滤功能实现的。

spring security过滤器链由bean构成。由于标准的基于servlet的网络应用并不能识别spring的bean，因此spring security提供了DelegatingFilterProxy代理类用于处理标准servlet API和生命周期与过滤器所在的spring应用的关系。

## ConfigAttribute

ConfigAttribute封装了访问元数据信息，如角色信息ROLE_ADMIN就是一个ConfigAttribute。

## Authentication对象

Authentication对象是表示登录系统的用户的抽象。

Authentication具体实现：
- UsernamePasswordAuthenticationToken：用户名与密码认证信息
- PreAuthenticatedAuthenticationToken：用于处理认证前的Authentication对象
- OpenIDAuthenticationToken：OpenID认证
- RunAsUserToken：RunAsManager

## SecurityContext与SecurityContextHolder

SecurityContext用于为当前线程存储Authentication对象，其实现类为SecurityContextImp。

SecurityContextHolder用于访问SecurityContext。

## AuthenticationProvider

AuthenticationProvider是认证Authentication对象的主要入口。

AuthenticationProvider接口包含两个方法：
- authenticate()
- supports()

AuthenticationProvider实现类：
- CasAuthenticationProvider
- JaasAuthenticationProvider
- DaoAuthenticationProvider
- OpenIDAuthenticationProvider
- RememberMeAuthenticationProvider
- LdapAuthenticationProvider

## AccessDecisionManager

AccessDecisionManager用于控制一个Authentication对象是否被允许访问特定的资源。

- AffirmativeBased
- ConsensusBased
- UnanimousBased

## AccessDecisionVoter

## UserDetailsService与AuthenticationUserDetailsService

UserDetailsService用于在认证请求到达时从底层加载用户信息，UserDetailsService接口包含一个方法loadUserByUsername(String username)。

	public interface UserDetailsService {
		UserDetails loadUserByUsername(String username) throws UsernameNotFoundException;
	}

AuthenticationUserDetailsService可以根据传入的Authentication对象返回一个UserDetails对象。

	public interface AuthenticationUserDetailsService<T extends Authentication> {
		UserDetails loadUserDetails(T token) throws UsernameNotFoundException;
	}


UserDetailsService与AuthenticationUserDetailsService一般通过特定的AuthenticationProvider对象调用。如OpenIDAuthenticationProvider和CasAuthenticationProvider通过AuthenticationUserDetailsService来获取UserDetails，而DaoAuthenticationProvider通过UserDetailsService获取UserDetails。

## UserDetails

UserDetails表示一个已经通过认证的用户，可以通过SecurityContext获取。

public interface UserDetails extends Serializable {
	Collection<? extends GrantedAuthority> getAuthorities();
	String getPassword();
	String getUsername();
	boolean isAccountNonExpired();
	boolean isAccountNonLocked();
	boolean isCredentialsNonExpired();
	boolean isEnabled();
}

# spring security中的设计模式

## 策略模式

策略模式定义了一系列的算法，并将每一个算法封装起来，使每个算法可以相互替代，使算法本身和使用算法的客户端分割开来，相互独立。

应用：如传递给TreeSet的Comparator对象。

开闭原则：
1. 对于扩展是开放的（Open for extension）。这意味着模块的行为是可以扩展的。当应用的需求改变时，我们可以对模块进行扩展，使其具有满足那些改变的新行为。也就是说，我们可以改变模块的功能。
2. 对于修改是关闭的（Closed for modification）。对模块行为进行扩展时，不必改动模块的源代码或者二进制代码。

## 装饰器模式

## 单一职责原则

## 依赖注入