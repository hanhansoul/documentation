# Criteria查询

	@Entity
	class Book {
	 
	    @Id
	    Long id;
	    String title;
	    String author;
	 
	    // getters and setters
	 
	}

## Repository

	@Repository
	class BookDao {
	 
	    EntityManager em;
	 
	    // constructor
	 
	    List<Book> findBooksByAuthorNameAndTitle(String authorName, String title) {
	        CriteriaBuilder cb = em.getCriteriaBuilder();
	        CriteriaQuery<Book> cq = cb.createQuery(Book.class);
	 
	        Root<Book> book = cq.from(Book.class);
	        Predicate authorNamePredicate = cb.equal(book.get("author"), authorName);
	        Predicate titlePredicate = cb.like(book.get("title"), "%" + title + "%");
	        cq.where(authorNamePredicate, titlePredicate);
	 
	        TypedQuery<Book> query = em.createQuery(cq);
	        return query.getResultList();
	    }
	 
	}

