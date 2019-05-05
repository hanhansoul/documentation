https://my.oschina.net/hapier/blog/699193
https://juejin.im/post/5b2ddbcef265da59a76c92a4

## Servlet

servlet是一种运行服务器端的java应用程序，具有独立于平台和协议的特性，并且可以动态的生成web页面，它工作在客户端请求与服务器响应的中间层。

1. 客户端发送请求至服务器端；
2. 服务器将请求信息发送至Servlet；
3. Servlet生成响应内容并将其传给服务器。响应内容动态生成，通常取决于客户端的请求；
4. 服务器将响应返回给客户端。

在Web应用程序中，一个Servlet在一个时刻可能被多个用户同时访问。这时Web容器将为每个用户创建一个线程来执行Servlet。如果Servlet不涉及共享资源的问题，不必关心多线程问题。但如果Servlet需要共享资源，需要保证Servlet是线程安全的。

## Filter

filter是一个可以复用的代码片段，可以用来转换HTTP请求、响应和头信息。filter不能像servelet一样产生一个 请求或者响应，它只是修改请求或响应。

servlet中的过滤器filter是实现了javax.servlet.Filter接口的服务器端程序，主要的用于过滤字符编码、处理业务逻辑判断等。

其工作原理是，预先配置好需要拦截的客户端请求与返回的响应，它会帮你拦截到指定的请求或响应，此时就可以对请求或响应统一设置编码，处理逻辑判断（如用户是否已经登陆、有没有权限访问该页面等等）。

filter随web应用启动而启动的，只需要初始化一次，只有当web应用停止或重新部署的时候才销毁。filter可以看做是Servlet的一种“变种”，主要用于对用户请求进行预处理，也可以对HttpServletResponse进行后处理，是个典型的处理链。其与Servlet的区别在于：它不能直接向用户生成响应。完整的流程是：Filter对用户请求进行预处理，接着将请求交给Servlet进行处理并生成响应，最后Filter再对服务器响应进行后处理。

filter的应用：

1. 在HttpServletRequest到达Servlet之前，拦截客户的HttpServletRequest。
2. 根据需要检查HttpServletRequest，也可以修改HttpServletRequest头和数据。
3. 在HttpServletResponse到达客户端之前，拦截HttpServletResponse。
4. 根据需要检查HttpServletResponse，也可以修改HttpServletResponse头和数据。

Filter接口，实现doFilter()方法。

	public class LogFilter implements Filter {

		//FilterConfig可用于访问Filter的配置信息
		private FilterConfig config;

		public void init(FilterConfig config) {
			this.config = config;
		}

		public void destroy() {
			this.config = null;
		}

		public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException,
		ServletException {
			//---------下面代码用于对用户请求执行预处理---------
			//获取ServletContext对象，用于记录日志
			ServletContext context = this.config.getServletContext();
			long before = System.currentTimeMillis();
			System.out.println("开始过滤...");
			//将请求转换成HttpServletRequest请求
			HttpServletRequest hrequest = (HttpServletRequest) request;
			//记录日志
			context.log("Filter已经截获到用户的请求地址： " + hrequest.getServletPath());
			//Filter只是链式处理，请求依然放行到目的地址
			chain.doFilter(request, response);
			//---------下面代码用于对服务器响应执行后处理---------
			long after = System.currentTimeMillis();
			//记录日志
			context.log("过滤结束");
			//再次记录日志
			context.log("请求被定位到" + hrequest.getRequestURI() + "所花的时间为: " + (after - before));
		}
	}

web.xml配置文件

	<filter>
	    <filter-name>logfilter</filter-name>
	    <filter-class>com.mine.test.LogFilter</filter-class>
	</filter>
	<filter-mapping>
	    <filter-name>logfilter</filter-name>
	    <!--配置过滤的范围 后缀符合即过滤 此处为全部过滤-->
	    <url-pattern>/*</url-pattern>
	</filter-mapping>

## Listener

监听器，从字面上可以看出listener主要用来监应用的。

通过listener可以监听web服务器中某一个执行动作，并根据其要求作出相应的响应。通俗的语言说就是在application，session，request三个对象创建消亡或者往其中添加修改删除属性时自动执行代 码的功能组件。比如spring的总监听器会在服务器启动的时候实例化我们配置的bean对象、hibernate 的session的监听器会监听session的活动和生命周期，负责创建，关闭session等活动。

Servlet的监听器Listener，它是实现了javax.servlet.ServletContextListener 接口的服务器端程序，它也是随web应用的启动而启动，只初始化一次，随web应用的停止而销毁。主要作用是：做一些初始化的内容添加工作、设置一些基本的内容、比如一些参数或者是一些固定的对象等等。

## interceptor

是在面向切面编程的，就是在你的service或者一个方法，前调用一个方法，或者在方法后调用一个方法，是基于JAVA的反射机制。比如动态代理就是拦截器的简单实现，在你调用方法前打印出字符串（或者做其它业务逻辑的操作），也可以在你调用方法后打印出字符串，甚至在你抛出异常的时候做业务逻辑的操作。

实现AOP的基础。

servlet、filter、listener是配置到web.xml中（web.xml 的加载顺序是：context-param -> listener -> filter -> servlet ），interceptor不配置到web.xml中，struts的拦截器配置到struts.xml中。spring的拦截器配置到spring.xml中。

## 生命周期

## 职责

### servlet

- 创建并返回一个包含基于客户请求性质的动态内容的完整的html页面；
- 创建可嵌入到现有的html页面中的一部分html页面（html片段）；
- 读取客户端发来的隐藏数据；
- 读取客户端发来的显示数据；
- 与其他服务器资源（包括数据库和java的应用程序）进行通信；
- 通过状态代码和响应头向客户端发送隐藏数据。

## 区别
