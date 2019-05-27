# chapter7 ACL

https://blog.csdn.net/qq_32734365/article/details/81413236

1. SID：ACL安全实体，包括用户、角色、组等，对应ACL_SID表；
2. 对象实体：ACL中的对象实例实体，对应ACL_OBEJECT_IDENTITY表；
3. ACE：表示ACL实体与对象之间的权限映射，对应ACL_ENTRY表。

数据库表：

1. acl_entry：
	1. acl_object_identity：acl_object_identity外键
	2. sid：acl_sid外键
	3. mask：权限掩码值
	4. granting：boolean
2. acl_object_identity：
	1. object_id_identity：实例主键标识符
	2. object_id_class：实例的类型，ACL_CLASS外键
	3. parent_object：实例对象的层级关系，为自身表的外键
	4. owner_id：实例对象的拥有者SID
	5. entries_inheriting：boolean，表示实例对象的权限是否可继承
3. acl_class：
	1. class：ACL中对象的类型
4. acl_sid：
	1. principal：boolean，表示特定SID是否为principal。SID可以表示组或角色
	2. sid：SID的名称，可以为principal的用户名、角色名等

实际应用中，权限掩码并不会组合不同的增删查改权限为一个掩码，而是分别存储并由系统组合处理，如由DefaultPermissionGrantingStrategy对象负责处理给定对象的权限。

## 配置

web.xml

	<!--ContextLoaderListener在启动web容器时自动装配ApplicationContext配置信息-->
	<listener>
		<listener-class>
			org.springframework.web.context.ContextLoaderListener
		</listener-class>
	</listener>

	<!--配置文件-->
	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>
			/WEB-INF/applicationContext-acl.xml
			/WEB-INF/applicationContext-security.xml
		</param-value>
	</context-param>

	<!--配置DispatcherServlet-->
	<servlet>
		<servlet-name>acl-example</servlet-name>
		<servlet-class>
			org.springframework.web.servlet.DispatcherServlet
		</servlet-class>
		<load-on-startup>1</load-on-startup>
	</servlet>
	<servlet-mapping>
		<servlet-name>acl-example</servlet-name>
		<url-pattern>/</url-pattern>
	</servlet-mapping>

	<!--配置spring security过滤器链-->
	<filter>
		<filter-name>springSecurityFilterChain</filter-name>
		<filter-class>
			org.springframework.web.filter.DelegatingFilterProxy
		</filter-class>
	</filter>
	<filter-mapping>
		<filter-name>springSecurityFilterChain</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>

applicationContext-acl.xml

	<!-- 数据源 -->
	<bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
		<property name="driverClassName" value="org.hsqldb.jdbcDriver"/>
		<property name="url" value="jdbc:hsqldb:mem:aclexample"/>
		<property name="username" value="sa"/>
		<property name="password" value=""/>
	</bean>

	<!-- JdbcTemplate-->
	<bean id="jdbcTemplate"
	class="org.springframework.jdbc.core.JdbcTemplate">
		<property name="dataSource" ref="dataSource"/>
	</bean>

	<!-- 事务管理 -->
	<bean id="transactionManager"
	class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<property name="dataSource" ref="dataSource"/>
	</bean>
	<tx:annotation-driven transaction-manager="transactionManager" />

	<!-- 缓存 -->
	<bean id="aclCache" class="org.springframework.security.acls.domain.EhCacheBasedAclCache">
		<constructor-arg>
			<bean class="org.springframework.cache.ehcache.EhCacheFactoryBean">
				<property name="cacheManager">
					<bean class="org.springframework.cache.ehcache.EhCacheManagerFactoryBean"/>
				</property>
				<property name="cacheName" value="aclCache"/>
			</bean>
		</constructor-arg>
	</bean>

	<!-- 
		lookup strategy 
		负责返回object identities与相应的ACL
		BasicLookupStrategy是LookupStrategy接口的唯一实现类
		BasicLookupStrategy首先从缓存中返回ACL，如果不存在则从数据库中查询并返回
	-->
	<bean id="lookupStrategy" class="org.springframework.security.acls.jdbc.BasicLookupStrategy">
		<constructor-arg ref="dataSource"/>
		<constructor-arg ref="aclCache"/>
		<constructor-arg ref="aclAuthorizationStrategy"/>
		<constructor-arg>
			<bean class="org.springframework.security.acls.domain.ConsoleAuditLogger"/>
		</constructor-arg>
	</bean>

	<!--
		AclAuthorizationStrategy
		AclAuthorizationStrategy的默认实现类是AclAuthorizationStrategyImpl，其构造需要接收一个或三个GrantedAuthority参数，用来对Acl进行相关操作时所需要的权限，包括
		1. 更改Acl对应对象的所有者需要的权限
		2. 更改Acl中包含的某个AccessControlEntry的audit信息（对应acl_entry表中的is_audit_success和is_audit_failure字段）需要的权限
		3. 增、删、改Acl中所包含的AccessControlEntry等需要的权限。
	-->
	<bean id="aclAuthorizationStrategy"
		class="org.springframework.security.acls.domain.AclAuthorizationStrategyImpl">
		<constructor-arg>
			<list>
				<bean class="org.springframework.security.core.authority.SimpleGrantedAuthority">
					<constructor-arg value="ROLE_ADMIN"/>
				</bean>
				<bean class="org.springframework.security.core.authority.SimpleGrantedAuthority">
					<constructor-arg value="ROLE_ADMIN "/>
				</bean>
				<bean class="org.springframework.security.core.authority.SimpleGrantedAuthority">
					<constructor-arg value="ROLE_ADMIN "/>
				</bean>
			</list>
		</constructor-arg>
	</bean>

	<!--
		架构的核心组件
		AclService与MutableAclService提供了访问ACL相关操作的功能，如读取ACL、创建ACL、删除ACL与
		更新ACL等。
	-->
	<bean id="aclService" class="org.springframework.security.acls.jdbc.JdbcMutableAclService">
		<constructor-arg ref="dataSource"/>
		<constructor-arg ref="lookupStrategy"/>
		<constructor-arg ref="aclCache"/>
	</bean>

