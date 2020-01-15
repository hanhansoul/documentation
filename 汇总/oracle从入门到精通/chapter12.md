# 事务与锁

- SET TRANSACTION
- COMMIT
- SAVEPOINT
- ROLLBACK
- ROLLBACK TO SAVEPOINT

## 锁

### 锁的分类

Oracle锁的分类：
1. 排它锁（X锁）
2. 共享锁（S锁）

### 锁的类型

1. DML锁
	 - 行级锁：事务锁
	 - 表级锁：防止修改表数据时，表的结构发生变化
2. DDL锁
3. 内部闩锁

表级锁的模式：
1. ROW SHARE：行级共享锁，`SELECT ... FROM ... FOR UPDATE`。不允许其他并行会话对同一张表使用排它锁，但允许利用DML语句或lock命令锁定同一张表中的其他记录。
2. ROW EXCLUSIVE：行级排它锁
3. SHARE：共享锁
3. SHARE ROW EXCLUSIVE：共享行级排他锁
4. EXCLUSIVE：排他锁

- SELECT ... FROM table ...：NONE
- INSERT INTO ...：RX
- UPDATE table：RX
- DELETE FROM table ...：RX
- SELECT * FROM table FOR UPDATE：RX
- LOCK TABLE table IN ROW SHARE MODE：RS
- LOCK TABLE table IN ROW EXCLUSIVE MODE：RX
- LOCK TABLE table IN SHARE MODE：S
- LOCK TABLE table IN SHARE ROW EXCLUSIVE MODE：SRX
- LOCK TABLE table IN EXCLUSIVE MODE：X