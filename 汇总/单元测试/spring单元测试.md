# spring单元测试

- https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#boot-features-testing-spring-boot-applications
- http://www.importnew.com/29579.html
- https://blog.csdn.net/pengjunlee/article/details/80206615
- https://blog.csdn.net/chimmhuang/article/details/80460197

- @RunWith
- @WebMvcTest：@WebMvcTest注解主要用于controller层测试，只覆盖应用程序的controller层，HTTP请求和响应是Mock出来的，因此不会创建真正的连接。因此需要创建 MockMvc bean进行模拟接口调用。
- @SpringBootTest
- @MockBean

## MockMvc方法解析

- perform：执行一个RequestBuilder请求，会自动执行SpringMvc的流程并映射到相应的控制器执行处理。
- get：声明发送一个get请求方法。MockHttpServletRequestBuilder get(Sring urlTemple, Object... urlVariables)：根据url模板和url变量值得到一个GET请求方式的。另外提供了其他的请求方法，如：POST、PUT、DELETE等 
- param：添加request的参数，如上面发送请求的时候带上了pcode=root的参数。假如使用需要发送json数据格式的时候将不能使用这种方式。 
- andExpect：添加ResultMatcher验证规则，验证控制器执行完成后结果是否正确（对返回的数据进行的判断） 
- andDo：添加ResultHandler结果处理器，比如调试时打印结果到控制台（对返回的数据进行的判断） 
- andReturn：最后返回相应的MvcResult：然后进行自定义验证/进行下一步异常处理（对返回的数据进行的判断） 

	@Test
	public void getHello() throws Exception{
	    mvc.perform(MockMvcRequestBuilders.get("/hello")
	    		.accept(MediaType.APPLICATION_JSON))
	            .andExpect(status().isOk())
	            .andExpect(content().string(equalTo("Hello World")));
	}

## 自动配置REST客户端

@RestClientTest：自动配置JSON支持，配置RestTemplateBuilder，支持MockRestServiceServer。一般的@Component类不会被装载。

	@RunWith(SpringRunner.class)
	@RestClientTest(RemoteVehicleDetailsService.class)
	public class ExampleRestClientTest {

		@Autowired
		private RemoteVehicleDetailsService service;

		@Autowired
		private MockRestServiceServer server;

		@Test
		public void getVehicleDetailsWhenResultIsSuccessShouldReturnDetails()
				throws Exception {
			this.server
				.expect(requestTo("/greet/details"))
				.andRespond(withSuccess("hello", MediaType.TEXT_PLAIN));
			
			String greeting = this.service.callRestService();
			assertThat(greeting).isEqualTo("hello");
		}

	}

## TestRestTemplate

TestRestTemplate是集成测试中RestTemplate的替代品。

	@RunWith(SpringRunner.class)
	@SpringBootTest(webEnvironment = WebEnvironment.RANDOM_PORT)
	public class SampleWebClientTests {

		@Autowired
		private TestRestTemplate template;

		@Test
		public void testRequest() {
			HttpHeaders headers = this.template
									.getForEntity("/example", String.class)
									.getHeaders();
			assertThat(headers.getLocation()).hasHost("other.example.com");
		}

		@TestConfiguration
		static class Config {
			@Bean
			public RestTemplateBuilder restTemplateBuilder() {
				return new RestTemplateBuilder()
						.setConnectTimeout(Duration.ofSeconds(1))
						.setReadTimeout(Duration.ofSeconds(1));
			}
		}

	}