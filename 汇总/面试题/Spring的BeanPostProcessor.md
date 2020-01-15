# Spring的BeanPostProcessor

https://zhuanlan.zhihu.com/p/40871948

BeanPostProcessor接口是Spring提供的众多接口之一，他的作用主要是如果我们需要在Spring容器完成Bean的实例化、配置和其他的初始化前后添加一些自己的逻辑处理，我们就可以定义一个或者多个BeanPostProcessor接口的实现，然后注册到容器中。

## Spring 中如何使用 BeanPostProcessor 处理实例化对象

### Spring中Bean的实例化过程

1. 实例化bean对象
2. 设置对象属性
3. 检查Aware相关接口并设置相关依赖
4. BeanPostProcessor前置处理
5. 检查是否是InitializingBean以决定是否调用afterPropertiesSet方法
6. 检查是否配置有自定义的init-method
7. BeanPostProcessor后置处理
8. 注册必要的destroy回调接口
9. 使用中
10. 是否实现DisposableBean接口
11. 是否配置有自定义的destroy方法

Spring中的BeanPostProcessor在实例化过程处于的位置分为两部分--前置处理和后置处理，而BeanPostProcessor接口也提供了两个可实现的方法。

	public interface BeanPostProcessor {
	    @Nullable
	    default Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
	        return bean;
	    }

	    @Nullable
	    default Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
	        return bean;
	    }
	}

由方法名字也可以看出，前者在实例化及依赖注入完成后、在任何初始化代码（比如配置文件中的init-method）调用之前调用；后者在初始化代码调用之后调用。此处需要注意的是：接口中的两个方法都要将传入的bean返回，而不能返回null，如果返回的是null那么我们通过getBean()方法将得不到目标。

### 自定义类来实现 BeanPostProcessor接口

	public class MyBeanPostProcessor implements BeanPostProcessor {

	    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
	        System.out.println("bean 对象初始化之前······");
	        return bean;
	    }

	    public Object postProcessAfterInitialization(final Object beanInstance, String beanName) throws BeansException {
	        // 为当前 bean 对象注册监控代理对象，负责增强 bean 对象方法的能力
	        Class beanClass = beanInstance.getClass();
	        if (beanClass == ISomeService.class) {
	            Object proxy = Proxy.newProxyInstance(beanInstance.getClass().getClassLoader(),
	                    beanInstance.getClass().getInterfaces(),
	                    new InvocationHandler() {
	                        public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
	                            System.out.println("ISomeService 中的 doSome() 被拦截了···");
	                            String result = (String) method.invoke(beanInstance, args);
	                            return result.toUpperCase();
	                        }
	                    });
	            return proxy;
	        }
	        return beanInstance;
	    }
	}

### 在配置文件中注册被监控的实现类以及注册代理实现类

	public interface BaseService {

	    String doSomething();

	    String eat();
	}

	public class ISomeService implements BaseService {

	    public String doSomething() {
	        return "Hello AlanShelby"; // 增强效果：返回内容全部大写
	    }

	    public String eat() {
	        return "eat food";
	    }
	}

	<!-- 注册 bean：被监控的实现类 -->
	<bean id="iSomeService" class="top.alanshelby.service.impl.ISomeService"></bean>

	<!-- 注册代理实现类 -->
	<bean class="top.alanshelby.utils.MyBeanPostProcessor"></bean>

## 手动实现 BeanPostProcessor

### 对 BeanFactory 的拓展