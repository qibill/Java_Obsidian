#### 授予_dbms_crypto_权限
```
GRANT EXECUTE ON  SYS.DBMS_CRYPTO TO SHAOYANG;
```

#### 监控表空间的 I/O 比例
```
SELECT
df.tablespace_name name,
df.file_name ile,
f.phyrds 物理读,
f.phyblkrd 物理块读,
f.phywrts 物理写时间，
f.phyblkwrt 物理块写
FROM v$filestat f, dba_data_files df
WHERE f.file# = df.file_id
```

#### 查询当前会话生效或正在起作用的参数值
```
SELECT
	NUM,
	NAME,
	TYPE,
	VALUE/1024 kb,
	isdefault,
	isses_modifiable,
	issys_modifiable,
	ismodified,
	isadjusted,
	DESCRIPTION,
	update_comment 
FROM
	v$parameter 
WHERE
	NAME IN (
		'db_block_size',
		'db_cache_size',
		'java_pool_size',
		'large_pool_size',
		'pga_aggregate_target',
	'shared_pool_size',
	'sort_area_size')
```

#### 查询内存信息
```
select pool, sum(bytes)/1024/1024 mb from v$sgastat where pool is not null group by pool
```

#### oracle查询单表占用空间的大小
```
SELECT segment_name AS TABLENAME,
       BYTES B,
       BYTES / 1024 KB,
       BYTES / 1024 / 1024 MB
  FROM user_segments
where segment_name = upper('GROUND_TP_CREATE_DATA');
```
#### oracle查询约束条件
**可能需要DBA权限。
1. 查找某表上的约束
```
select * from user_constraints where table_name= 'MEDIA''
```
2. 查找到约束所在的表
```
SELECT * FROM USER_CONSTRAINTS WHERE CONSTRAINT_NAME = 'PK_MEDIA' ;
```

#### Oracle的Update误操作使用闪回解决

```
select * from GROUND_TB_BLOODCOLLECTOR as of timestamp to_timestamp('2020-5-23 16:24:00', 'yyyy-mm-dd hh24:mi:ss');
```

闪回操作前启用行移动功能
```
alter table GROUND_TB_BLOODCOLLECTOR enable row movement;
```

```
flashback table GROUND_TB_BLOODCOLLECTOR to timestamp TO_TIMESTAMP('2020-5-23 16:24:00', 'yyyy-mm-dd hh24:mi:ss');
```
