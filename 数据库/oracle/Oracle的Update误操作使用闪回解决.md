
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
