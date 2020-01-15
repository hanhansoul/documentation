# mysql的默认隔离级别

mysql默认使用可重复读作为默认隔离级别。

## 主从复制

mysql基于binlog实现主从复制。

binlog格式：
1. statement：记录修改sql语句
2. row：记录每行实际数据的变更
3. mixed：statement和row模式的混合