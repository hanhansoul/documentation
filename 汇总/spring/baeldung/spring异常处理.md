## @ExceptionHandler

@ExceptionHandler是Controller级别的注解，用于在Controller内方法上注解，只能处理该Controller的异常。

	public class FooController {
	     
	    //...
	    @ExceptionHandler({ CustomException1.class, CustomException2.class })
	    public void handleException() {
	        //
	    }
	}

## HandlerExceptionResolver

定义一个HandlerExceptionResolver，用于处理应用抛出的所有异常。能够为REST API实现一个统一异常处理机制。

### ExceptionHandlerExceptionResolver

由DispatcherServlet默认提供，是@ExceptionHandler实现的核心组件。

### DefaultHandlerExceptionResolver

由DispatcherServlet默认提供，用于将标准的Spring异常解析为对应的HTTP状态码。

该解析器不会设置响应的body中任何信息。对于REST API，状态码不能够为客户端提供足够信息。

- BindException：400 (Bad Request)
- ConversionNotSupportedException：500 (Internal Server Error)
- HttpMediaTypeNotAcceptableException：406 (Not Acceptable)
- HttpMediaTypeNotSupportedException：415 (Unsupported Media Type)
- HttpMessageNotReadableException：400 (Bad Request)
- HttpMessageNotWritableException：500 (Internal Server Error)
- HttpRequestMethodNotSupportedException：405 (Method Not Allowed)
- MethodArgumentNotValidException：400 (Bad Request)
- MissingServletRequestParameterException：400 (Bad Request)
- MissingServletRequestPartException：400 (Bad Request)
- NoSuchRequestHandlingMethodException：404 (Not Found)
- TypeMismatchException：400 (Bad Request)

### ResponseStatusExceptionResolver

由DispatcherServlet默认提供，通过@ResponseStatus注解将异常映射被对应的HTTP状态码。

	@ResponseStatus(value = HttpStatus.NOT_FOUND)
	public class ResourceNotFoundException extends RuntimeException {
	    public ResourceNotFoundException() {
	        super();
	    }
	    public ResourceNotFoundException(String message, Throwable cause) {
	        super(message, cause);
	    }
	    public ResourceNotFoundException(String message) {
	        super(message);
	    }
	    public ResourceNotFoundException(Throwable cause) {
	        super(cause);
	    }
	}

### SimpleMappingExceptionResolver与AnnotationMethodHandlerExceptionResolver

将异常映射为对应的view名称。已被弃用。

### 自定义的HandlerExceptionResolver

略。

## @ControllerAdvice

@ControllerAdvice提供了全局异常处理器，同时返回ResponseEntity类型数据。


	@ControllerAdvice
	public class RestResponseEntityExceptionHandler 
	  extends ResponseEntityExceptionHandler {
	 
	    @ExceptionHandler(value 
	      = { IllegalArgumentException.class, IllegalStateException.class })
	    protected ResponseEntity<Object> handleConflict(
	      RuntimeException ex, WebRequest request) {
	        String bodyOfResponse = "This should be application specific";
	        return handleExceptionInternal(ex, bodyOfResponse, 
	          new HttpHeaders(), HttpStatus.CONFLICT, request);
	    }
	}

- 能够完全控制响应的body与状态码
- 将多种异常映射到同一个方法上
- 充分利用了RESTful风格的ResponseEntity响应

## ResponseStatusException

RESTful应用能够通过响应中的HTTP状态码将请求是否成功返回给客户端。

	@GetMapping(value = "/{id}")
	public Foo findById(@PathVariable("id") Long id, HttpServletResponse response) {
	    try {
	        Foo resourceById = RestPreconditions.checkFound(service.findOne(id));
	 
	        eventPublisher.publishEvent(new SingleResourceRetrievedEvent(this, response));
	        return resourceById;
	    } catch (MyResourceNotFoundException exc) {
	         throw new ResponseStatusException(
	           HttpStatus.NOT_FOUND, "Foo Not Found", exc);
	    }
	}

ResponseStatusException优势：
1. 不需要自定义异常类
2. 一种异常对应多个状态码
3. 便于控制

### ResponseStatus

通过@ResponseStatus注解能够为HTTP响应设置状态码和原因。但@ResponseStatus会将异常与错误信息紧耦合，如所有的ActorNotFoundException异常都会返回相同的状态码和错误信息。

	@ResponseStatus(code = HttpStatus.NOT_FOUND, reason = "Actor Not Found")
	public class ActorNotFoundException extends Exception {
	    // ...
	}

### ResponseStatusException

	ResponseStatusException(HttpStatus status)
	ResponseStatusException(HttpStatus status, java.lang.String reason)
	ResponseStatusException(
	  HttpStatus status, 
	  java.lang.String reason, 
	  java.lang.Throwable cause
	)

### ResponseStatusException优势

- 同种类型的异常能够被分别处理
- 避免创建新的异常类
- 通过编程创建与控制异常处理

## 示例	

### 生成ResponseStatusException

Spring Boot提供了默认的/error映射，返回一个JSON响应，包含HTTP状态码和异常信息。

	@GetMapping("/actor/{id}")
	public String getActorName(@PathVariable("id") int id) {
	    try {
	        return actorService.getActor(id);
	    } catch (ActorNotFoundException ex) {
	        throw new ResponseStatusException(
	          HttpStatus.NOT_FOUND, "Actor Not Found", ex);
	    }
	}

	$ curl -i -s -X GET http://localhost:8080/actor/8
	HTTP/1.1 404
	Content-Type: application/json;charset=UTF-8
	Transfer-Encoding: chunked
	Date: Sun, 28 Jan 2018 19:48:10 GMT
	 
	{
	    "timestamp": "2018-01-28T19:48:10.471+0000",
	    "status": 404,
	    "error": "Not Found",
	    "message": "Actor Not Found",
	    "path": "/actor/8"
	}

## Spring Secutiry中的拒绝访问控制

## SpringBoot支持