# 第六章 配置不同的认证提供服务者

- LDAP
- OpenId
- X.509
- JAAS
- 数据库

## 基于数据库的认证服务提供者

基于数据库的认证机制使用的AuthenticationProvider实现类为DaoAuthenticationProvider，该实现是基于一个用于获取用户信息的UserDetailService抽象，因此与基于内存的认证区别在于UserDetailService具体实现。

web.xml

	<?xml version="1.0" encoding="UTF-8"?>
	<web-app>
		<listener>
			<listener-class>
				org.springframework.web.context.ContextLoaderListener
			</listener-class>
		</listener>

		<context-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>
				/WEB-INF/applicationContext-security.xml
			</param-value>
		</context-param>

		<filter>
			<filter-name>springSecurityFilterChain</filter-name>
			<filter-class>
				org.springframework.web.filter.DelegatingFilterProxy
			</filter-class>
		</filter>

		<filter-mapping>
			<filter-name>springSecurityFilterChain</filter-name>
			<url-pattern>/*</url-pattern>
		</filter-mapping>
	</web-app>

applicationContext-security.xml

	<security:http auto-config="true">
		<security:intercept-url pattern="/*" access="ROLE_SCARVAREZ_MEMBER" />
		<security:form-login/>
	</security:http>

	<security:authentication-manager>
		<security:authentication-provider>
			<security:jdbc-user-service data-source-ref="dataSource" />
		</security:authentication-provider>
	</security:authentication-manager>

	<jdbc:embedded-database id="dataSource">
		<jdbc:script location="classpath:security-schema.sql"/>
		<jdbc:script location="classpath:users.sql"/>
	</jdbc:embedded-database>


## LDAP认证

LDAP（Lightweight Directory Access Protocol），即轻量目录访问协议。

通过LDAP我们可以实现:

1. 所有相关系统都能接入
2. 统一用户身份及安全管理
3. 所有系统一个账号及密码

目录简单来说就是一种树状结构的数据库。

而目录服务是一种以树状结构的目录数据库为基础，外加各种访问协议的信息查询服务。

顾名思义，目录天生就是用来查询的。

与关系型数据库相比，目录服务最大的优点就是读取性能极高。但是，目录服务的写入性能就非常差了，而且不支持事务处理等容错功能，因此不适合频繁修改数据。

在现实世界中，资源的分布形式很多都是树状的、有层次的。因此，目录服务有着非常广泛的用途。像企业员工信息、企业设备信息、证书公钥等具有层次性、且不需要频繁改写的数据都适合使用目录服务来存储。X.500是ISO制定的一套目录服务的标准，它是一个协议族，定义了一个机构如何在全局范围内共享名称和与名称相关联的对象。通过它，可以将局部的目录服务连接起来，构建基于Internet的分布在全球的目录服务系统，而目录访问协议（DAP）是X.500的核心组成之一。

LDAP 协议的主要特点如下：

1. 基于TCP/IP。
2. 以树状结构存储数据。
3. 读取速度快，写入速度慢。
4. 服务器用于存放数据，客户端用于操作数据。
5. 跨平台、维护简单。
6. 支持SSL/TLS加密。
7. 协议是开放的。

LDAP的四大模型：

1. 信息模型：规定了LDAP目录信息的表示方式以及数据的存储结构；
2. 命名模型：规定了LDAP目录中数据如何进行组织和区分。
3. 功能模型：规定了可以对LDAP目录进行的操作（如查询、更新、认证等）。
4. 安全模型：规定了保护LDAP目录中的数据的安全措施。

### LDAP结构

## OpenID

OpenID用于单点登录的验证功能。

OpenID是一个身份认证机制，不包含任何权限控制功能。

### 配置OpenID认证

	<security:http auto-config="true">
		<security:intercept-url pattern="/*" access="ROLE_ADMINISTRATOR" />
		<security:openid-login/>
	</security:http>

	<security:user-service id="userService">
		<security:user name="http://carlo8172.myopenid.com/" authorities="ROLE_ADMINISTRATOR"/>
	</security:user-service>

	<security:authentication-manager alias="authenticationManager"/>

`<security:openid-login/>`为spring提供了一个OpenIDAuthenticationFilter和OpenIDAuthenticationProvider，同时将对应的UserDetailsService实现注入到AuthenticationManager中。

<security:user-service>中提供了OpenID账户的用户名。

### OpenID流程

1. /spring_security_login由DefaultLoginPageGeneratingFilter创建。登录页面上包含一个`<form name="" action="/j_spring_openid_security_check" method="POST">`。
2. 登录时，请求被发送给OpenIDAuthenticationFilter