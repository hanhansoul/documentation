# servlet/filter/listener/interceptor区别与联系

https://www.cnblogs.com/doit8791/p/4209442.html

## servlet

Servlet是一种运行在服务端的Java程序，可以用来收集来自用户请求（Request），并向客户端返回响应（Response）。

Servlet的任务有：

1. 接收请求数据：我们都知道客户端请求会被封装成HttpServletRequest对象，里面包含了请求头、参数等各种信息。
2. 处理请求：通常我们会在service、doPost或者doGet方法进行接收参数，并且调用业务层（service）的方法来处理请求。
3. 完成响应：处理完请求后，我们一般会转发（forward）或者重定向（redirect）到某个页面，转发是HttpServletRequest中的方法，重定向是HttpServletResponse中的方法，两者是有很大区别的。

Servlet声明周期：
1. init()初始化
2. service()处理客户端请求
3. destroy()销毁

Servlet的创建：Servlet可以在第一次接收请求时被创建，也可以在在服务器启动时就被创建，这需要在web.xml的<\servlet>中添加一条配置信息<\load-on-startup>5<\/load-on-startup>，当值为0或者大于0时，表示容器在应用启动时就加载这个servlet，当是一个负数时或者没有指定时，则指示容器在该servlet被请求时才加载。 

ServletRequest类为请求类，ServletResponse类为响应类。

Servlet内置对象：
1. HttpServletRequest
2. HttpServletResponse
3. HttpSession
4. Throwable
5. PageContext
6. ServletContext
7. ServletConfig

## filter

filter与servlet区别：servlet主要负责处理请求，而filter主要负责拦截请求和过滤，并将过滤后的请求发送给servlet。

filter是一个可以复用的代码片段，可以用来转换HTTP请求、响应和头信息。Filter不像Servlet，它不能产生一个请求或者响应，它只是修改对某一资源的请求，或者修改从某一的响应。Servlet中的过滤器Filter是实现了javax.servlet.Filter接口的服务器端程序，主要的用途是过滤字符编码、做一些业务逻辑判断等。其工作原理是，只要你在web.xml文件配置好要拦截的客户端请求，它都会帮你拦截到请求，此时你就可以对请求或响应(Request、Response)统一设置编码，简化操作；同时还可进行逻辑判断，如用户是否已经登陆、有没有权限访问该页面等等工作。它是随你的web应用启动而启动的，只初始化一次，以后就可以拦截相关请求，只有当你的web应用停止或重新部署的时候才销毁。Filter可认为是Servlet的一种“变种”，它主要用于对用户请求进行预处理，也可以对HttpServletResponse进行后处理，是个典型的处理链。它与Servlet的区别在于：它不能直接向用户生成响应。完整的流程是：Filter对用户请求进行预处理，接着将请求交给Servlet进行处理并生成响应，最后Filter再对服务器响应进行后处理。

Filter声明周期：
1. init()方法初始化
2. doFilter()方法拦截请求
3. destroy()销毁

 Filter拦截客户端请求，按需进行处理后，转发给相应的Servlet去处理客户端请求。

## Listener

监听 ServletContext、HttpSession、ServletRequest 三个对象的生命周期和属性变化事件

1. ServletContext代表应用，一个应用对应一个ServletContext对象
2. HttpSession代表Session，一个Session代表一个会话，一个浏览器对应一个会话
3. ServletRequest代表请求，一个请求就会产生一个ServletRequest对象

按数量来分：ServletRequest > HttpSession > ServletContext