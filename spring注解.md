# spring常用注解

声明bean的注解

- @Component：组件，没有明确的角色
- @Service：在业务逻辑层使用（service层）
- @Repository：在数据访问层使用（dao层）
- @Controller：在展现层使用，控制器的声明（controll层）

注入bean的注解

- @Autowired：由Spring提供，通过@Qualifier注解可以使用byName注入
- @Inject：由JSR-330提供
- @Resource：由JSR-250提供

@Autowired与@Resource区别：

1. @Autowired按byType自动注入，而@Resource默认按byName自动注入；
2. @Autowired由spring提供，@Resource由JSR-250提供

@Resource装配顺序：

1. 如果同时指定了name和type，则从Spring上下文中找到唯一匹配的bean进行装配，找不到则抛出异常。
2. 如果指定了name，则从上下文中查找名称（id）匹配的bean进行装配，找不到则抛出异常。
3. 如果指定了type，则从上下文中找到类似匹配的唯一bean进行装配，找不到或是找到多个，都会抛出异常。
4. 如果既没有指定name，又没有指定type，则自动按照byName方式进行装配；如果没有匹配，则回退为一个原始类型进行匹配，如果匹配则自动装配。

java配置类相关注解

- @Configuration：声明当前类为配置类，相当于xml形式的Spring配置（类注解）
- @Bean：注解在方法上，声明当前方法的返回值为一个bean，替代xml中的<bean>（方法注解）
- @ComponentScan：用于对Component进行扫描，相当于xml中的<context:component-scan>（类上）
- @WishlyConfiguration：为@Configuration与@ComponentScan的组合注解，可以替代这两个注解

切面（AOP）相关注解

- @Aspect：声明一个切面（类注解）。使用@After、@Before、@Around定义建言（advice），可直接将拦截规则（切点）作为参数。
- @After：在方法执行之后执行（方法上）
- @Before：在方法执行之前执行（方法上）
- @Around：在方法执行之前与之后执行（方法上）
- @PointCut：声明切点。在java配置类中使用@EnableAspectJAutoProxy注解开启Spring对AspectJ代理的支持（类上）

@Bean的属性支持

- @Scope：设置Spring容器如何新建Bean实例（方法上，得有@Bean）。其设置类型包括：
	- Singleton（单例,一个Spring容器中只有一个bean实例，默认模式）,
	- Protetype（每次调用新建一个bean）,
	- Request（web项目中，给每个http request新建一个bean）,
	- Session（web项目中，给每个http session新建一个bean）,
	- GlobalSession（给每一个 global http session新建一个Bean实例）
- @PostConstruct：由JSR-250提供，在构造函数执行完之后执行，等价于xml配置文件中bean的initMethod
- @PreDestory：由JSR-250提供，在Bean销毁之前执行，等价于xml配置文件中bean的destroyMethod

@Value注解：为属性注入值

环境切换

- @Profile：通过设定Environment的ActiveProfiles来设定当前context需要使用的配置环境。（类或方法上）
- @Conditional：Spring4中可以使用此注解定义条件话的bean，通过实现Condition接口，并重写matches方法，从而决定该bean是否被实例化。（方法上）

异步相关

- @EnableAsync：配置类中，通过此注解开启对异步任务的支持，叙事性AsyncConfigurer接口（类上）
- @Async：在实际执行的bean方法使用该注解来申明其是一个异步任务（方法上或类上所有的方法都将异步，需要@EnableAsync开启异步任务）

定时任务相关

- @EnableScheduling：在配置类上使用，开启计划任务的支持（类上）
- @Scheduled：来申明这是一个任务，包括cron、fixDelay、fixRate等类型（方法上，需先开启计划任务的支持）

@Enable\*注解说明：这些注解主要用来开启对xxx的支持。

- @EnableAspectJAutoProxy：开启对AspectJ自动代理的支持
- @EnableAsync：开启异步方法的支持
- @EnableScheduling：开启计划任务的支持
- @EnableWebMvc：开启Web MVC的配置支持
- @EnableConfigurationProperties：开启对@ConfigurationProperties注解配置Bean的支持
- @EnableJpaRepositories：开启对SpringData JPA Repository的支持
- @EnableTransactionManagement：开启注解式事务的支持
- @EnableCaching：开启注解式的缓存支持

SpringMVC部分

