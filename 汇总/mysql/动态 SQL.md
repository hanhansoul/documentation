# 动态 SQL

1. if
2. choose (when, otherwise)
3. trim (where, set)
4. foreach

## if

动态 SQL 通常要做的事情是根据条件包含 where 子句的一部分。

	<select id="findActiveBlogLike"
	     resultType="Blog">
	  SELECT * FROM BLOG WHERE state = ‘ACTIVE’
	  <if test="title != null">
	    AND title like #{title}
	  </if>
	  <if test="author != null and author.name != null">
	    AND author_name like #{author.name}
	  </if>
	</select>

## choose, when, otherwise

	<select id="findActiveBlogLike"
	     resultType="Blog">
	  SELECT * FROM BLOG WHERE state = ‘ACTIVE’
	  <choose>
	    <when test="title != null">
	      AND title like #{title}
	    </when>
	    <when test="author != null and author.name != null">
	      AND author_name like #{author.name}
	    </when>
	    <otherwise>
	      AND featured = 1
	    </otherwise>
	  </choose>
	</select>

## trim, where, set

	<select id="findActiveBlogLike"
	     resultType="Blog">
	  SELECT * FROM BLOG
	  <where>
	    <if test="state != null">
	         state = #{state}
	    </if>
	    <if test="title != null">
	        AND title like #{title}
	    </if>
	    <if test="author != null and author.name != null">
	        AND author_name like #{author.name}
	    </if>
	  </where>
	</select>

通过自定义 trim 元素来定制 where 元素的功能。

	<trim prefix="WHERE" prefixOverrides="AND |OR ">
	  ...
	</trim>

类似的用于动态更新语句的解决方案叫做 set。set 元素可以用于动态包含需要更新的列，而舍去其它的。

	<update id="updateAuthorIfNecessary">
	  update Author
	    <set>
	      <if test="username != null">username=#{username},</if>
	      <if test="password != null">password=#{password},</if>
	      <if test="email != null">email=#{email},</if>
	      <if test="bio != null">bio=#{bio}</if>
	    </set>
	  where id=#{id}
	</update>

## foreach

	<select id="selectPostIn" resultType="domain.blog.Post">
	  SELECT *
	  FROM POST P
	  WHERE ID in
	  <foreach item="item" index="index" collection="list"
	      open="(" separator="," close=")">
	        #{item}
	  </foreach>
	</select>

你可以将任何可迭代对象（如 List、Set 等）、Map 对象或者数组对象传递给 foreach 作为集合参数。当使用可迭代对象或者数组时，index 是当前迭代的次数，item 的值是本次迭代获取的元素。当使用 Map 对象（或者 Map.Entry 对象的集合）时，index 是键，item 是值。

## 在注解中使用动态语句

	    @Update({"<script>",
	      "update Author",
	      "  <set>",
	      "    <if test='username != null'>username=#{username},</if>",
	      "    <if test='password != null'>password=#{password},</if>",
	      "    <if test='email != null'>email=#{email},</if>",
	      "    <if test='bio != null'>bio=#{bio}</if>",
	      "  </set>",
	      "where id=#{id}",
	      "</script>"})
	    void updateAuthorValues(Author author);

## 多数据库支持

一个配置了“\_databaseId”变量的 databaseIdProvider 可用于动态代码中，这样就可以根据不同的数据库厂商构建特定的语句。

	<insert id="insert">
	  <selectKey keyProperty="id" resultType="int" order="BEFORE">
	    <if test="_databaseId == 'oracle'">
	      select seq_users.nextval from dual
	    </if>
	    <if test="_databaseId == 'db2'">
	      select nextval for seq_users from sysibm.sysdummy1"
	    </if>
	  </selectKey>
	  insert into users values (#{id}, #{name})
	</insert>