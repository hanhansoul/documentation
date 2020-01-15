# chapter6 SQL表达式

## CASE

	SELECT cust_last_name,
		CASE credit_limit 
		WHEN 100 THEN 'Low'
		WHEN 5000 THEN 'High'
		ELSE 'Medium' 
		END AS credit
	FROM customers
	ORDER BY cust_last_name, credit;

	SELECT AVG(CASE WHEN e.salary > 2000 
					THEN e.salary 
					ELSE 2000 
					END) "Average Salary" 
			FROM employees e;

## CURSOR表达式

## 日期表达式

## 间隔表达式

- INTERVAL YEAR TO MONTH
- INTERVAL DAY TO SECOND