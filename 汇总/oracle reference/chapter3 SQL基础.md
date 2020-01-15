# SQL基础

- 数据类型
- 数据类型比较规则
- 字面量
- 格式化
- NULL
- 注释
- 数据库对象
- 数据库对象名与限定符
- 数据库与SQL语法

## 数据类型

### 字符数据类型

- CHAR
- NCHAR
- VARCHAR2
- NVARCHAR2

### 数字类型

- NUMBER(p, s)：p是有效数字位数，s是小数部分位数
- FLOAT

## 数据类型比较

## 数据转换

### 字符串转日期

- TO_DATE
- TO_CHAR

	SELECT TO_CHAR(TO_DATE('27-OCT-98', 'DD-MON-RR'), 'YYYY') "Year" FROM DUAL;

	SELECT TO_CHAR(TO_DATE('27-OCT-17', 'DD-MON-RR'), 'YYYY') "Year" FROM DUAL;

	SELECT TO_CHAR(TO_DATE('27-OCT-98', 'DD-MON-RR'), 'YYYY') "Year" FROM DUAL;

### Null

- IS NULL
- IS NOT NULL