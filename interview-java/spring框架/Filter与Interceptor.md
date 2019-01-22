Filter与Interceptor

- Filter是基于函数回调的，而Interceptor则是基于Java反射和动态代理。
- Filter依赖于Servlet容器，而Interceptor不依赖于Servlet容器。
- Filter对几乎所有的请求起作用，而Interceptor只对Controller请求起作用。
