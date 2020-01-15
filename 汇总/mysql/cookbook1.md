## where表达式中的列别名

在where语句中使用列别名会导致异常，解决方案是使用原始列名。

## 调试比较表达式

	SELECT * FROM mail WHERE srcuser < 'c' AND size > 5000

	SELECT srcuser, srcuser < 'c' AND size > 5000 FROM mail

## 克隆表

	// 克隆表结构
	CREATE TABLE ... LIKE

	// 克隆部分或全部表数据
	INSERT INTO ... SELECT

	CREATE TABLE ... SELECT

## 临时表

创建TEMPORARY表，在使用结束后让mysql自动删除该表。

	CREATE TEMPORARY TABLE tbl_name (...);

	CREATE TEMPORARY TABLE new_table LIKE original_table;

	CREATE TEMPORARY TABLE tbl_name SELECT ...;