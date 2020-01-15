# JPA删除操作

	@Entity
	public class Book {
	 
	    @Id
	    @GeneratedValue
	    private Long id;
	    private String title;
	 
	    // standard constructors
	 
	    // standard getters and setters
	}

	@Repository
	public interface BookRepository extends CrudRepository<Book, Long> {}

## deleteById与deleteAll

	// 根据主键删除记录
	repository.deleteById(book1.getId());

	// 删除所有记录
	repository.deleteAll();

## 派生删除方法

通过deleteBy指定删除方法根据的属性名。

	@Repository
	public interface BookRepository extends CrudRepository<Book, Long> {
	    long deleteByTitle(String title);
	}

删除方法需在事务中执行，因此需要@Transactional注解。

## 自定义删除方法

通过@Query与@Modifying注解实现。

	@Modifying
	@Query("delete from Book b where b.title=:title")
	void deleteBooks(@Param("title") String title);

	@Test
	@Transactional
	public void whenDeleteFromCustomQuery_thenDeletingShouldBeSuccessful() {
	    repository.deleteBooks("The Hobbit");
	    assertThat(repository.count()).isEqualTo(1);
	}

## 关联关系删除

关联关系删除与指定的级联关系有关。

	@Entity
	public class Category {
	 
	    @Id
	    @GeneratedValue
	    private Long id;
	    private String name;
	 
	    @OneToMany(mappedBy = "category", cascade = CascadeType.ALL, orphanRemoval = true)
	    private List<Book> books;
	 
	    // standard constructors
	 
	    // standard getters and setters
	}