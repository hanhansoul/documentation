# Spring Data JPA事务

## 通过注解配置事务

在@Configuration注解的类上使用@EnableTransactionManagement配置事务。

	@Configuration
	@EnableTransactionManagement
	public class PersistenceJPAConfig{
	 
	   @Bean
	   public LocalContainerEntityManagerFactoryBean
	     entityManagerFactoryBean(){
	      //...
	   }
	 
	   @Bean
	   public PlatformTransactionManager transactionManager(){
	      JpaTransactionManager transactionManager
	        = new JpaTransactionManager();
	      transactionManager.setEntityManagerFactory(
	        entityManagerFactoryBean().getObject() );
	      return transactionManager;
	   }
	}

对于包含spring-data-\*或spring-tx等依赖的Spring Boot项目会默认配置事务管理。

## @Transactional注解

@Transactional可以注解在类或者方法上。

	@Service
	@Transactional
	public class FooService {
	    //...
	}

- 传播形式
- 隔离级别
- 超时时间
- 只读标识
- 回滚