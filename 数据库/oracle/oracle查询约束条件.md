
### 查找约束的方法如下：

1. 查找某表上的约束

```
select * from user_constraints where table_name= 'MEDIA''
```
2. 查找到约束所在的表

```
SELECT * FROM USER_CONSTRAINTS WHERE CONSTRAINT_NAME = 'PK_MEDIA' ;
```
可能需要DBA权限。

