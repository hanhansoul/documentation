# JPA审计

在@Entity类中指定特定事件的回调方法。

- @PrePersist
- @PreUpdate
- @PreRemove

	@Entity
	public class Bar {
	       
	    @PrePersist
	    public void onPrePersist() { ... }
	       
	    @PreUpdate
	    public void onPreUpdate() { ... }
	       
	    @PreRemove
	    public void onPreRemove() { ... }
	       
	}

为行的增删改记录操作类别与时间。

	@Entity
	public class Bar {
	      
	    //...
	      
	    @Column(name = "operation")
	    private String operation;
	      
	    @Column(name = "timestamp")
	    private long timestamp;
	      
	    //...
	      
	    // standard setters and getters for the new properties
	      
	    //...
	      
	    @PrePersist
	    public void onPrePersist() {
	        audit("INSERT");
	    }
	      
	    @PreUpdate
	    public void onPreUpdate() {
	        audit("UPDATE");
	    }
	      
	    @PreRemove
	    public void onPreRemove() {
	        audit("DELETE");
	    }
	      
	    private void audit(String operation) {
	        setOperation(operation);
	        setTimestamp((new Date()).getTime());
	    }
	      
	}

如果需要为多个类添加审计功能，可以使用@EntityListeners来复用代码。

	@EntityListeners(AuditListener.class)
	@Entity
	public class Bar { ... }
	public class AuditListener {
	     
	    @PrePersist
	    @PreUpdate
	    @PreRemove
	    private void beforeAnyOperation(Object object) { ... }
	     
	}

## Spring Data JPA

@EnableJpaAuditing实现了审计功能。

	@Configuration
	@EnableTransactionManagement
	@EnableJpaRepositories
	@EnableJpaAuditing
	public class PersistenceConfig { ... }

Spring Data本身提供了JPA实体实现用于审计功能：AuditingEntityListener。

	@Entity
	@EntityListeners(AuditingEntityListener.class)
	public class Bar { ... }

- @CreatedDate：自动添加创建时间
- @LastModifiedDate：自动更新最后一次修改时间

	@Entity
	@EntityListeners(AuditingEntityListener.class)
	public class Bar {
	     
	    //...
	     
	    @Column(name = "created_date", nullable = false, updatable = false)
	    @CreatedDate
	    private long createdDate;
	 
	    @Column(name = "modified_date")
	    @LastModifiedDate
	    private long modifiedDate;
	     
	    //...
	     
	}

### 通过Spring Security记录修改人信息

通过Spring Security可以跟踪记录的创建人和修改人。

- @CreatedBy
- @LastModifiedBy

	@Entity
	@EntityListeners(AuditingEntityListener.class)
	public class Bar {
	     
	    //...
	     
	    @Column(name = "created_by")
	    @CreatedBy
	    private String createdBy;
	 
	    @Column(name = "modified_by")
	    @LastModifiedBy
	    private String modifiedBy;
	     
	    //...
	     
	}

通过实现AuditorAware<T>可以自定义返回的当前用户信息。

	public class AuditorAwareImpl implements AuditorAware<String> {
	  
	    @Override
	    public String getCurrentAuditor() {
	        // your custom logic
	    }
	 
	}

	@EnableJpaAuditing(auditorAwareRef="auditorProvider")
	public class PersistenceConfig {
	     
	    //...
	     
	    @Bean
	    AuditorAware<String> auditorProvider() {
	        return new AuditorAwareImpl();
	    }
	     
	    //...
	     
	}

## 总结

1. JPA提供了基本的审计功能与回调功能。但只能修改非关联关系的实体。
2. Spring Data JPA继承了JPA的缺陷，不能对删除操作进行审计。