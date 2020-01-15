# REST-assured

- https://www.baeldung.com/rest-assured-tutorial
- https://www.baeldung.com/rest-assured-response

## 简介

REST-assured用于简化REST API的测试与验证工作。

## 简单示例

响应路径http://localhost:8080/events?id=390

JSON格式响应：

	{
	    "id": "390",
	    "data": {
	        "leagueId": 35,
	        "homeTeam": "Norway",
	        "visitingTeam": "England",
	    },
	    "odds": [{
	        "price": "1.30",
	        "name": "1"
	    },
	    {
	        "price": "5.25",
	        "name": "X"
	    }]
	}

单元测试：

	@Test
	public void givenUrl_whenSuccessOnGetsResponseAndJsonHasRequiredKV_thenCorrect() {
		get("/events?id=390").then().statusCode(200).assertThat()
	   		.body("data.leagueId", equalTo(35)); 
	}

	@Test
	public void givenUrl_whenJsonResponseHasArrayWithGivenValuesUnderKey_thenCorrect() {
	   get("/events?id=390").then().assertThat()
	      .body("odds.price", hasItems("1.30", "5.25"));
	}

## 匿名JSON根验证

匿名JSON根是指不包含键值对但仍然合法的JSON数据，如[1, 2, 3]。

可以通过$符号或空字符串访问。

	when().get("/json").then().body("$", hasItems(1, 2, 3));

	when().get("/json").then().body("", hasItems(1, 2, 3));

## 浮点数

JSON中的浮点数类型为float，不能被转换为double类型。必须通过f标识进行转换。

	{
	    "odd": {
	        "price": "1.30",
	        "ck": 12.2,
	        "name": "1"
	    }
	}

	get("/odd").then().assertThat().body("odd.ck", equalTo(12.2));	// 失败

	get("/odd").then().assertThat().body("odd.ck", equalTo(12.2f));