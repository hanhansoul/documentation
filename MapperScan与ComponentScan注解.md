## @MapperScan

之前要求直接在每一个Mapper类上添加注解@Mapper，通过@MapperScan可以指定要扫描的Mapper类的包的路径。

在项目中大多数用的都是@MapperScan注解，指定basePackages，扫描mybatis Mapper接口类。另外一种方式是用@Mapper注解，其实这两种方法扫描配置用的是一个地方，只是扫描入口不同。

@MapperScan是根据其注解上MapperScannerRegistrar进行自动配置的，最终调用的自动配置代码和下面的代码一致。

@Mapper自动配置的程序入口是 MybatisAutoConfiguration类的最下面，位置在mybatis-spring-boot-starter包下面的mybatis-spring-boot-autoconfigure，根据名字可以看出，这个是自动配置，这个地方用到了spring-boot的自动配置相关注解。

### @MapperScan与@ComponentScan

@ComponentScan是组件扫描注解，用来扫描@Controller、@Service、@Repository这类,主要就是定义扫描的路径从中找出标志了需要装配的类到Spring容器中。

@MapperScan是扫描mapper类的注解，就不用在每个mapper类上加@MapperScan了。

这两个注解是可以同时使用的。