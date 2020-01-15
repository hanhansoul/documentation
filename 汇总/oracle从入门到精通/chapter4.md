# SQL基础

SQL中4种类型的语言：
1. DDL：数据定义语言
2. DML：数据操纵语言
3. DQL：数据查询语言
4. DCL：数据控制语言

## 数据类型

### 常用数据类型

1. 字符型
2. 数字型
3. 日期类型
4. 其他类型

#### 字符类型

1. varchar2：可变长度字符串（常用）
2. nvarchar2：Unicode变长字符串
3. char：定长字符类型
4. nchar：Unicode定长字符类型
5. long：变长的字符串类型（很少使用）

#### 数字型

1. number(p, s)：p表示精度，s表示保留的小数位数，可以存储定长的整数和小数
2. float：浮点数

#### 日期类型

1. date
2. timestamp

#### 其他类型

1. blob：存储二进制数据
2. clob：存储字符串数据
3. bfile：将非结构化的二进制数据存储在数据库以外的系统文件中

## 数据定义语言

### create

	create table table_name (
		column_name datatype [null|not null],
		column_name datatype [null|not null],
		...
		[constraint]
	)

### alter

	alter table table_name
	add column_name |
	modify column_name |
	drop column column_name

### drop

	drop table table_name

## 约束

### 主键约束

	alter table table_name
	add contraints constraint_name 
	primary key (column_name);

### 外键约束

	constraint constraint_name foreign key (column_name)
	reference table_name (column_name)
	on delete cascade

### check约束

	constraint constraint_name check(condition)

### unique约束

	constraint constraint_name unique(column_name)

### not null约束

## 数据操纵语言和数据查询语言

### insert

	insert into table_name (col1, col2, ...)
		values (val1, val2, ...);

	insert into table_name1 (col1, col2, ...)
		select col1, col2, ... from table_name2;

	create table table_name as 
		select col1, col2, ... from source_table;

### update

	update table_name set col1=val1, col2=val2, ...
		where condition;

### delete

### select

