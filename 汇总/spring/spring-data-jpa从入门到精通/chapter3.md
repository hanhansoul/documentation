## 定义查询方法的配置方法

- 从方法名称中指定特定用于存储的查询和更新
- 通过使用@Query手动定义的查询

默认通用方法的接口：
- JpaRepository
- PagingAndSortingRepository
- CrudRepository

@RepositoryDefinition：如果不需要继承Spring Data接口，可以将@RepositoryDefinition注解在用户自己的Repository接口上。

@NoRepositoryBean：暴露指定的接口方法。

	@NoRepositoryBean
	interface MyBaseRepository<T, ID extends Serializable> extends Repository<T, ID> {

		Optional<T> findById(ID id);

		<S extends T> S save(S entity);
	}

	interface UserRepository extends MyBaseRepository<User, Long> {
		User findByEmailAddress(EmailAddress emailAddress);
	}

## 方法的查询策略设置

通过@EnableJpaRepositories的queryLookupStrategy属性可以配置方法的查
询策略。

	@EnableJpaRepositories(
		queryLookupStrategy=QueryLookupStrategy.Key.CREATE_IF_NOT_FOUND)

- CREATE：直接根据方法名进行创建。
- USE_DECLARED_QUERY：声明方式创建，即本书说的注解方式。
- CREATE_IF_NOT_FOUND：默认配置，以上两种方式的结合版。先用声明方式进行查找，如果没有找到与方法相匹配的查询，就用create的方法名创建规则创建一个查询。

## 查询方法的创建

- find...By
- read...By
- query...By
- count...By
- get...By

	interface PersonRepository extends Repository<User, Long> {

		List<Person> findByEmailAddressAndLastname(EmailAddress emailAddress, String lastname);

		// Enables the distinct flag for the query
		List<Person> findDistinctPeopleByLastnameOrFirstname(String lastname, String firstname);
		List<Person> findPeopleDistinctByLastnameOrFirstname(String lastname, String firstname);

		// Enabling ignoring case for an individual property
		List<Person> findByLastnameIgnoreCase(String lastname);
		// Enabling ignoring case for all suitable properties
		List<Person> findByLastnameAndFirstnameAllIgnoreCase(String lastname, String firstname);

		// Enabling static ORDER BY for a query
		List<Person> findByLastnameOrderByFirstnameAsc(String lastname);
		List<Person> findByLastnameOrderByFirstnameDesc(String lastname);
	}

- 表达式通常是可以连接的运算符的属性遍历。你可以使用组合属性表达式AND和OR。你还可以将运算关键字Between、LessThan、GreaterThan、Like作为属性表达式。
- 该方法解析器支持设置一个IgnoreCase标志个别特性或支持忽略大小写。
- 可以通过OrderBy在引用属性和提供排序方向的查询方法中附加一个子句来应用静态排序。

	interface PersonRepository extends Repository<User, Long> {

		List<Person> findByEmailAddressAndLastname(EmailAddress emailAddress, String lastname);

		// Enables the distinct flag for the query
		List<Person> findDistinctPeopleByLastnameOrFirstname(String lastname, String firstname);
		List<Person> findPeopleDistinctByLastnameOrFirstname(String lastname, String firstname);

		// Enabling ignoring case for an individual property
		List<Person> findByLastnameIgnoreCase(String lastname);
		// Enabling ignoring case for all suitable properties
		List<Person> findByLastnameAndFirstnameAllIgnoreCase(String lastname, String firstname);

		// Enabling static ORDER BY for a query
		List<Person> findByLastnameOrderByFirstnameAsc(String lastname);
		List<Person> findByLastnameOrderByFirstnameDesc(String lastname);
	}

## 关键字列表

略。

## 方法的查询策略的属性表达式

对于对象中的嵌套对象，可以通过遍历嵌套属性定义约束。

## 查询结果的处理

### 参数选择分页和排序

	Page<User> findByLastname(String lastname, Pageable pageable);

	Slice<User> findByLastname(String lastname, Pageable pageable);

	List<User> findByLastname(String lastname, Sort sort);

	List<User> findByLastname(String lastname, Pageable pageable);

### 限制返回结果数量

	User findFirstByOrderByLastnameAsc();

	User findTopByOrderByAgeDesc();

	Page<User> queryFirst10ByLastname(String lastname, Pageable pageable);

	Slice<User> findTop3ByLastname(String lastname, Pageable pageable);

	List<User> findFirst10ByLastname(String lastname, Sort sort);

	List<User> findTop10ByLastname(String lastname, Pageable pageable);

### 查询结果的不同形式

#### Stream

可以通过使用Java 8 Stream<T>作为返回类型来逐步处理查询方法的结果，而不是简单地将查询结果包装在Stream数据存储中，特定的方法用于执行流。

	@Query("select u from User u")
	Stream<User> findAllByCustomQueryAndStream();

	Stream<User> readAllByFirstnameNotNull();

	@Query("select u from User u")
	Stream<User> streamAllPaged(Pageable pageable);

	try (Stream<User> stream = repository.findAllByCustomQueryAndStream()) {
		stream.forEach(…);
	}

### 异步查询

可以使用Spring的异步方法执行功能异步的存储库查询。这意味着方法将在调用时立即返回，并且实际的查询执行将发生在已提交给TaskExecutor的任务中，比较适合定时任务的实际场景。

	@Async
	Future<User> findByFirstname(String firstname);               

	@Async
	CompletableFuture<User> findOneByFirstname(String firstname); 

	@Async
	ListenableFuture<User> findOneByLastname(String lastname); 