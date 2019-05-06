# ORM

- @Id：为实体注解主键属性
- @Access：指定属性的访问模式
	- Field Access
	- Property Access
	- Mixed Access
- @Column：指定属性对应的列名，默认列名为属性名
- @Table(name="", schema="")：指定表名

## 简单类型映射
1. Java基本数据类型：byte、int、short、long、boolean、char、float、double
2. 基本数据类型的包装类型：Byte、Integer、Short、Long、Boolean、Character、Float、Double
3. 字节或字符数组：byte[]、Byte[]、char[]、Character[]
4. 大数类型：BigInteger、BigDecimal
5. 字符串：String
6. Java日期与时间：Date、Calendar
7. JDBC日期与时间：java.sql.Date、java.sql.Time、java.sql.Timestamp
8. 枚举类型
9. 自定义的可序列化类型

## 列映射

- @Column(name)
- @Basic(fetch=FetchType.LAZY)：懒加载
- @Lob：二进制数据，包括字节和字符数组
- @Enumerated(EnumType.STRING)：枚举类型
- @Temporal(TemporalType.DATE)：时间/日期
- @Transient

## 主键映射

### 主键类型

1. 基本Java数据类型：byte、int、short、long、char
2. 基本类型的包装类型：Byte、Integer、Short、Long、Character
3. 字符串：String
4. 大数类型：BigInteger
5. 日期类型：Date

- @GeneratedValue(strategy)
	- AUTO
	- TABLE
	- SEQUENCE
	- IDENTITY

- @TableGenerator(name, table, pkColumnName, valueColumnName)
- @SequenceGenerator(name, sequenceName)


## 关系映射

### 多对一映射

- @ManyToOne：多对一映射，置于“一”一端的属性名上
- @JoinColumn

### 一对一映射

- @OneToOne(mappedBy)：双向一对一映射，置于不包含连接列的表的对象属性上
- @OneToOne @JoinColumn：双向一对一映射，置于包含连接列的表的对象属性上

### 一对多映射

### 多对多映射

- @ManyToMany
- @JoinTable(name, joinColumns, inverseJoinColumn)