# 分布式session

Web应用在单机部署的情况下，Session是被单个应用服务器存储管理的，由于只有一个应用服务器，用户的所有请求都是通过它进行响应处理的，所以能够很容易实现会话跟踪和保持。分布式架构或微服务架构，无论使应用系统部署到多台应用服务器上，用户的请求也会通过负载均衡转发到某个具体应用服务器上执行，可能会出现在A1系统登录后创建并保存Session，再次发起请求，请求被转发到A2系统上显示未登录的情况，此时单机部署模式下的Session机制已不能满足要求。所以，在分布式架构或微服务架构下，必须保证一个应用服务器上保存Session后，其它应用服务器可以同步或共享这个Session。

## 分布式session管理实现方案

### Session复制

（不适用）

在支持Session复制的Web服务器上，通过修改Web服务器的配置，可以实现将Session同步到其它Web服务器上，达到每个Web服务器上都保存一致的Session。

优点：代码上不需要做支持和修改。

缺点：需要依赖支持的Web服务器，一旦更换成不支持的Web服务器就不能使用了，在数据量很大的情况下不仅占用网络资源，而且会导致延迟。

适用场景：只适用于Web服务器比较少且Session数据量少的情况。

可用方案：开源方案tomcat-redis-session-manager，暂不支持Tomcat8。

### Session粘滞

（不适用）

将用户的每次请求都通过某种方法强制分发到某一个Web服务器上，只要这个Web服务器上存储了对应Session数据，就可以实现会话跟踪。

优点：使用简单，没有额外开销。

缺点：一旦某个Web服务器重启或宕机，相对应的Session数据将会丢失，而且需要依赖负载均衡机制。

适用场景：对稳定性要求不是很高的业务情景。

### Session集中管理

（核心 + Spring Session）

在单独的服务器或服务器集群上使用缓存技术，如Redis存储Session数据，集中管理所有的Session，所有的Web服务器都从这个存储介质中存取对应的Session，实现Session共享。

优点：可靠性高，减少Web服务器的资源开销。

缺点：实现上有些复杂，配置较多。

适用场景：Web服务器较多、要求高可用性的情况。

可用方案：开源方案Spring Session，也可以自己实现，主要是重写HttpServletRequestWrapper中的getSession方法，博主也动手写了一个，github搜索joincat用户，然后自取。

### 基于Cookie管理

（不适用）

这种方式每次发起请求的时候都需要将Session数据放到Cookie中传递给服务端。

优点：不需要依赖额外外部存储，不需要额外配置。

缺点：不安全，易被盗取或篡改；Cookie数量和长度有限制，需要消耗更多网络带宽。

适用场景：数据不重要、不敏感且数据量小的情况。

# Spring Session + redis实现分布式session

https://cloud.tencent.com/developer/article/1470096

	server:
	  port: 8081
	spring:
	  redis:
	    database: 0
	    host: localhost
	    port: 6379
	    jedis:
	      pool:
	        max-active: 8
	        max-wait: -1
	        max-idle: 8
	        min-idle: 0
	    timeout: 10000
	redis:
	 hostname: localhost
	 port: 6379
	 #password: 123456

 
	//maxInactiveIntervalInSeconds为SpringSession的过期时间（单位：秒）
	@EnableRedisHttpSession(maxInactiveIntervalInSeconds = 1800)
	public class SessionConfig {

	    // 冒号后的值为没有配置文件时，制动装载的默认值
	    @Value("${redis.hostname:localhost}")
	    private String hostName;
	    @Value("${redis.port:6379}")
	    private int port;
	   // @Value("${redis.password}")
	   // private String password;

	    @Bean
	    public JedisConnectionFactory connectionFactory() {
	        JedisConnectionFactory connection = new JedisConnectionFactory();
	        connection.setPort(port);
	        connection.setHostName(hostName);
	        //connection.setPassword(password);
	        // connection.setDatabase(0);
	        return connection;
	    }
	}