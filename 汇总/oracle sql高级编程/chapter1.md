# chapter1

## INSERT

### 单表插入

	INSERT INTO table_name (...) VALLUES (...)

### 多表插入

	INSERT ALL 

当INSERT关键字后面指定ALL子句时，这个语句就会执行无条件

## UPDATE

## DELETE

## MERGE

MERGE语句具有按条件获取要更新或插入到表中的数据行，然后从1个或多个源头对表进行更新或者向表中插入行两方面能力。经常用来在数据库汇总移动大量的数据。

该语句可以很方便的将多个操作合为一个，可以避免使用多个INSERT、UPDATE和DELETE语句。

	MERGE <hint>
	INTO <table_name>
	USING <table_view_or_query>
	ON (<condition>)
	WHEN MATCHED THEN <update_clause>
	DELETE <where_clause>
	WHEN NOT MATCHED THEN <insert_clause>


	merge into dept60_bonuses b
	using (
		select employee_id, salary, department_id
		from employees
		where department_id = 60) e
	on (b.employee_id = e.employee_id)
	when matched then
		update set b.bonus_amt = e.salary * 0.2
		where b.bonus_amt = 0
		delete where (e.salary > 7500)
	when not matched then
		insert (b.employee_id, b.bonus_amt)
		values (e.employee_id, e.,salary * 0.1)
		where (e.salary < 7500)


		