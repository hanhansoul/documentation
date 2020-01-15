# TestRestTemplate

## TestRestTemplate功能

### 通过基本的凭据进行认证

所有请求都会通过指定凭据进行认证。

	TestRestTemplate testRestTemplate = new TestRestTemplate("user", "passwd");
	ResponseEntity<String> response = testRestTemplate.
	  getForEntity(URL_SECURED_BY_AUTHENTICATION, String.class);
	  
	assertThat(response.getStatusCode(), equalTo(HttpStatus.OK));

### 