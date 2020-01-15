# JpaRepository扩展详解

## JpaRepository介绍

从JpaRepository的子类是Spring Data项目对JPA实现的封装与扩展。

JpaRepository继承了PagingAndSortingRepository接口。

## QueryByExampleExecutor的使用

QBE是一种用户友好的查询技术，具有简单接口。允许动态查询创建，并且不需要编写包含字段名称的查询。

	public interface QueryByExampleExecutor<T> {

		<S extends T> Optional<S> findOne(Example<S> var1);

		<S extends T> Iterable<S> findAll(Example<S> var1);

		<S extends T> Iterable<S> findAll(Example<S> var1, Sort var2);

		<S extends T> Page<S> findAll(Example<S> var1, Pageable var2);

		<S extends T> long count(Example<S> var1);

		<S extends T> boolean exists(Example<S> var1);
	}

### Example

	public interface Example<T> {
		static default <T> Example<T> of(T probe) {
			return new TypedExample(probe, ExampleMatcher.matching());
		}

		static default <T> Example<T> of(T probe, ExampleMatcher matcher) {
			return new TypedExample(probe, matcher);
		}

		T getProbe();

		ExampleMatcher getMatcher();

		default Class<T> getProbeType() {
			return ProxyUtils.getUserClass(this.getProbe().getClass());
		}
	}

- Probe：具有填充字段的域对象的实际实体类，即查询条件的封装类
- ExampleMatcher：关于如何匹配特定字段的匹配规则，可以在多个实例重复使用。
- Example：有Probe和ExampleMatcher组成，用于创建查询。

QBE适用场景：
1. 
2. 需要在不影响已有查询的情况下经常重构领域类对象
3. 

### QueryByExampleExecutor的特点及约束

1. 支持动态查询，即支持查询条件的个数不固定。
2. 不支持过滤条件分组，不支持用or连接条件。
3. 仅支持字符串的开始/结束/包含/正则表达式匹配和其他属性的精确匹配查询。

### QueryByExampleExecutor使用实例

### ExampleMatcher详解

// TODO

### QueryByExampleExecutor使用场景与实际的使用

## JpaSpecificationExecutor

JpaSpecificationExecutor是JPA提供的Criteria API，可用于动态生成

	public interface JpaSpecificationExecutor<T> {
		Optional<T> findOne(@Nullable Specification<T> var1);

		List<T> findAll(@Nullable Specification<T> var1);

		Page<T> findAll(@Nullable Specification<T> var1, Pageable var2);

		List<T> findAll(@Nullable Specification<T> var1, Sort var2);

		long count(@Nullable Specification<T> var1);
	}

JpaSpecificationExecutor是针对Criteria API进行了predicate标准封装，使得操作Criteria更加便利。

### Criteria

1. Root<T> root：代表可以查询和操作的实体对象的根。如果将实体对象比喻成表名，那root里面就是这张表里面的字段。通过里面的Path<Y>get(String attributeName)来获得我们操作的字段。
2. CriteriaQuery<?> query：代表一个Specification顶层查询对象，它包含着查询的各个部分，比如：select、from、where、group by、order by等。CriteriaQuery对象只对实体类型或嵌入式类型的Criteria查询起作用，简单理解，它提供了查询ROOT的方法。
3. CriteriaBuilder cb：用来构建CritiaQuery的构建器对象，其实就相当于条件或者是条件组合，以谓语Predicate的形式返回。

	CriteriaQuery<T> where(Predicate... restrictions);
	CriteriaQuery<T> select(Selection<? extends T> selection);
	CriteriaQuery<T> having(Predicate... restrictions);

	Predicate p1 = cb.like(root.get("name").as(String.class)), 
		"%" + uqm.getName() + "%");
	Predicate p2 = cb.equal(root.get("uuid").as(Integer.class), 
		uqm.getUuid());
	Predicate p3 = cb.gt(root.get("age").as(Integer.class), 
		uqm.getAge());

	Predicate p = cb.and(p3, cb.or(p1, p2));

## 自定义的Repository

- 继承JpaSpecificationExecutor通过CriteriaQuery几乎可以实现任何逻辑了。
- 自己写独立接口继承CrudRepository，独立实现类利用EntityManager操作Criteria Query API可以实现任何逻辑。