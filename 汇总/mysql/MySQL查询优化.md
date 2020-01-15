# MySQL查询优化

## 逻辑查询优化

## 子查询优化

1. 特殊类型的子查询：ALL/ANY，通过MAX/MIN转换优化
2. 普通类型的子查询：通常用上拉操作优化

### 子查询处理的情况

#### IN_SUBS

格式：IN / =ANY

优化策略：
1. 半连接：语义上具有半连接语义采用半连接策略，如t1.primary_key IN SELECT t2.col FROM t2
2. 物化表：内表使用多次且内表较小可存入内存则将其存入临时表
3. EXISTS策略

格式：NOT IN / <>ANY

优化策略：
1. EXISTS策略
2. 物化表

#### ANY_SUBS / ALL_SUBS

格式：=ANY

优化策略
1. 半连接
2. 物化表
3. EXISTS策略

格式：<>ANY

优化策略
1. EXISTS策略
2. 物化表

格式：>ANY / <=ANY / <ANY / <=ANY

优化策略
1. 转换为MAX或MIN

### 子查询处理过程

### 子查询优化的限制

- MySQL不支持子查询合并
- MySQL对子查询反嵌套（子查询展开）支持有限

## group by优化

查找了网上一些博客分析GROUP BY 与临时表的关系 : 
1. 如果GROUP BY 的列没有索引,产生临时表. 
2. 如果GROUP BY时,SELECT的列不止GROUP BY列一个,并且GROUP BY的列不是主键 ,产生临时表. 
3. 如果GROUP BY的列有索引,ORDER BY的列没索引.产生临时表. 
4. 如果GROUP BY的列和ORDER BY的列不一样,即使都有索引也会产生临时表. 
5. 如果GROUP BY或ORDER BY的列不是来自JOIN语句第一个表.会产生临时表. 
6. 如果DISTINCT 和 ORDER BY的列没有索引,产生临时表.
