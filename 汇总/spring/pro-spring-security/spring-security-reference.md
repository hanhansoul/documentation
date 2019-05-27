### 设置默认登陆后页面地址

default-target-url：当用户成功登陆后默认导向的页面地址。

	<http pattern="/login.htm*" security="none"/>
	<http use-expressions="false">
		<intercept-url pattern='/**' access='ROLE_USER' />
		<form-login login-page='/login.htm' default-target-url='/home.htm' always-use-default-target='true' />
	</http>

### 登出处理

默认登出url为/logout，可以通过logout-url属性指定。

### 使用其他认证提供商

1. LDAP
2. 数据库

## 高级网络应用特征

### remember-me认证

### HTTP/HTTPS通道安全

通过<\intercept-url>中的requires-channel属性指定。

### session管理

	<http>
		<session-management invalid-session-url="/invalidSession.htm" />
	</http>

	<http>
		<logout delete-cookies="JSESSIONID" />
	</http>

## OpenID支持

	<http>
		<intercept-url pattern="/**" access="ROLE_USER" />
		<openid-login />
	</http>

## 方法级安全

	// 允许使用@Secured注解
	<global-method-security secured-annotations="enabled" />

