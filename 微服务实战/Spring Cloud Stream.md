# Spring Cloud Stream

## 快速入门

- @EnableBinding：用于指定一个或多个定义了@Input或@Output注解的接口，以此实现对消息通道的绑定。
	- Spring Cloud Stream默认实现了绑定了名为output通道的Source接口，绑定了名为input通道的Sink接口，还有结合Sink和Source的Processor接口。
- @StreamListener：定义在方法上，将被修饰的方法注册为消息中间件上数据流的时间监听器，注解中的属性值对应了监听的消息通道名。

## 核心概念

Spring Cloud Stream构建的应用程序与消息中间件之间是通过绑定器Binder相关联的。

### 绑定器

Binder实现了应用程序与消息中间件细节之间的隔离。当需要升级或更换中间件产品时，只需要更换对应的Binder绑定器而不需要修改任何Spring Boot应用逻辑。

### 发布——订阅模式

Spring Cloud Stream中的消息通信方式遵循了发布——订阅模式，当一条消息被投递到消息中间件之后，会通过共享的Topic主题进行广播，消息消费者在订阅的主题中收到它并处罚金

### 消费组

每一个微服务服务都会部署多个实例，消息生产者发送消息给具体微服务时，只希望被消费一次。

如果在同一个主题上的应用需要启动多个实例时，可以通过spring.cloud.stream.bindings.input.group属性为应用指定一个组名，这样这个应用的多个实例在接收到消息时，只会有一个成员真正受到消息并进行处理。

### 消息分区

消费组无法控制消息具体被哪个实例消费。

当生产者将消息数据发送给多个消费者实例时，保证拥有共同特征的消息数据始终是由同一个消费者实例接收和处理。

## 使用详解

- @EnableBinding启动消息驱动功能，只包含唯一属性value。

### 绑定消息通道

定义消息通道的注解：

- @Input
- @Output

定义绑定消息通道的接口可以被@EnableBinding注解的value参数指定，从而在应用启动时实现对定义消息通道的绑定。

创建一个将Input消息通道作为输出通道的接口

	public interface SinkSender {

		@Output(Sink.INPUT)
		MessageChannel output();
	}

	@EnableBinding(value = {Sink.class, SinkSender.class})
	public class SinkReceiver {
		private static Logger logger = LoggerFactory.getLogger(SinkReceiver.class);

		@StreamListener(Sink.INPUT)
		public void receive(Object payload) {
			logger.info("Received: " + payload);
		}
	}

### 注入消息通道

由于Spring Cloud Stream会根据绑定接口中的@Input和@Output注解来创建消息通道实例，所以我们也可以通过直接注入的方式来使用消息通道对象。

