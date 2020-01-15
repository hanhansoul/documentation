## 核心概念 

- Repository
- CrudRepository
- JpaRepository
- PagingAndSortingRepository

	public interface PagingAndSortingRepository<T, ID extends Serializable>extends CrudRepository<T, ID> {

		Iterable<T> findAll(Sort sort);

		Page<T> findAll(Pageable pageable);
	}

	PagingAndSortingRepository<User, Long> repository = // … get access to a bean
	Page<User> users = repository.findAll(PageRequest.of(1, 20));

## 查询方法

@EnableJpaRepositories：开启JPA支持。

## 定义Repository接口

## Repository在多Spring Data模块间复用

## 定义查询方法

- 根据方法名直接生成
- 手动定义查询

### 查询解析策略

- CREATE
- USE_DECLARED_QUERY
- CREATE_IF_NOT_FOUND：默认

### 创建查询

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

### 特殊参数

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

### Stream

查询方法可以以Stream<T>作为返回值类型。

	@Query("select u from User u")
	Stream<User> findAllByCustomQueryAndStream();

	Stream<User> readAllByFirstnameNotNull();

	@Query("select u from User u")
	Stream<User> streamAllPaged(Pageable pageable);

	try (Stream<User> stream = repository.findAllByCustomQueryAndStream()) {
		stream.forEach(…);
	}

### 异步查询

	@Async
	Future<User> findByFirstname(String firstname);               

	@Async
	CompletableFuture<User> findOneByFirstname(String firstname); 

	@Async
	ListenableFuture<User> findOneByLastname(String lastname); 

## 创建Repository实例

### XML配置

	<repositories base-package="com.acme.repositories" />

### JavaConfig

	@Configuration
	@EnableJpaRepositories("com.acme.repositories")
	class ApplicationConfiguration {

		@Bean
		EntityManagerFactory entityManagerFactory() {
			// …
		}
	}

### 独立使用

	RepositoryFactorySupport factory = … // Instantiate factory here
	UserRepository repository = factory.getRepository(UserRepository.class);

