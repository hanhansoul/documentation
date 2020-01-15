# Spirng Boot使用Junit4单元测试进行RESTful接口测试

https://spring.io/guides/gs/testing-web/

- @WebMvcTest：for testing the controller layer
- @JsonTest：for testing the JSON marshalling and unmarshalling
- @DataJpaTest：for testing the repository layer
- @RestClientTests：for testing REST clients
- @AutoConfigureMockMvc：用于自动配置MockMvc

Spring Boot自动提供了一个TestRestTemplate类，用于测试restful接口。

# 测试web层

## @AutoConfigureMockMvc与@WebMvcTest

通过MockMvc，可以不需要启动服务器对HTTP请求和controller层进行测试。

通过@AutoConfigureMockMvc可以将MockMvc注入到测试实例中。

	@RunWith(SpringRunner.class)
	@SpringBootTest
	@AutoConfigureMockMvc
	public class ApplicationTest {

	    @Autowired
	    private MockMvc mockMvc;

	    @Test
	    public void shouldReturnDefaultMessage() throws Exception {
	        this.mockMvc.perform(get("/"))
	        	.andDo(print())
	        	.andExpect(status().isOk())
	            .andExpect(content().string(containsString("Hello World")));
	    }
	}

@WebMvcTest可以将进一步将测试限制在web层。@WebMvcTest可以值初始化web层，而不是整个上下文。

	@RunWith(SpringRunner.class)
	@WebMvcTest
	public class WebLayerTest {

	    @Autowired
	    private MockMvc mockMvc;

	    @Test
	    public void shouldReturnDefaultMessage() throws Exception {
	        this.mockMvc.perform(get("/"))
	        	.andDo(print())
	        	.andExpect(status().isOk())
	            .andExpect(content().string(containsString("Hello World")));
	    }
	}

在包含多个controller的应用中，还可以指定特定的controller初始化。如：@WebMvcTest(HomeController.class)。

## @MockBean

@MockBean：为测试类创建与注入一个类的mock。
