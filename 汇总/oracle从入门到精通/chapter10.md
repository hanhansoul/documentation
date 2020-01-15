# 存储过程

## 概述

存储过程是一段存储在数据库中执行某种功能的程序，包含一条或多条SQL语句，与PL/SQL中的块有所区别。

1. 简化复杂操作
2. 增加数据的独立性
3. 提高安全性
4. 提高性能

创建存储过程语法：

	CREATE PROCEDURE procedure_name
	AS
	BEGIN
		// ...
	END;

查看存储过程：

	SELECT * FROM USER_SOURCE WHERE NAME = '' ORDER BY LINE;