## 4 标准表达式语法

- Variable表达式：${...}
- Selection Variable表达式：*{...}
- Message表达式：#{...}
- URL表达式：@{...}
- Fragment表达式：~{...}

### Messages

\#{}直接显示properties文件中对应的变量的内容。

	// properties
	home.welcome = Welcome, user!
	// html
	<p th:utext="#{home.welcome}">Welcome to our grocery store!</p>

同时，#{}也支持传入一个或多个参数，用逗号隔开。

	// properties
	home.welcome = Welcome, {0}!
	// html
	<p th:utext="#{home.welcome(${session.user.name})}">Welcome to our grocery store!</p>
	// 或
	<p th:utext="#{${welcomeMsgKey}(${session.user.name})}">Welcome to our grocery store!</p>

### Variables

${}即OGNL表达式，能够映射存储在context变量中的对应值。

	// html
	<p>Today is: <span th:text="${today}">13 february 2011</span>.</p>
	// 等价于
	ctx.getVariable("today")
	
OGNL的其他用法。

	// (.)表达式等同于调用getter函数
	${person.father.name}
	// []同上
	${person['father']['name']}
	// map变量中的值可以通过(.)或[]表达式获取
	${countriesByCode.ES}
	${personsByName['Stephen Zucchini'].age}
	// list或array中的值可以通过[]索引获取
	${personsArray[0].name}
	// 调用函数
	${person.createCompleteName()}
	${person.createCompleteNameWithSeparator('-')}

### Expression on selections

*{}与${}功能类似，唯一不同的地方是，在使用th:object标签时，*{}将在给标签结果对象内的成员变量中进行查找。

	<div th:object="${session.user}">
	    <p>Name: <span th:text="*{firstName}">Sebastian</span>.</p>
	    <p>Surname: <span th:text="*{lastName}">Pepper</span>.</p>
	    <p>Nationality: <span th:text="*{nationality}">Saturn</span>.</p>
	</div>
	// 等同于
	<div>
		<p>Name: <span th:text="${session.user.firstName}">Sebastian</span>.</p>
		<p>Surname: <span th:text="${session.user.lastName}">Pepper</span>.</p>
		<p>Nationality: <span th:text="${session.user.nationality}">Saturn</span>.</p>
	</div>
	
### URLs

@{}指定链接地址，@{}在th:href标签中使用，用于替换<a\>中href属性的值。

	<!-- Will produce 'http://localhost:8080/gtvg/order/details?orderId=3' (plus rewriting) -->
	<a href="details.html" 
	   th:href="@{http://localhost:8080/gtvg/order/details(orderId=${o.id})}">view</a>
	
	<!-- Will produce '/gtvg/order/details?orderId=3' (plus rewriting) -->
	<a href="details.html" th:href="@{/order/details(orderId=${o.id})}">view</a>
	
	<!-- Will produce '/gtvg/order/3/details' (plus rewriting) -->
	<a href="details.html" th:href="@{/order/{orderId}/details(orderId=${o.id})}">view</a>

### Fragments

	<div th:insert="~{commons :: main}">...</div>

	<div th:with="frag=~{footer :: #main/text()}">
		<p th:insert="${frag}">
	</div>

### Literals

**Text literals**

	<p>Now you are looking at a <span th:text="'working web application'">template file</span>.</p>

**Number literals**
	
	<p>The year is <span th:text="2013">1492</span>.</p>
	<p>In two years, it will be <span th:text="2013 + 2">1494</span>.</p>

**Boolean literals**

	<div th:if="${user.isAdmin() == false}">
	// 或
	<div th:if="${user.isAdmin()} == false">

**null literal**

	<div th:if="${variable.something} == null">

### 字符串操作

**字符串连接**

	<span th:text="'The name of the user is ' + ${user.name}">

**字符串替换**

字符串替换是将硬编码字符串与${}变量进行连接的一种更简单的方式，将需要替换的字符串置于(|)中，功能类似于'...'+'...'。

	<span th:text="|Welcome to our application, ${user.name}!|">
	// 等价于
	<span th:text="'Welcome to our application, ' + ${user.name} + '!'">

**算数计算**

支持+、-、*、/和%操作。

	<div th:with="isEven=(${prodStat.count} % 2 == 0)">

	<div th:with="isEven=${prodStat.count % 2 == 0}">

**比较操作**

支持>、<、>=、<=、==和!=比较运算符，其中<和>需要替换为&lt和&gt。

	<div th:if="${prodStat.count} &gt; 1">

	<span th:text="'Execution mode is ' + ( (${execMode} == 'dev')? 'Development' : 'Production')">
	
**条件表达式**

	<tr th:class="${row.even}? 'even' : 'odd'">
		...
	</tr>

**数据转换与格式化**

${{}}与*{{}}中的变量将通过数据转换服务进行处理。thymeleaf-spring3与thymeleaf-spring4通过Spring自身的转换服务整合了thymeleaf数据转换机制。

	<td th:text="${{user.lastAccessDate}}">...</td>

**预处理**

// TODO

