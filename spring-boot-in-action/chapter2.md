@SpringBootApplication开启了Spring的组件扫描和Spring Boot的自动配置功能。实际上，@SpringBootApplication将三个有用的注解组合在了一起。

1. Spring的@Configuration：标明该类使用Spring基于Java的配置。
2. Spring的@ComponentScan：启用组件扫描，这样你写的Web控制器类和其他组件才能被自动发现并注册为Spring应用程序上下文里的Bean。
3. Spring Boot的@EnableAutoConfiguration：这个小注解也可以称为
@Abracadabra，这一行配置开启了SpringBoot自动配置的魔力，让你不用再写成篇的配置了。

### Spring Boot项目构建过程解析

Spring Boot为Maven和Gradle项目提供了构建器。

## 使用起步依赖

### 指定基于功能的依赖

### 覆盖起步依赖引入的传递依赖

