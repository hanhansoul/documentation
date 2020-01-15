
用于注解实例方法，每次测试前执行一次。

- @Before
- @After

用于注解公共静态方法，每一个测试类仅执行一次。

- @BeforeClass：在第一个测试之前执行
- @AfterClass：在最后一个测试之后执行

## 使用断言验证测试结果

Assert类提供了一系列静态方法来验证期望值和真实值是否相同。

- assertTrue(assert condition);
- assertTrue(failure message, assert condition);
- assertFalse(boolean condition);
- assertFalse(failure message, boolean condition);
- assertNull
- assertNotNull
- assertEquals(string message, object expected, object actual)：判断对象的值是否相同
- assertEquals(object expected, object actual);
- assertEquals(primitive expected, primitive actual);
- assertSame(object expected, object actual)：判断对象的引用是否相同
- assertNotSame

## 测试异常处理

通过@Test注解的expected=<Exception class name>.class参数实现异常测试。

	@Test(expected=RuntimeException.class)
	public void test_exception_condition() {
		throw new RuntimeException();
	}

## @RunWith

测试运行器（test runner）负责执行JUnit测试。

@RunWith注解接受一个类名，该类是org.junit.runner.Runner的子类。默认采用JUnit4类运行器。

Suite是一个标准运行器，用于构建一个包含多个包的测试的suite。

JUnit Runner是一个继承了JUnit抽象类Runner的类。Runners用于执行测试类。可以通过@Runwith注解来设置一个Runner要执行的测试。

JUnit测试的启动是通过使用JUnitCore类实现的。你也可以通过使用命令行，或者使用JUnitCore各种run()方法中的一个来启动。

然后JUnitCore使用反射来为传递的测试类找到一个合适的Runner。其中的一个步骤就是查找测试类上的@RunWith注解。如果没有找到其他的Runner，将使用默认的Runner(BlockJUnit4ClassRunner)。Runner将被实例化，测试类会被传递给Runner。然后Runner就会实例化传进来的测试类，然后运行。

- Spring的SpringJUnit4ClassRunner可以帮助你测试Spring框架应用。它可以让你在测试类中使用依赖注入，也可以创建支持事务的测试方法。
- SpringRunner是SpringJUnit4ClassRunner的简写。
- Mockito提供了MockitoJUnitRunner，用于自动地初始化mock。

## Suite

一个Suite能够打包和执行多个测试。

	import org.junit.runner.RunWith;
	import org.junit.runners.Suite;
	@RunWith(Suite.class)
	@Suite.SuiteClasses({AssertTest.class, 
		TestExecutionOrder.class,
		Assumption.class})
	public class TestSuite {
	}

## assertThat

### is、not、equalTo
	
	int myAge = 30;
    assertThat(myAge, equalTo(30));
    assertThat(myAge, is(30));
    assertThat(myAge, not(equalTo(33)));
    assertThat(myAge, is(not(33)));

### either、both、anyOf、allOf

- either().or()
- both().and()
- anyOf()
- allOf()

### hasItem、hasItems

### startsWith、endsWith、containsString
	
	String myName = "John Jr Dale";
	assertThat(myName, startsWith("John"));
	assertThat(myName, endsWith("Dale"));
	assertThat(myName, containsString("Jr"));

## Mockito

Mockito用于模拟对象的创建、验证与打桩。

### @Mock、mock()与@MockBean

@Mock与mock()等价，都来自于Mockito库，使用前需要进行初始化。

@MockBean属于spring-boot-test库，本质是一个spring boot类型，通过ApplicationContext内容加载。

### 对象mock

### 方法打桩

方法stub是指为方法调用与模拟方法行为设置一个期望值或期望结果。

Mock对象本质上是一个代理对象，负责模拟实际对象的行为。

我们能够通过一个mock对象stub一个方法，用于重定义方法的行为。

- Mockito.when()：指定了一个需要stub的方法
- Mockito.thenReturn()：指定方法返回的特定值

	import static org.mockito.Matchers.anyString;
	import static org.mockito.Mockito.when;
	@RunWith(MockitoJUnitRunner.class)
	public class StockBrokerTest {
		@Mock MarketWatcher marketWatcher;
		@Mock Portfolio portfolio;

		@Test
		public void marketWatcher_Returns_current_stock_status() {
			Stock uvsityCorp = new Stock("UV", "Uvsity Corporation", new BigDecimal("100.00"));
			when(marketWatcher.getQuote(anyString())).thenReturn(uvsityCorp);
			assertNotNull(marketWatcher.getQuote("UV"));
	}

- thenReturn(value to-be-returned)：返回特定值
- thenThrow(throwable to-bo-thrown)：抛出特定异常
- thenAnswer(Answer answer)：执行给定逻辑
- thenCallRealMethod()：执行方法的真实逻辑

	import com.packt.trading.dto.Stock;
	import static org.junit.Assert.assertNotNull;
	import static org.mockito.Matchers.anyString;
	import static org.mockito.Matchers.isA;
	import static org.mockito.Mockito.verify;
	import static org.mockito.Mockito.when;

	@RunWith(MockitoJUnitRunner.class)
	public class StockBrokerTest {
		@Mock MarketWatcher marketWatcher;
		@Mock Portfolio portfolio;
		StockBroker broker;

		@Before public void setUp() {
			broker = new StockBroker(marketWatcher);
		}

		@Test
		public void when_ten_percent_gain_then_the_stock_is_sold() {
			//Portfolio's getAvgPrice is stubbed to return $10.00
			when(portfolio.getAvgPrice(isA(Stock.class))).
				thenReturn(new BigDecimal("10.00"));

			//A stock object is created with current price $11.20
			Stock aCorp = new Stock("A", "A Corp", new BigDecimal("11.20"));

			//getQuote method is stubbed to return the stock
			when(marketWatcher.getQuote(anyString())).thenReturn(aCorp);

			//perform method is called, as the stock price increases
			// by 12% the broker should sell the stocks
			broker.perform(portfolio, aCorp);

			//verifying that the broker sold the stocks
			verify(portfolio).sell(aCorp, 10);
		}

### verify()

- verify()：用于验证方法是否被执行了，
- times()
- never()
- atLeastOnce()
- atLeast()
- atMost()
- only()
- timeout()
- verifyZeroInteractions(object... mocks)
- verifyNoMoreInteractions(object... mocks)

## 抛出异常

thenThrow(Throwable)：在调用一个stub方法时抛出指定的异常。

	@Test(expected = IllegalStateException.class)
	public void throwsException() throws Exception {
		when(portfolio.getAvgPrice(isA(Stock.class))).
			thenThrow(new IllegalStateException("Database down"));
		portfolio.getAvgPrice(new Stock(null, null, null));
	}

无返回值的方法抛出异常：

	doThrow(exception).when(mock).voidmethod(arguments);

	@Test(expected = IllegalStateException.class)
	public void throwsException_void_methods() throws Exception {
		doThrow(new IllegalStateException()).
			when(portfolio).
			buy(isA(Stock.class));
		portfolio.buy(new Stock(null, null, null));
	}