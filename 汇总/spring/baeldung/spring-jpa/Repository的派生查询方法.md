# Repository的派生查询方法

对于简单查询，可以通过添加对应的方法名派生查询方法。

Spring Data JPA提供find、read、query、count和get等谓词，通过By关键字与查询属性关联。

	@Table(name = "users")
	@Entity
	class User {
	    @Id
	    @GeneratedValue
	    private Integer id;
	     
	    private String name;
	    private Integer age;
	    private ZonedDateTime birthDate;
	    private Boolean active;
	 
	    // standard getters and setters
	}

## 等值条件查询

By：

	List<User> findByName(String name);

Is与Equals：

	List<User> findByNameIs(String name);
	List<User> findByNameEquals(String name);

IsNot：

	List<User> findByNameIsNot(String name);

处理Null值：
	
	List<User> findByNameIsNull();
	List<User> findByNameIsNotNull();

True与False：

	List<User> findByActiveTrue();
	List<User> findByActiveFalse();

## 模糊条件查询

StartingWith与EndingWith：

	List<User> findByNameStartingWith(String prefix);
	List<User> findByNameEndingWith(String suffix);

Containing：

	List<User> findByNameContaining(String infix);

Like：

	List<User> findByNameLike(String likePattern);
	
	String likePattern = "a%b%c";
	userRepository.findByNameLike(likePattern);

## 比较条件查询

Than与ThanEqual：

	List<User> findByAgeLessThan(Integer age);
	List<User> findByAgeLessThanEqual(Integer age);
	List<User> findByAgeGreaterThan(Integer age);
	List<User> findByAgeGreaterThanEqual(Integer age);

Between：

	List<User> findByAgeBetween(Integer startAge, Integer endAge);

In：
	
	List<User> findByAgeIn(Collection<Integer> ages);

Before与After：

	List<User> findByBirthDateAfter(ZonedDateTime birthDate);
	List<User> findByBirthDateBefore(ZonedDateTime birthDate);

## 多条件查询

Or与And：
	
	List<User> findByNameOrBirthDate(String name, ZonedDateTime birthDate);
	List<User> findByNameOrBirthDateAndActive(String name, ZonedDateTime birthDate, Boolean active);

## 排序

OrderBy：

	List<User> findByNameOrderByName(String name);
	List<User> findByNameOrderByNameAsc(String name);

## findOne与findById

	User user = userRepository.findOne(1);
	User user = userRepository.findById(1);
