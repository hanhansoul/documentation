# spring整合redis

## 连接

spring data redis提供了多个模板完成redis数据库的数据存取功能。

spring data redis提供了四个连接工厂：
1. JedisConnectionFactory
2. JredisConnectionFacotry
3. LettuceConnectionFactory
4. SrpConnectionFactory

spring data redis提供了两种较为高级的数据访问模板：
1. RedisTemplate
2. StringRedisTemplate

	RedisConnectionFactory cf = ...;
	RedisTemplate<String, Product> redis = new RedisTemplate<String, Product>();
	redis.setConnectionFactory(cf);

RedisTemplate的API：
1. opsForValue()
2. opsForList()
3. opsForSet()
4. opsForZSet()
5. opsForHash()
6. boundValueOps(K)
7. boundListOps(K)
8. boundSetOps(K)
9. boundZSet(K)
10. boundHashOps(K)

通过RedisTemplate<String, Product>保存Product：

	redis.opsForValue().set(product.getSku(), product);

通过RedisTemplate<String, Product>获取Product：

	Product product = redis.opsForValue().get("123456");

List操作：

	redis.opsForList().rightPush("cart", product);
	redis.opsForList().leftPush("cart", product);
	Product product = redis.opsForList().leftPop("cart");
	Product product = redis.opsForList().rightPop("cart");
	List<Product> products = resid.opsForList().range("cart", 2, 12);

Set操作：

	List<Product> diff = redis.opsForSet().difference("cart1", "cart2");
	List<Product> union = redis.opsForSet().unioin("cart1", "cart2");
	List<Product> isect = redis.opsForSet().isect("cart1", "cart2");
	redis.opsForSet().remove(product);
	Product random = redis.opsForSet().randomMember("cart");

## 序列化

序列化器用于对key和value进行序列化。spring data redis提供了多个序列化其：
1. GenericToStringSerializer
2. JacksonJsonRedisSerializer
3. Jackson2JsonRedisSerializer
4. JdkSerializationRedisSerializer
5. OxmSerializer
6. StringRedisSerializer
