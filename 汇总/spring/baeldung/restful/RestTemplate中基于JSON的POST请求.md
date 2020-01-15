# RestTemplate中基于JSON的POST请求

## 示例

	public class Person {
	    private Integer id;
	    private String name;
	 
	    // standard constructor, getters, setters
	}

	public interface PersonService {
	 
	    public Person saveUpdatePerson(Person person);
	    public Person findPersonById(Integer id);
	}

## REST API

	@PostMapping(value = "/createPerson", consumes = "application/json", produces = "application/json")
	public Person createPerson(@RequestBody Person person) {
	    return personService.saveUpdatePerson(person);
	}
	 
	@PostMapping(value = "/updatePerson", consumes = "application/json", produces = "application/json")
	public Person updatePerson(@RequestBody Person person, HttpServletResponse response) {
	    response.setHeader("Location", ServletUriComponentsBuilder.fromCurrentContextPath()
	      .path("/findPerson/" + person.getId()).toUriString());
	     
	    return personService.saveUpdatePerson(person);
	}

当通过JSON格式post数据时，需要将@RequestMapping的consumes属性赋值为application/json。

- @RequestBody：参数与HTTP请求的body绑定
- @ResponseBody
- @RestController

## 使用RestTemplate

	@BeforeClass
	public static void runBeforeAllTestMethods() {
	    createPersonUrl = "http://localhost:8082/spring-rest/createPerson";
	    updatePersonUrl = "http://localhost:8082/spring-rest/updatePerson";
	 
	    restTemplate = new RestTemplate();
	    headers = new HttpHeaders();
	    headers.setContentType(MediaType.APPLICATION_JSON);
	    personJsonObject = new JSONObject();
	    personJsonObject.put("id", 1);
	    personJsonObject.put("name", "John");
	}

## postForObject

	@Test
	public void givenDataIsJson_whenDataIsPostedByPostForObject_thenResponseBodyIsNotNull()
	  throws IOException {
	  	// 将对象封装在HttpEntity中，并添加一个header。
	    HttpEntity<String> request = new HttpEntity<String>(personJsonObject.toString(), headers);
	    // 通过RestTemplate发送请求，并以String类型返回response的body。
	    String personResultAsJsonStr = restTemplate.postForObject(createPersonUrl, request, String.class);
	    JsonNode root = objectMapper.readTree(personResultAsJsonStr);
	     
	    assertNotNull(personResultAsJsonStr);
	    assertNotNull(root);
	    assertNotNull(root.path("name").asText());
	}

以Person类型返回response。

	Person person = restTemplate.postForObject(createPersonUrl, request, Person.class);
	 
	assertNotNull(person);
	assertNotNull(person.getName());

createPersonUrl创建的response的body是JSON格式的，postForObject方法能够自动将其转换为responseType指定的类型。

## postForEntity

postForEntity返回的response是一个ResponseEntity对象。

	@Test
	public void givenDataIsJson_whenDataIsPostedByPostForEntity_thenResponseBodyIsNotNull()
	  throws IOException {
	    HttpEntity<String> request = new HttpEntity<String>(personJsonObject.toString(), headers);
	     
	    ResponseEntity<String> responseEntityStr = restTemplate.
	      postForEntity(createPersonUrl, request, String.class);
	    JsonNode root = objectMapper.readTree(responseEntityStr.getBody());
	  
	    assertNotNull(responseEntityStr.getBody());
	    assertNotNull(root.path("name").asText());
	}

或

	ResponseEntity<Person> responseEntityPerson = restTemplate.
		postForEntity(createPersonUrl, request, Person.class);
	  
	assertNotNull(responseEntityPerson.getBody());
	assertNotNull(responseEntityPerson.getBody().getName());

## postForLocation

postForLocation返回header中Location的值。

	response.setHeader("Location", ServletUriComponentsBuilder.fromCurrentContextPath()
		.path("/findPerson/" + person.getId()).toUriString());

	@Test
	public void givenDataIsJson_whenDataIsPostedByPostForLocation_thenResponseBodyIsTheLocationHeader() 
	  throws JsonProcessingException {
	    HttpEntity<String> request = new HttpEntity<String>(personJsonObject.toString(), headers);
	    URI locationHeader = restTemplate.postForLocation(updatePersonUrl, request);
	     
	    assertNotNull(locationHeader);
	}