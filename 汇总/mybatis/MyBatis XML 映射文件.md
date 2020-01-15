# MyBatis XML 映射文件

- cache – 对给定命名空间的缓存配置。
- cache-ref – 对其他命名空间缓存配置的引用。
- resultMap – 是最复杂也是最强大的元素，用来描述如何从数据库结果集中来加载对象。
- parameterMap – 已被废弃！老式风格的参数映射。更好的办法是使用内联参数，此元素可能在将来被移除。文档中不会介绍此元素。
- sql – 可被其他语句引用的可重用语句块。
- insert – 映射插入语句
- update – 映射更新语句
- delete – 映射删除语句
- select – 映射查询语句

## select

	<select id="selectPerson" parameterType="int" resultType="hashmap">
	  SELECT * FROM PERSON WHERE ID = #{id}
	</select>

- id
- parameterType
- resultType

## insert, update 和 delete

	<insert
	  id="insertAuthor"
	  parameterType="domain.blog.Author"
	  flushCache="true"
	  statementType="PREPARED"
	  keyProperty=""
	  keyColumn=""
	  useGeneratedKeys=""
	  timeout="20">

	<update
	  id="updateAuthor"
	  parameterType="domain.blog.Author"
	  flushCache="true"
	  statementType="PREPARED"
	  timeout="20">

	<delete
	  id="deleteAuthor"
	  parameterType="domain.blog.Author"
	  flushCache="true"
	  statementType="PREPARED"
	  timeout="20">

	<selectKey
	  keyProperty="id"
	  resultType="int"
	  order="BEFORE"
	  statementType="PREPARED">

## 结果映射

resultMap 元素是 MyBatis 中最重要最强大的元素。它可以让你从 90% 的 JDBC ResultSets 数据提取代码中解放出来，并在一些情形下允许你进行一些 JDBC 不支持的操作。实际上，在为一些比如连接的复杂语句编写映射代码的时候，一份 resultMap 能够代替实现同等功能的长达数千行的代码。ResultMap 的设计思想是，对于简单的语句根本不需要配置显式的结果映射，而对于复杂一点的语句只需要描述它们的关系就行了。

	<select id="selectUsers" resultType="User">
	  select
	    user_id             as "id",
	    user_name           as "userName",
	    hashed_password     as "hashedPassword"
	  from some_table
	  where id = #{id}
	</select>

这些情况下，MyBatis 会在幕后自动创建一个 ResultMap，再基于属性名来映射列到 JavaBean 的属性上。如果列名和属性名没有精确匹配，可以在 SELECT 语句中对列使用别名（这是一个基本的 SQL 特性）来匹配标签。

	<resultMap id="userResultMap" type="User">
	  <id property="id" column="user_id" />
	  <result property="username" column="user_name"/>
	  <result property="password" column="hashed_password"/>
	</resultMap>

## resultMap

- constructor - 用于在实例化类时，注入结果到构造方法中
	- idArg - ID 参数；标记出作为 ID 的结果可以帮助提高整体性能
	- arg - 将被注入到构造方法的一个普通结果

- id – 一个 ID 结果；标记出作为 ID 的结果可以帮助提高整体性能

- result – 注入到字段或 JavaBean 属性的普通结果

- association – 一个复杂类型的关联；许多结果将包装成这种类型
	- 嵌套结果映射 – 关联本身可以是一个 resultMap 元素，或者从别处引用一个

- collection – 一个复杂类型的集合
	- 嵌套结果映射 – 集合本身可以是一个 resultMap 元素，或者从别处引用一个

- discriminator – 使用结果值来决定使用哪个 resultMap
	- case – 基于某些值的结果映射
		- 嵌套结果映射 – case 本身可以是一个 resultMap 元素，因此可以具有相同的结构和元素，或者从别处引用一个

### id & result

Id 和 Result 的属性

1. property：映射到列结果的字段或属性。
2. column：数据库中的列名，或者是列的别名。
3. javaType：Java 类的完全限定名。
4. jdbcType：JDBC 类型。
5. typeHandler：默认的类型处理器。

### 关联
	
	<association property="author" column="blog_author_id" javaType="Author">
	  <id property="id" column="author_id"/>
	  <result property="username" column="author_username"/>
	</association>

关联（association）元素处理“有一个”类型的关系。 比如，在我们的示例中，一个博客有一个用户。关联结果映射和其它类型的映射工作方式差不多。 你需要指定目标属性名以及属性的javaType（很多时候 MyBatis 可以自己推断出来），在必要的情况下你还可以设置 JDBC 类型，如果你想覆盖获取结果值的过程，还可以设置类型处理器。

关联的不同之处是，你需要告诉 MyBatis 如何加载关联。MyBatis 有两种不同的方式加载关联：

- 嵌套 Select 查询：通过执行另外一个 SQL 映射语句来加载期望的复杂类型。
- 嵌套结果映射：使用嵌套的结果映射来处理连接结果的重复子集。

#### 关联的嵌套 Select 查询

在大型数据集或大型数据表上表现不佳。这个问题被称为“N+1 查询问题”。 概括地讲，N+1 查询问题是这样子的：

- 你执行了一个单独的 SQL 语句来获取结果的一个列表（就是“+1”）。
- 对列表返回的每条记录，你执行一个 select 查询语句来为每条记录加载详细信息（就是“N”）。

这个问题会导致成百上千的 SQL 语句被执行。有时候，我们不希望产生这样的后果。

MyBatis 能够对这样的查询进行延迟加载，因此可以将大量语句同时运行的开销分散开来。 然而，如果你加载记录列表之后立刻就遍历列表以获取嵌套的数据，就会触发所有的延迟加载查询，性能可能会变得很糟糕。

#### 关联的嵌套结果映射

#### 关联的多结果集（ResultSet）

## 集合

	<collection property="posts" ofType="domain.blog.Post">
	  <id property="id" column="post_id"/>
	  <result property="subject" column="post_subject"/>
	  <result property="body" column="post_body"/>
	</collection>


	private List<Post> posts;

### 集合的嵌套 Select 查询

posts 是一个存储 Post 的 ArrayList 集合

	<collection property="posts" 
		javaType="ArrayList" 
		ofType="Post"
		column="id" 
		select="selectPostsForBlog"/>

在一般情况下，MyBatis 可以推断 javaType 属性，因此并不需要填写。

	<collection property="posts" 
		column="id" 
		ofType="Post" 
		select="selectPostsForBlog"/>

## 自动映射

## 缓存

MyBatis 内置了一个强大的事务性查询缓存机制，它可以非常方便地配置和定制。 为了使它更加强大而且易于配置，我们对 MyBatis 3 中的缓存实现进行了许多改进。

默认情况下，只启用了本地的会话缓存，它仅仅对一个会话中的数据进行缓存。 要启用全局的二级缓存，只需要在你的 SQL 映射文件中添加一行：

	<cache/>

缓存效果：

1. 映射语句文件中的所有 select 语句的结果将会被缓存。
2. 映射语句文件中的所有 insert、update 和 delete 语句会刷新缓存。
3. 缓存会使用最近最少使用算法（LRU, Least Recently Used）算法来清除不需要的缓存。
4. 缓存不会定时进行刷新（也就是说，没有刷新间隔）。
5. 缓存会保存列表或对象（无论查询方法返回哪种）的 1024 个引用。
6. 缓存会被视为读/写缓存，这意味着获取到的对象并不是共享的，可以安全地被调用者修改，而不7
7. 干扰其他调用者或线程所做的潜在修改。