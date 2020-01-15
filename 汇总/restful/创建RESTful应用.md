# RESTful接口

https://spring.io/guides/gs/consuming-rest/

## 创建RESTful接口

传统MVC的controller与RESTful应用的controller的区别在于创建HTTP响应体的不同：
1. 传统MVC：依赖于视图解析器渲染并返回视图；
2. RESTful：controller只是返回数据的对象，如JSON等。

MappingJacksonHttpMessageConverter提供了自动将POJO对象转换为JSON对象的方法。

## 消费RESTful接口

RestTemplate为RESTful接口访问提供了方法。