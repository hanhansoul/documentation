# API网关服务

对外服务，即边缘服务。

- 降低维护路由规则和服务实例列表的难度
- 对于微服务接口访问时各前置校验等冗余问题的处理

Facade设计模式

Zuul提供了一套过滤器机制，支持对微服务接口的拦截和校验。开发者通过使用Zull创建各种校验过滤器，指定规则请求执行校验逻辑，只有通过校验的才会被路由到具体的微服务接口。

## 快速入门

### 创建网关

@EnableZuulProxy开启API网关服务功能。

### 请求路由

- 传统路由方式
- 面向服务的路由

### 请求过滤

继承ZuulFilter抽象类并实现它定义的4个抽象函数。

- filterType：过滤器类型
- filterOrder：过滤器的执行顺序
- shouldFilter：判断该过滤器是否需要被执行
- run：过滤器的具体逻辑

## 路由详解

### 传统路由配置

不依赖于服务发现机制的情况下，通过在配置文件中具体指定每个路由表达式与服务实例的映射关系来实现API网关对外部请求的路由。

- 单实例配置
- 多实例配置

### 服务路由配置

Zuul与Eureka整合，实现对服务实例的自动化维护。

### 请求过滤

通过前置的网关服务来完成非业务性质的校验。

Zuul允许开发者在API网关上通过定义过滤器来实现对请求的拦截与过滤。只需要继承ZuulFilter抽象类并实现它定义的4个抽象函数就可以完成对请求的拦截和过滤了。

- 作为系统的统一入口，屏蔽了系统内部各个微服务的细节。
- 可以与服务治理框架结合，实现自动化的服务实例维护以及负载均衡的路由转发。
- 可以实现接口权限校验与微服务业务逻辑的解耦。
- 通过服务网关中的过滤器，在各生命周期总去校验请求的内容，将原本在对外服务层做的校验前移，保证微服务的无状态性。

## 路由详解

### 传统路由配置

#### 单实例配置

#### 多实例配置

	zuul.routes.<route>.path
	zuul.routes.<route>.serviceId
	<route>.ribbon.listOfServiers

- ribbon.eureka.enabled：由于zuul.routes.<route>.serviceId指定的是服务名称，默认情况下Ribbon会根据服务发现机制来获取配置服务名对应的实例清单。
	- user-service.ribbon.listOfServers：该参数内容与zuul.routes.<route>.serviceId的配置相对应，这两个参数的配置相当于在该应用内部手工维护了服务与实例的对应关系。

### 服务路由配置

- zuul.routes.<serviceId>=<path>：<serviceId>用来指定路由的具体服务名，<path>用来配置匹配的请求表达式。

API网关也可以看作Eureka服务治理下的一个普通微服务应用，除了将自己注册到Eureka服务注册中心上以外，也会从注册中心获取所有服务以及它们的实例清单。

### 服务路由的默认规则

### 自定义路由映射规则

### 路径匹配

### 路由前缀

### 本地跳转

### Cookie与头信息

## 过滤器详解

### 过滤器

过滤器负责对请求的处理过程进行干预，是实现请求校验、服务聚合等功能的基础。

路由映射和请求转发都是由几个不同的过滤器完成的，路由映射主要通过pre类型的过滤器完成，它将请求路径与配置的路由规则进行匹配，以找到需要转发的目标地址；而请求转发由route类型的过滤器完成，对pre类型过滤器获得的路由地址进行转发。

过滤器基本特征：
- filterType()：过滤类型
	- pre：请求被路由之前调用
	- routing：路由请求时被调用
	- post：在routing和error过滤器之后被调用
	- error：处理请求时发生错误时被调用
- filterOrder()：执行顺序
- shouldFilter()：执行条件
- run()：具体操作

### 请求生命周期

Zuul过滤器覆盖了一个外部HTTP请求到达API网关，直到返回请求结果的全部生命周期。

	pre -> routing -> post

### 核心过滤器

- pre过滤器
	- ServletDetectionFilter：最优先执行的过滤器，用来检测当前请求是通过Spring的DispatcherServlet处理运行的，还是通过ZuulServlet来处理运行的。
	- Servlet30WrapperFilter：第二个执行的过滤器，将原始的HttpServletRequest包装成Servlet30RequestWrapper对象。
	- FormBodyWrapperFilter：第三个执行的过滤器，将符合要求的请求体包装成FormBodyRequestWrapper对象。
	- DebugFilter：第四个执行的过滤器，根据配置参数和请求中的debug参数来决定是否执行过滤器中的操作。
	- PreDecorationFilter：pre阶段最后执行的过滤器，判断当前请求上下文中是否存在forward.to和serviceId参数，并为当前请求做一些预处理。

- route过滤器
	- RibbonRoutingFilter：serviceId请求转发
	- SimpleHostRoutingFilter：url请求转发
	- SendForwardFilter：forward请求转发

- post过滤器
	- SendErrorFilter：处理有错误的请求响应
	- SendResponseFilter：处理正常处理的请求响应

### 异常处理

### ErrorFilter处理

使用error类型的过滤器：在pre、route、post三个阶段中有异常抛出的时候都会进入error阶段处理，因此可以通过创建一个error类型的过滤器来捕获这些异常信息，并根据这些异常信息在请求的上下文中注入需要返回给客户端的错误描述。

### 不足与优化

自定义过滤器中处理异常的两种基本方法：
1. 通过各个阶段的过滤器增加try-catch块，实现过滤器内部的异常处理；
2. 利用error类型过滤器的生命周期特征，集中处理pre、route、post阶段抛出的异常信息。

// TODO

### 禁用过滤器

Zuul提供了一个参数来禁用指定的过滤器。

	zuul.<SimpleClassName>.<filterType>.disable=true

## 动态加载

作为最外部的网关，必须具备动态更新内部逻辑的能力，如动态修改路由规则、动态添加/删除过滤器等等。

### 动态路由

Zuul动态路由与Spring Cloud Config动态刷新机制联系，将API网关配置文件通过Spring Cloud Config连接的Git仓库存储和管理，就能轻松实现动态刷新路由规则功能。

// TODO

### 动态过滤器

对于请求过滤器的动态加载，需要借助基于JVM实现的动态语言，如Groovy。

