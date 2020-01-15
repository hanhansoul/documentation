# Hystrix

可能因为网络原因或是依赖服务自身问题出现调用故障或延迟。

Hystrix是吸纳了断路器、线程隔离等服务保护功能。提供服务降级、服务熔断、线程和信号隔离、请求缓存、请求合并以及服务加农等功能。

- @EnableCircuitBreaker
- @SpringCloudApplication：包含了@SpringBootApplication、@EnableDiscoveryClient和@EnableCircuitBreaker注解。
- @HystrixCommand：在Service类中的方法上使用该注解，指定回调方法。

## 原理

命令设计模式

// TODO

## 使用详解

### 创建请求命令

Hystrix命令即HystrixCommand，用来封装具体的依赖服务调用逻辑。

	public class UserCommand extends HystrixCommand<User> {

		private RestTemplate restTemplate;
		priate Long id;

		public UserCommand(Setter setter, RestTemplate restTemplate, Long id) {
			super(setter);
			this.restTemplate = restTemplate;
			this.id = id;
		}

		protected User run() {
			return restTemplate.getForObject("http://USER-SERVICE/users/{1}", User.class, id);
		}
	}

- 同步执行：User u = new UserCommand(restTemplate, 1L).execute();
- 异步执行：Future<User> futureUser = new UserCommand(restTemplate, 1L).queue();

	Observable<String> ho = new UserCommand(restTemplate, 1L).observe();

	Observable<String> co = new UserCommand(restTemplate, 1L).toObservable();

### 定义服务降级

fallback是Hystrix命令执行失败时使用的后备方法，用来实现服务的降级处理逻辑。

### 异常处理

#### 异常传播

#### 异常获取

### 命令名称、分组以及线程池划分

Hystrix命令默认的线程划分是根据命令分组来实现的。

### 请求缓存

### 请求合并