- @EnableWebMvc：在配置类中开启Web MVC的配置支持，如一些ViewResolver或者MessageConverter等，若无此句，重写WebMvcConfigurerAdapter方法（用于对SpringMVC的配置）。
- @Controller：声明该类为SpringMVC中的Controller
- @RequestMapping：用于映射Web请求，包括访问路径和参数（类或方法上）
- @ResponseBody：支持将返回值放在response内，而不是一个页面，通常用户返回json数据（返回值旁或方法上）
- @RequestBody：允许request的参数在request体中，而不是在直接连接在地址后面。（放在参数前）
- @PathVariable：用于接收路径参数，比如@RequestMapping(“/hello/{name}”)申明的路径，将注解放在参数中前，即可获取该值，通常作为Restful的接口实现方法。
- @RestController：该注解为一个组合注解，相当于@Controller和@ResponseBody的组合，注解在类上，意味着，该Controller的所有方法都默认加上了@ResponseBody。
- @ControllerAdvice：通过该注解，我们可以将对于控制器的全局配置放置在同一个位置，注解了@Controller的类的方法可使用@ExceptionHandler、@InitBinder、- @ModelAttribute
- @ExceptionHandler：用于全局处理控制器里的异常
- @InitBinder：用来设置WebDataBinder，WebDataBinder用来自动绑定前台请求参数到Model中。

# spring boot常用注解

## @SpringBootApplication

spring boot核心注解，用在Spring Boot主类上，标识这是一个Spring Boot应用，用来开启Spring Boot的各项能力。

@SpringBootApplication是@SpringBootConfiguration、@EnableAutoConfiguration、@ComponentScan这三个注解的组合，也可以用这三个注解来代替@SpringBootApplication注解。

## @Configuration

这是Spring 3.0添加的一个注解，用来代替applicationContext.xml配置文件，所有这个配置文件里面能做到的事情都可以通过这个注解所在类来进行注册。

## @SpringBootConfiguration

这个注解就是@Configuration注解的变体，只是用来修饰是Spring Boot配置而已，或者可利于Spring Boot后续的扩展。

## @EnableAutoConfiguration

允许Spring Boot自动配置注解，开启这个注解之后，Spring Boot就能根据当前类路径下的包或者类来配置Spring Bean。

如：当前类路径下有Mybatis这个JAR包，MybatisAutoConfiguration注解就能根据相关参数来配置Mybatis的各个Spring Bean。

## @ComponentScan

这是Spring 3.1添加的一个注解，用来代替配置文件中的component-scan配置，开启组件扫描，即自动扫描包路径下的@Component注解进行注册bean实例到context中。

## @Conditional

这是Spring 4.0添加的新注解，用来标识一个Spring Bean或者Configuration配置文件，当满足指定的条件才开启配置。

- @ConditionalOnBean：组合@Conditional注解，当容器中有指定的Bean才开启配置。
- @ConditionalOnMissingBean：组合@Conditional注解，和@ConditionalOnBean注解相反，当容器中没有指定的Bean才开启配置。
- @ConditionalOnClass：组合@Conditional注解，当容器中有指定的Class才开启配置。
- @ConditionalOnMissingClass：组合@Conditional注解，和@ConditionalOnMissingClass注解相反，当容器中没有指定的Class才开启配置。
- @ConditionalOnWebApplication：组合@Conditional注解，当前项目类型是WEB项目才开启配置。
- @ConditionalOnNotWebApplication
- @ConditionalOnProperty：组合@Conditional注解，当指定的属性有指定的值时才开启配置。
- @ConditionalOnExpression
- @ConditionalOnJava
- @ConditionalOnResource
- @ConditionalOnJndi
- @ConditionalOnCloudPlatform
- @ConditionalOnSingleCandidate

## @ConfigurationProperties

用来加载额外的配置（如 .properties 文件），可用在 @Configuration 注解类，或者@Bean注解方法上面。

## @EnableConfigurationProperties

一般要配合@ConfigurationProperties注解使用，用来开启对@ConfigurationProperties注解配置Bean的支持。

## @AutoConfigureAfter

用在自动配置类上面，表示该自动配置类需要在另外指定的自动配置类配置完之后。

如Mybatis的自动配置类，需要在数据源自动配置类之后。

## @AutoConfigureBefore

这个和@AutoConfigureAfter注解使用相反，表示该自动配置类需要在另外指定的自动配置类配置之前。

## @Import

这是Spring 3.0添加的新注解，用来导入一个或者多个@Configuration注解修饰的类，这在Spring Boot里面应用很多。

## @ImportResource

这是Spring 3.0添加的新注解，用来导入一个或者多个Spring配置文件，这对Spring Boot兼容老项目非常有用，因为有些配置无法通过Java Config的形式来配置就只能用这个注解来导入。

# spring boot自动配置原理

Spring Boot的自动配置注解是@EnableAutoConfiguration。

https://mp.weixin.qq.com/s?__biz=MzI3ODcxMzQzMw==&mid=2247484365&idx=1&sn=a4ab1d977d6b03bf122b4d596d7ee1ab&scene=21#wechat_redirect
