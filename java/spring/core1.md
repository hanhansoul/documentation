## 初始化对象

1. 构造器初始化
2. 静态工厂方法初始化
3. 实例工厂方法初始化

## 依赖注入

基本的对象初始化过程是不包含参数的，而大多时候，对象初始化时都需要传入参数或其他对象引用，此时就需要利用依赖注入。

依赖注入（DI）是对象定义自身依赖对象的过程。

依赖注入的方式：
1. 通过构造器参数
2. 通过工厂方法参数
3. 通过setter方法参数

依赖注入的本质是控制反转，对象的创建与初始化不是通过直接调用类的构造函数实现的，而是通过spring容器完成相关工作。

### 基于构造器的依赖注入

xml配置文件中，构造器参数通过<constructor-arg>标签注入。

### 基于setter函数的依赖注入

在调用了不含参数的构造器或不含参数的静态工厂方法创建对象后，容器通过调用setter方法实现对象的初始化。

xml配置文件中，setter函数参数通过<property>标签注入。

### 依赖解析过程

1. 根据配置文件信息，创建并初始化ApplicationContext。配置信息可以通过xml文件、java代码或注解提供。

For each bean, its dependencies are expressed in the form of properties, constructor arguments,
or arguments to the static-factory method( if you use that instead of a normal constructor).
These dependencies are provided to the bean, when the bean is actually created.

Each property or constructor argument is an actual definition of the value to set, or a reference
to another bean in the container.

Each property or constructor argument that is a value is converted from its specified format to
the actual type of that property or constructor argument. By default, Spring can convert a value
supplied in string format to all built-in types, such as int, long, String, boolean, and so forth.

## bean的作用范围

## 

