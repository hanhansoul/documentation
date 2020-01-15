## @Query详解

### @Query用法

	public interface UserRepository extends JpaRepository<User, Long> {

		@Query("select u from User u where u.emailAddress = ?1")
		User findByEmailAddress(String emailAddress);

		@Query("select u from User u where u.firstname like %?1")
		List<User> findByFirstnameEndsWith(String firstname);

		@Query(value = "SELECT * FROM USERS WHERE EMAIL_ADDRESS = ?1", nativeQuery = true)
  		User findByEmailAddress(String emailAddress);

  		// 分页
  		@Query(value = "SELECT * FROM USERS WHERE LASTNAME = ?1",
    			countQuery = "SELECT count(*) FROM USERS WHERE LASTNAME = ?1",
    			nativeQuery = true)
		Page<User> findByLastname(String lastname, Pageable pageable);
	}

### @Query排序

	public interface UserRepository extends JpaRepository<User, Long> {

		@Query("select u from User u where u.lastname like ?1%")
		List<User> findByAndSort(String lastname, Sort sort);

		@Query("select u.id, LENGTH(u.firstname) as fn_len from User u where u.lastname like ?1%")
		List<Object[]> findByAsArrayAndSort(String lastname, Sort sort);
	}

	repo.findByAndSort("lannister", new Sort("firstname"));               
	repo.findByAndSort("stark", new Sort("LENGTH(firstname)"));           
	repo.findByAndSort("targaryen", JpaSort.unsafe("LENGTH(firstname)")); 
	repo.findByAsArrayAndSort("bolton", new Sort("fn_len"));   

### 命名参数

	public interface UserRepository extends JpaRepository<User, Long> {

		@Query("select u from User u where u.firstname = :firstname or u.lastname = :lastname")
		User findByLastnameOrFirstname(@Param("lastname") String lastname,
										@Param("firstname") String firstname);
	}

### SpEL表达式

在Spring Data JPA 1.4以后，支持在@Query中使用SpEL表达式来接收变量。

根据制定的Repository自动插入相关的entityName，解析方式：
1. 如果定义了@Entity注解，直接使用其属性名
2. 如果没有定义，直接使用实体类名称

	@Entity
	public class User {

		@Id
		@GeneratedValue
		Long id;

		String lastname;
	}

	public interface UserRepository extends JpaRepository<User,Long> {

		@Query("select u from #{#entityName} u where u.lastname = ?1")
		List<User> findByLastname(String lastname);
	}

SpEL表达式控制参数：

	@Query("select u from User u where u.firstname = ?1 and u.firstname=?#{[0]} 
			and u.emailAddress = ?#{principal.emailAddress}")
	List<User> findByFirstnameAndCurrentUserWithCustomQuery(String firstname);

## 修改查询

@Modifying：实现只需要参数绑定的update查询的执行

	@Modifying
	@Query("update User u set u.firstname = ?1 where u.lastname = ?2")
	int setFixedFirstnameFor(String firstname, String lastname);