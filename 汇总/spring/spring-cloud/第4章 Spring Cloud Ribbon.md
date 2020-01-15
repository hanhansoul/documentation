## 客户端负载均衡

- 系统高可用
- 缓解网络压力

1. 服务提供者只需要启动多个服务实例并注册到一个注册中心或是多个相关联的服务注册中心。
2. 服务消费者直接通过调用@LocalBalanced注解修饰过的RestTemplate来实现向服务的接口调用。

## RestTemplate详解

### GET请求

- getForEntity()
	- ResponseEntity getForEntity(String url, Class responseType, Object... urlVariables)
	- ResponseEntity getForEntity(String url, Class responseType, Map urlVariables)
	- ResponseEntity getForEntity(String url, Class responseType)

- getForObject()
	- Object getForObject(String url, Class responseType, Object... urlVariables)
	- Object getForObject(String url, Class responseType, Map urlVariables)
	- Object getForObject(String url, Class responseType)

### POST请求

- postForEntity()
	- ResponseEntity postForEntity(String url, Object request, Class responseType, Object... urlVariables)
	- ResponseEntity postForEntity(String url, Object request, Class responseType, Map urlVariables)
	- ResponseEntity postForEntity(String url, Object request, Class responseType)

request参数可以是一个普通对象，也可以是一个HttpEntity对象。RestTemplate会将普通对象request参数转换为一个HttpEntity对象。request会被当做一个完成的HTTP请求对象处理，包含body和header内容。

- postForObject()
	- Object postForObject(String url, Object request, Class responseType, Object... urlVariables)
	- Object postForObject(String url, Object request, Class responseType, Map urlVariables)
	- Object postForObject(String url, Object request, Class responseType)

postForLocation函数返回新资源的URI，相当于指定了返回类型，所以此方法实现的POST请求不需要指定responseType。

- postForLocation()
	- Object postForLocation(String url, Object request, Object... urlVariables)
	- Object postForLocation(String url, Object request, Map urlVariables)
	- Object postForLocation(String url, Object request)

### PUT请求

put()
	- put(String url, Object request, Object... urlVariables)
	- put(String url, Object request, Map urlVariables)
	- put(String url, Object request)

### DELETE请求

delete()
	- put(String url, Object... urlVariables)
	- put(String url, Map urlVariables)
	- put(String url)

## 配置详解

- IClientConfig：Ribbon的客户端配置
- IRule：Ribbon的负载均衡策略
- IPing：Ribbon的实例检查策略
- ServerList<Server>：服务实例清单的维护机制
- ServerListFilter<Server>：服务实例清单过滤机制
- ILoadBalancer：负载均衡器

## 重试机制

Eureka强调CAP原理中的AP，即可用性与可靠性，Eureka为实现更高的服务可用性，牺牲了一定的一致性。