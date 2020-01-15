# @Modifying

## Spring Data JPA中三种查询数据的机制

- Query方法
- @Query
- 自定义repository实现

## @Modifying注解

@Modifying注解用于@Query使用INSERT、UPDATE、DELETE以及DDL操作。

	@Modifying
	@Query("update User u set u.active = false where u.lastLoginDate < :date")
	void deactivateUsersNotLoggedInSince(@Param("date") LocalDate date);

	@Modifying
	@Query("delete User u where u.active = false")
	int deleteDeactivatedUsers();

@Modifying注解返回int类型时将返回修改的记录条数。

	@Modifying
	@Query(value = "alter table USERS.USERS add column deleted int(1) not null default 0", nativeQuery = true)
	void addDeletedColumn();

使用deleteBy方法与@Query删除方法区别：deleteBy方法将所有记录取出，并依次删除记录。

## 管理Persistence Context

