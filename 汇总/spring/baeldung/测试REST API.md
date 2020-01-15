# 测试REST API

REST API测试的关注点：
1. HTTP响应码
2. HTTP头
3. HTTP内容

## 测试状态码

	@Test
	public void givenUserDoesNotExists_whenUserInfoIsRetrieved_then404IsReceived()
	  throws ClientProtocolException, IOException {
	  
	    // Given
	    String name = RandomStringUtils.randomAlphabetic( 8 );
	    HttpUriRequest request = new HttpGet( "https://api.github.com/users/" + name );
	 
	    // When
	    HttpResponse httpResponse = HttpClientBuilder.create().build().execute( request );
	 
	    // Then
	    assertThat(
	      httpResponse.getStatusLine().getStatusCode(),
	      equalTo(HttpStatus.SC_NOT_FOUND));
	}

## 测试Media Type

	@Test
	public void
	givenRequestWithNoAcceptHeader_whenRequestIsExecuted_thenDefaultResponseContentTypeIsJson()
	  throws ClientProtocolException, IOException {
	  
	   // Given
	   String jsonMimeType = "application/json";
	   HttpUriRequest request = new HttpGet( "https://api.github.com/users/eugenp" );
	 
	   // When
	   HttpResponse response = HttpClientBuilder.create().build().execute( request );
	 
	   // Then
	   String mimeType = ContentType.getOrDefault(response.getEntity()).getMimeType();
	   assertEquals( jsonMimeType, mimeType );
	}