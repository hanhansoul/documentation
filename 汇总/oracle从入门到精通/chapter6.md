# orcale内置函数

oracle内置函数
1. 单行函数：查询表和视图时每行都能返回一个结果，可用于SELECT、WHERE、ORDER BY等子句中
2. 集合函数：作用在多行记录上返回一个结果，用于GROUP BY与HAVING子句中

dual表：oracle中真实存在的一个表，多用于在没有目标的select查询语句中，本身只包含一个dummy字段。

## 数值型函数

1. ABS(n)
2. MOD(n2, n1)
3. SIGN(n)
4. 三角函数
5. CEIL(n) / FLOOR(n)
6. SORT(n)
7. POWER(n2, n1)
8. LOG(n1, n2)
9. ROUND() / TRUNC()

## 字符型函数

- CHR(n)：将ASCII码值转换为字符
- LENGTH()

### 字符串截取函数

- SUBSTR：以字符为单位
- SUBSTRB：以字节为单位
- SUBSTRC
- SUBSTR2
- SUBSTR4

	SUBSTR(char, position[, substring_length])

- char：原始字符串
- position：要截取字符串的开始位置，初始为1
- substring_length：截取的长度

## 字符串连接函数

	CONCAT(s1, s2)

与||符号功能类似。

## 日期类型函数

SYSDATE：得到系统当前日期

	SELECT TO_CHAR(SYSDATE, 'YYYY-MM-DD') FROM DUAL;

SYSTIMESTAMP：返回系统时间，包含时区信息。

- ADD_MONTHS(date, integer)
- LAST_DAY(date)：返回参数指定日期对应月份的最后一天
- NEXT_DAT(date, char)：返回当前日期先后的一周char对应的日期
	- SELECT SYSDATE, NEXT_DAY(SYSDATE, '星期一') FROM DUAL;
- EXTRACT(datetime)
- MONTH_BETWEEN(date1, date2)

## 转换函数

- CAST(expr as type_name)：类型转换函数
- CHARTOROWID(char)：将字符串类型转换为ROWID类型
- ROWIDTOCHAR(rowid)
- CONVERT(char, dest_char_set[, source_char_set])
- TO_CHAR(number)：将一个数值类型参数转换为字符型数据
- TO_DATE()
- TO_NUMBER()

## NULL函数