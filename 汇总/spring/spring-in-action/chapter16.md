spring提供了两种方法将资源的java表述形式转换为发送给客户端的表述形式：
1. 内容协商：选择一个视图，能够将模型渲染为呈现给客户端的表述形式；
2. 消息转换器：通过一个消息转换器将控制器所返回的对象转换为呈现给客户端的表述形式。

### 协商资源表述

ContentNegotiatingViewResolver视图解析器

控制器的处理方法完成时，通常返回一个逻辑视图名，DispatcherServlet接下来会将视图的名字传递给一个视图解析器，要求它来帮助确定应该用哪个视图来渲染请求结果。

当处理REST时，视图不仅要匹配视图名，而且所选择的的视图要适合客户端。

#### 确定请求的媒体类型

#### 影响媒体类型的选择

#### ContentNegotiatingViewResolver的优势与限制

### 使用HTTP信息转换器

消息转换提供了一种更为直接的方式，能够将控制器产生的数据转换为服务于客户端的表述形式。

1. AtomFeedHttpMessageConverter：Rome Feed对象和Atom Feed之间的互相转换。
2. BufferedImageHttpMessageConverter：BufferedImages与图片二进制数据之间互相转换。
3. ByteArrayHttpMessageConverter：读取/写入字节数组，从所有媒体类型中读取，并以application/octet-stream格式写入。
4. FormHttpMessageConverter
5. Jaxb2RootElementHttpMessageConverter
6. MappingJacksonHttpMessageConverter
7. MappingJacksonHttpMessageConverter