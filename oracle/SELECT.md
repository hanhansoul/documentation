# SELECT

- subquery_factoring_clause
- search_clause
- cycle_clause
- select_list
- table_reference
- flashback_query_clause
- query_table_expression
- pivot_clause
- pivot_for_clause
- pivot_in_clause
- unpivot_clause
- unpivot_in_clause
- partition_extension_clause
- subquery_restriction_clause
- table_collection_expression
- rollup_cube_clause
- grouping_sets_clause
- model_clause

## WITH语句

WITH AS短语，也叫做子查询部分，可以定义一个sql片断，该片断会被整个sql语句所用到，特别对于UNION ALL比较有用。因为UNION ALL 的每个部分可能相同，但是如果每个部分都去执行一遍的话，成本太高，所以可以使用WITH AS语句，只需要执行一遍即可。

	
	with tmpTableName as (select * from tb_name)
	select * from tmpTableName
	
	with tmp1 as (select * from tb_1),
	     tmp2 as (select * from tb_2)
	select * from tmp1,tmp2 where tmp1.id = tmp2.id

	insert into STUDENT
	with t1 as (select * from STUDENT t)
	select t1.stuid + 10 as stuid,
	     t1.stuname || '1' as stuname,
	     t1.stusex,
	     t1.stuclass
	from t1
	where t1.stuid = '5'

	delete from STUDENT t
 	where t.stuid in (
 		with t1 as (select * from STUDENT t)
		select t1.stuid from t1 where t1.stuid = '15')

## SEARCH

## PIVOT

行转列

	PIVOT (aggregate(expr) pivot_for_clause pivot_in_clause)

	PIVOT (aggregate(expr) FOR column IN (subquery))

	select 
		CLASS,
		SUBJECT,
		max(SCORE) THE_HIGHEST_SCORE 
	from STUDENTSCORE 
	group by CLASS, SUBJECT

	select * from (
		select CLASS, SUBJECT, SCORE from STUDENTSCORE
	) pivot (max(SCORE) for SUBJECT in ('Maths' as MAX_MATHS, 'English' as MAX_ENGLISH, 'Chinese' as MAX_CHINSES))