applicationContext-security.xml

	<security:http auto-config="true" />
	
	<security:authentication-manager>
		<security:authentication-provider>
			<security:user-service>
				<security:user authorities="ROLE_USER" name="car" password="scarvarez" />
				<security:user authorities="ROLE_ADMIN" name="mon" password="scarvarez" />
			</security:user-service>
		</security:authentication-provider>
	</security:authentication-manager>

	<context:component-scan base-package="com.apress.pss.acl.services" />
	<security:global-method-security secured-annotations="enabled" />

ForumController.java

	@Controller
	@RequestMapping("/forum")
	public class ForumController {
		@Autowired
		private ForumService forumService;

		@RequestMapping(method = RequestMethod.POST, value = "/post")
		public ModelAndView createPost(@RequestBody String postContent){
			forumService.createPost(new Post(postContent));
			return showForm();
		}

		@RequestMapping(method = RequestMethod.GET, value = "/")
		public ModelAndView showForm(){
			Map<String, Object> model = new HashMap<String, Object>();
			model.put("posts", forumService.getPosts());
			return new ModelAndView("form",model);
		}
	}

ForumServiceImpl.java

	@Service
	public class ForumServiceImpl implements ForumService {
		@Autowired
		private MutableAclService mutableAclService;

		private Map<Integer, Post> postStore = new HashMap<Integer, Post>();

		@Transactional
		@Secured({"ROLE_USER","ROLE_ADMIN"})
		public void createPost(Post post) {
			Integer id = new Integer(Math.abs(post.hashCode()));
			ObjectIdentity oid = new ObjectIdentityImpl(Post.class, id);
			MutableAcl acl = mutableAclService.createAcl(oid);
			User user = (User)SecurityContextHolder.getContext().getAuthentication().getPrincipal();

			// 将ADMINISTRATION权限授予当前用户
			acl.insertAce(0, BasePermission.ADMINISTRATION,
				new PrincipalSid(user.getUsername()), true);
			
			// 将DELETE权限授予ROLE_ADMIN角色的用户
			acl.insertAce(1, BasePermission.DELETE,
				new GrantedAuthoritySid("ROLE_ADMIN"), true);

			// 将READ权限授予ROLE_USER角色的用户
			acl.insertAce(2, BasePermission.READ,
				new GrantedAuthoritySid("ROLE_USER"), true);

			mutableAclService.updateAcl(acl);
			post.setId(id);
			postStore.put(id, post);
		}

		public Map<Integer, Post> getPosts(){
			return postStore;
		}

		public void setMutableAclService(MutableAclService mutableAclService) {
			this.mutableAclService = mutableAclService;
		}
	}

## 访问对象

AclEntryVoter用于定义投票表决器，通过投票确定是否通过或驳回根据ACL提供的给定角色的访问权限。

AclEntryVoter构造器接受三个参数：
1. AclService对象引用；
2. 配置属性的名称，用于@Secured注解中的参数；
3. 对于该对象允许执行的特定操作对应的Permission类型。

