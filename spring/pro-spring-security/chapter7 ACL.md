# chapter7 ACL

1. SID：ACL安全实体，包括用户、角色、组等，对应ACL_SID表；
2. 对象实体：ACL中的对象实例实体，对应ACL_OBEJECT_IDENTITY表；
3. ACE：表示ACL实体与对象之间的权限映射，对应ACL_ENTRY表。

数据库表：

1. acl_entry：
	1. acl_object_identity：acl_object_identity外键
	2. sid：acl_sid外键
	3. mask：权限掩码值
	4. granting：boolean
2. acl_object_identity：
	1. object_id_identity：实例主键标识符
	2. object_id_class：实例的类型，ACL_CLASS外键
	3. parent_object：实例对象的层级关系，为自身表的外键
	4. owner_id：实例对象的拥有者SID
	5. entries_inheriting：boolean，表示实例对象的权限是否可继承
3. acl_class：
	1. class：ACL中对象的类型
4. acl_sid：
	1. principal：boolean，表示特定SID是否为principal。SID可以表示组或角色
	2. sid：SID的名称，可以为principal的用户名、角色名等

实际应用中，权限掩码并不会组合不同的增删查改权限为一个掩码，而是分别存储并由系统组合处理，如由DefaultPermissionGrantingStrategy对象负责处理给定对象的权限。