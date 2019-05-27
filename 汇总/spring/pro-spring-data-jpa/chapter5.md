# Collection Mapping

## 关联关系与元素集合

- @ElementCollection
- @CollectionTable

- @OrderBy
- @OrderColumn

## Maps

### Keys and Values

Map的键值类型要求其唯一性。

### 键值类型：基本类型

如果映射是一个以基本类型作为键值的元素集合，键将被保存在值存储的同一个集合表中。

- MapKeyColumn：注解集合表中保存基本类型

### 键值类型：实体的属性

- @MapKey

### 键值类型：内嵌类型

### 键值类型：实体