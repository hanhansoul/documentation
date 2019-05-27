DefaultListableBeanFactory

DefaultListableBeanFactory是bean加载的核心，注册和加载bean的默认实现。XmlBeanFactory继承自DefaultListableBeanFactory，使用了自定义的XML读取器XmlBeanDefinitionReader。

XmlBeanDefinitionReader

XmlBeanDefinitionReader用于读取、解析和注册XML资源文件。

1. 通过继承自AbstractBeanDefinitionReader中的方法，使用ResourceLoader将资源文件路径转换为对应的资源文件。
2. 通过DocumentLoader对Resource文件进行转换，将Resource文件转换为Document文件。
3. 通过实现接口BeanDefinitionDocumentReader的DefaultBeanDefinitionDocumentReader类对Document进行解析，并使用BeanDefinitionParserDelegate对Element进行解析。

## 容器的基础XmlBeanFactory

### 配置文件封装

Spring的配置文件读取是通过ClassPathResource进行封装的。

InputStreamSource封装任何能返回InputStream的类，如File、Classpath下的资源和Byte数组等。getInputStream()方法返回一个新的InputStream对象。

Resource接口抽象了所有Spring内部使用的底层资源：File、URL、Classpath等。

对于不同来源的资源文件都有相应的Resource实现：
- 文件（FileSystemResource）
- Classpath资源（ClassPathResource）
- URL资源（UrlResource）
- InputStream资源（InputStreamResource）
- Byte数组（ByteArrayResource）

Resource接口为所有资源文件提供了统一处理方法。

通过Resource相关类完成对配置文件进行封装后，对配置文件的读取工作就交给了XmlBeanDefinitionReader处理了。

### 加载Bean

loadBeanDefinitions()方法时资源加载的切入点。

1. 封装资源文件。进入XmlBeanDefinitionReader后首先对参数Resource使用EncodedResource类进行封装。
2. 获取输入流。从Resource中获取对应的InputStream并构造InputSource。
3. 通过构造的InputSource实例和Resource实例继续调用函数doLoadBeanDefinitions。

doLoadBeanDefinitions()

1. 获取对XML文件的验证模式。
2. 加载XML文件，并得到对应的Document。
3. 根据返回的Document注册Bean信息。

## 获取XML的验证模式

### DTD和XSD区别

DTD即文档类型定义，是一种XML约束模式语言，是XML文件的验证机制，属于XML文件组成部分。通过比对XML文件与提供的DTD文件可以判断XML文件是否符合规范。

XML Schema语言就是XSD，描述了XML文档的结构，


## 获取Document

## 解析与注册BeanDefinitions

### profile属性使用

profile属性使我们可以同时在配置文件中部署两套配置来适用于生产环境和开发环境，这样可以方便地进行切换开发、部署环境，最常用的就是更换不同的数据库。

### 解析并注册BeanDefinition

