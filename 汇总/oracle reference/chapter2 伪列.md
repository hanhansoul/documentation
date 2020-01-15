# 伪列

## 层级查询伪列

层级查询伪列只能用于层级查询中。

- CONNECT_BY_ISCYCLE：如果当前行的祖先节点也是其子节点，即包含环，返回1，否则返回0。
- CONNECT_BY_ISLEAF：如果当前行是通过CONNECT BY条件定义的树状结构的叶子节点，返回1，否则返回0.
- LEVEL：返回当前行所在的层级，根节点层级为1，以此类推。

### 层级查询

如果表中包含层级数据，可以使用层级查询子句按层级顺序选择数据行，形成层级树。

## 序列伪列

序列是可以生成唯一序列值的对象，多用于生成主键和唯一键。

1. CURRVAL：返回序列的当前值，如sequence.CURRVAL。
2. NEXTVAL：递增序列并返回序列的下一个值，如sequence.NEXTVAL。

使用场景：
1. SELECT语句参数列表中，不包括子查询或视图中的SELECT语句。
2. INSERT语句中的SELECT子查询。
3. INSERT语句中VALUES子句中。
4. UPDATE语句中SET子句中。

	SELECT employees_seq.nextval FROM DUAL;

	INSERT INTO employees VALUES 
		(employees_seq.nextval, 'John', 'Doe', 'jdoe', '555-1212', 
		TO_DATE(SYSDATE), 'PU_CLERK', 2500, null, null, 30);

	// 复用同一个序列值
	INSERT INTO orders (order_id, order_date, customer_id)
		VALUES (orders_seq.nextval, TO_DATE(SYSDATE), 106);
	INSERT INTO order_items (order_id, line_item_id, product_id)
		VALUES (orders_seq.currval, 1, 2359);
	INSERT INTO order_items (order_id, line_item_id, product_id)
		VALUES (orders_seq.currval, 2, 3290);
	INSERT INTO order_items (order_id, line_item_id, product_id)
		VALUES (orders_seq.currval, 3, 2381);

## 行的版本查询伪列

- VERSIONS_STARTSCN
- VERSIONS_STARTTIME
- VERSIONS_ENDSCN
- VERSIONS_ENDTIME
- VERSIONS_XID：创建当前行的事务的唯一标识
- VERSIONS_OPERATION：事务执行的操作：I、D、U。

## COLUMN_VALUE伪列

## OBJECT_ID伪列

OBJECT_ID伪列返回一个对象表或视图的列的对象标识，该对象标识即对象表的主键。

OBJECT_ID用于INSTEAD OF触发器。

## OBJECT_VALUE伪列

OBJECT_VALUE伪列返回一个为对象表、XMLTable表、对象视图或XMLTable视图的列的系统生成的名称。

## ORA_ROWSCN

ORA_ROWSCN反映了当前行的最近一次修改的系统修改编号（SCN）

## ROWID

对于数据库中的每一行，ROWID返回该行存储的地址。

不同表中的行可能会有相同的ROWID。

应用场景：
1. 访问单行的最快方式
2. 表示行在表中的存储方式
3. 表中行的唯一标识

ROWID不能作为行的主键，由于对行的增删改都可能导致ROWID的改变。

	SELECT ROWID, last_name
		FROM employees
		WHERE department_id = 20;

## ROWNUM

ROWNUM返回结果行对应的顺序编号。

可以使用ROWNUM限制查询返回的结果行数量。

	SELECT * FROM employees
		WHERE ROWNUM < 11;

	-- 返回结果可能与上一句不同
	SELECT * FROM employees
		WHERE ROWNUM < 11
		ORDER BY last_name;


	-- 返回空结果集
	SELECT * FROM employees WHERE ROWNUM > 1;

	-- ROWID用于赋值
	UPDATE my_table SET column1 = ROWNUM;

## XMLDATA

XMLDATA用于访问底层的LOB或对象关系列等。

