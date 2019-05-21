### @SpringBootApplication

用在Spring Boot主类上，标识这是一个Spring Boot应用，用来开启Spring Boot的各项能力。

是包含三个注解的组合：
1. @SpringBootConfiguration
2. @EnableAutoConfiguration
3. @ComponentScan

### @EnableAutoConfiguration

允许Spring Boot自动配置注解，开启这个注解之后，Spring Boot就能根据当前类路径下的包或者类来配置Spring Bean。

### @Configuration

代替applicationContext.xml配置文件，所有这个配置文件里面能做到的事情都可以通过这个注解所在类来进行注册。

### @SpringBootConfiguration

这个注解就是@Configuration注解的变体，只是用来修饰是Spring Boot配置而已，或者可利于Spring Boot后续的扩展。

### @ComponentScan

代替配置文件中的<component-scan>配置，开启组件扫描，即自动扫描包路径下的@Component 注解进行注册bean实例到context中。

### @Conditional

用来标识一个Spring Bean或者Configuration配置文件，当满足指定的条件才开启配置。

- @ConditionalOnBean：当容器中有指定的Bean才开启配置。
- @ConditionalOnClass：当容器中有指定的Class才开启配置。
- @ConditionalOnMissingBean：当容器中没有指定的Bean才开启配置。
- @ConditionalOnMissingClass：当容器中没有指定的Class才开启配置。
- @ConditionalOnWebApplication:当前项目类型是WEB项目才开启配置。
- @ConditionalOnNotWebApplication：当前项目类型不是WEB项目才开启配置。
- @ConditionalOnProperty
- @ConditionalOnExpression
- @ConditionalOnJava
- @ConditionalOnResource
- @ConditionalOnJndi
- @ConditionalOnCloudPlatform
- @ConditionalOnSingleCandidate

### @ConfigurationProperties

用来加载额外的配置（如.properties文件），可用在@Configuration注解类，或者@Bean注解方法上面。

### @EnableConfigurationProperties

一般要配合@ConfigurationProperties注解使用，用来开启对@ConfigurationProperties注解配置Bean的支持。

### @AutoConfigureAfter

用在自动配置类上面，表示该自动配置类需要在另外指定的自动配置类配置完之后。

如Mybatis的自动配置类，需要在数据源自动配置类之后。

	@AutoConfigureAfter(DataSourceAutoConfiguration.class)
	public class MybatisAutoConfiguration {
		// ...
	}

### @AutoConfigureBefore

这个和@AutoConfigureAfter注解使用相反，表示该自动配置类需要在另外指定的自动配置类配置之前。

### @Import

用来导入一个或者多个@Configuration注解修饰的类，这在Spring Boot里面应用很多。