@Secured注解参数：对于@Secured("ROLE_USER")以及其对应的RoleVoter，投票器会根据RoleVoter处理以ROLE_开头的配置参数。对于AclEntryVoter，我们可以指定该投票器支持的特定配置属性。

	<bean id="aclDeletePostVoter" class="org.springframework.security.acls.AclEntryVoter">
		<constructor-arg ref="aclService" />
		<constructor-arg value="ACL_POST_DELETE" />
		<constructor-arg>
			<list>
				<util:constant static-field="org.springframework.security.acls.domain.BasePermission.DELETE" />
			</list>
		</constructor-arg>
		<property name="processDomainObjectClass" value="com.apress.pss.acl.domain.Post" />
	</bean>

	<bean id="aclUpdatePostVoter" class="org.springframework.security.acls.AclEntryVoter">
		<constructor-arg ref="aclService" />
		<constructor-arg value="ACL_POST_UPDATE" />
		<constructor-arg>
			<list>
				<util:constant static-field="org.springframework.security.acls.domain.BasePermission.ADMINISTRATION" />
			</list>
		</constructor-arg>
		<property name="processDomainObjectClass" value="com.apress.pss.acl.domain.Post" />
	</bean>

	<bean id="aclReadPostVoter" class="org.springframework.security.acls.AclEntryVoter">
		<constructor-arg ref="aclService" />
		<constructor-arg value="ACL_POST_READ" />
		<constructor-arg>
			<list>
				<util:constant static-field="org.springframework.security.acls.domain.BasePermission.READ" />
			</list>
		</constructor-arg>
		<property name="processDomainObjectClass" value="com.apress.pss.acl.domain.Post" />
	</bean>

applicationContext-security.xml补充

	<security:global-method-security secured-annotations="enabled" 	access-decision-manager-ref="customAccessDecisionManager" />
		
	<bean id="customAccessDecisionManager" 	class="org.springframework.security.access.vote.AffirmativeBased">
		<property name="decisionVoters">
			<list>
				<ref bean="aclDeletePostVoter"/>
				<ref bean="aclUpdatePostVoter"/>
				<ref bean="aclReadPostVoter"/>
			</list>
		</property>
	</bean>

ForumServiceImpl.deletePost()

	@Transactional
	@Secured("ACL_POST_DELETE")
	public void deletePost(Post post){
		ObjectIdentity oid = new ObjectIdentityImpl(Post.class, post.getId());
		mutableAclService.deleteAcl(oid, true);
		postStore.remove(postStore.get(post.getId()));
	}

ACL访问对象执行步骤：
1. 遍历AccessDecisionVoter，及AclEntryVoter列表，返回处理对应@Secured注解参数的EntryVoter，即ACL_POST_DELETE对应的AclEntryVoter；
2. 投票器遍历方法的参数列表，查询该AclEntryVoter中processDomainObjectClass对应的类型的对象，即Post对象；
3. 返回域对象对应的ObjectIdentity；
4. 从Authentication对象中返回SID；
5. 返回ObjectIdentity和SID对应的ACL；
6. 根据返回的ACL确定该访问是否合法，通过依次比较ACL中对应的每一个ACE的权限与SID。

## 过滤返回对象

创建同时具有ROLE_USER和ROLE_ADMIN角色的用户

	<security:user authorities="ROLE_USER,ROLE_ADMIN" name="bea" 
		password="scarvarez" />.

使用@PostFilter注解过滤返回的对象。使用@PostFilter需要创建一个PreInvocationAuthorizationAdviceVoter对象。

	<bean id ="preInvocationAuthorizationAdviceVoter"
		class="org.springframework.security.access.prepost.
		reInvocationAuthorizationAdviceVoter">
		<constructor-arg>
			<bean class="org.springframework.security.access.expression.method.
				ExpressionBasedPreInvocationAdvice"/>
		</constructor-arg>
	</bean>

getPosts()方法，其中filterObject表示getPosts()方法返回的容器中的每一个元素。

	@PostFilter("hasPermission(filterObject, 'READ')")
	public Collection<Post> getPosts(){
		return new ArrayList<Post>(postStore.values());
	}

applicationContext-seucurity.xml

	<bean id="customPermissionEvaluator" 
		class="org.springframework.security.acls.AclPermissionEvaluator">
		<constructor-arg ref="aclService" />
	</bean>

	<bean id="customExpressionHandler"
		class="org.springframework.security.access.expression.method.DefaultMethodSecurityExpressionHandler">
		<property name="permissionEvaluator" ref="customPermissionEvaluator" />
	</bean>