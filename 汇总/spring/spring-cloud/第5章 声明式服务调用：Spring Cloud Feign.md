# 声明式服务调用：Spring Cloud Feign

Spring Cloud Feign对RestTemplate进行了进一步封装，帮助我们定义和实现依赖服务接口的定义。在Feign实现下，我们只需要创建一个接口并用注解的方式来配置它，就可以完成对服务提供方的接口绑定，简化了使用Spring Cloud Ribbon自行封装服务调用客户端的开发量，Feign具备可插拔的注解支持，包括Feign注解和JAX-RS注解。扩展了对Spring MVC注解的支持。

## 快速入门

@EnableFeignClients注解开启Spring Cloud Feign支持。

	@EnableFeignClients
	@EnableDiscoveryClient
	@SpringBootApplication
	public class ConsumerApplication {
		public static void main(String[] args) {
			SpringApplication.run(ConsumerApplication.class, args);
		}
	}

定义接口，通过FeignClient注解指定服务名来绑定服务，再使用Spring MVC注解来绑定具体该服务提供的REST接口。

	@FeignClient("hello-service")
	public interface HelloService {
		@RequestMapping("/hello")
		String hello();
	}

	@RestController
	public classs ConsumerController {
		@AutoWired
		HelloService helloService;

		@RequestMapping(value="/feign-consumer", methos=RequestMethod.GET)
		public String helloConsumer() {
			return helloService.hello();
		}
	}

通过Feign，我们只需要定义服务绑定接口，以声明式方法，优雅而简单地实现服务调用。

## 参数绑定

注：Feign中，注解中的value属性不能为空。

- @RequestParam
- @RequestHeader

## 继承特性