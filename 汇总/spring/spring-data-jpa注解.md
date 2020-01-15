## @Transactional

配置方法或类的事务。

## @NoRepositoryBean

spring不会为注解了@noRepositoryBean的Repository接口创建实例与注入。该注解为其他Repository接口提供了统一的父接口。

	@NoRepositoryBean
	interface MyUtilityRepository<T, ID extends Serializable> extends CrudRepository<T, ID> {
	    Optional<T> findById(ID id);
	}

	@Repository
	interface PersonRepository extends MyUtilityRepository<Person, Long> {}

## @Param

为@Query中的:name参数提供关联。

	@Query("FROM Person p WHERE p.name = :name")
	Person findByName(@Param("name") String name);

## @Id

## @Transient

