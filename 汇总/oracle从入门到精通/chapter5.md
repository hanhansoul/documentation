# 利用select检索数据

	select
		[distinct|all]
		select_list
	from table_list
		[where_clause]
		[group_by_clause]
		[having condition]
		[order_by_clause]

## 排序时对NULL值进行处理

NULL值默认情况下排序视为最大值。

可以通过NULLS FIRST与NULLS LAST指定NULL的位置。

## where子句进行筛选

1. IS NULL
2. LIKE
3. BETWEEN ... AND ...
4. IN
4. AND / OR / NOT

## 模糊查询数据

1. \_：替代一个字符
2. %：提到多个字符

## 针对NULL值的查询

1. IS NULL
2. IS NOT NULL

## group by和having子句

### group by子句语法与使用

group by子句不能出现在where子句中，但是可以出现在where子句后，即被分组的数据是经过where子句筛选过的。

1. 查询中使用group by时，select列表中只能存在分组函数，或出现在group by子句中的字段；
2. group by子句不允许出现在where条件中，但允许出现在之后，也就是可以和where条件并列使用。

### having子句的使用

having通常与group by一同使用，限制搜索条件。与where子句不同，having子句与组有关，而不与单个的值有关。在group by子句中，会作用于group by创建的组。

**having与where的区别**

1、where后不能跟聚合函数，因为where执行顺序大于聚合函数。 
2、where子句的作用是在对查询结果进行分组前，将不符合where条件的行去掉，即在分组之前过滤数据，条件中不能包含聚组函数，使用where条件显示特定的行。 
3、having子句的作用是筛选满足条件的组，即在分组之后过滤数据，条件中经常包含聚组函数，使用having条件显示特定的组，也可以使用多个分组标准进行分组。

## 使用子查询

### 子查询返回单行

### 子查询返回多行

1. ANY：表示满足子查询结果的任何一个
2. SOME：与ANY类似
3. ALL：表示满足子查询结果的所有结果