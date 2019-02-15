# Spring framework core

## IoC容器

控制反转（IoC）也被称为依赖注入（DI）。

这是对象定义它们的依赖关系的一个过程。

- 构造器参数
- 工厂方法参数
- 属性setter

容器在创建bean的时候注入依赖关系。

这个过程与bean由自身通过构造器方法或Service Location模式机制来控制自身的初始化与依赖关系是完全反转的。

org.springframework.beans and org.springframework.context包是spring控制反转的核心。

- BeanFactory provides the configuration framework and basic functionality
- ApplicationContext adds more enterprise-specific functionality. 

ApplicationContext子类

- ClassPathXmlApplicationContext
- FileSystemXmlApplicationContext

## Bean

- 包限定类名
- Bean行为配置元素：scope、lifecycle、callback
- 其他Bean的引用


## 依赖

### 依赖注入

依赖注入的优势：

- 代码更加简洁
- 解耦合
- 便于单元测试（使用stub/mock）

依赖注入有两种方式：

- 基于构造器的依赖注入
- 依赖setter的依赖注入

#### 基于构造器的依赖注入

通过构造器参数注入依赖，与使用静态工厂方法的方式类似。

**构造器参数解析**

Constructor argument resolution matching occurs using the argument’s type.

	package x.y;

	public class Foo {

	    public Foo(Bar bar, Baz baz) {
	        // ...
	    }
	}

	<beans>
	    <bean id="foo" class="x.y.Foo">
	        <constructor-arg ref="bar"/>
	        <constructor-arg ref="baz"/>
	    </bean>

	    <bean id="bar" class="x.y.Bar"/>

	    <bean id="baz" class="x.y.Baz"/>
	</beans>

#### 依赖setter的依赖注入

#### 依赖解析过程

1. 根据配置元数据创建并初始化ApplicationContext，用于描述所有的bean。配置元数据可以通过xml、java代码或注解方式指定。
2. 对于每个bean，其依赖关系可以通过类属性、构造方法参数或静态工厂方法参数提供。这些依赖关系将在bean创建时提供。
3. 每一个属性或构造方法参数可以是一个值，也可以是容器中另一个bean的引用。
4. 属性或构造方法参数中的值参数，可以通过spring转换为正确的类型，参数可以被转换为int、long、boolean或String类型