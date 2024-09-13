创建测试表，并插入测试数据

SQL> create table t5(deptno int,name varchar2(20),say number);                    
Table created.

SQL> insert into t5 values(30,'jason',5000);
1 row created.

SQL> insert into t5 values(30,'susie',8000);
1 row created.

SQL> insert into t5 values(40,'jack',3000);
1 row created.

SQL> insert into t5 values(50,'smith',4000);
1 row created.

SQL> insert into t5 values(50,'july',6000);
1 row created.

SQL> commit;
Commit complete.

SQL> select * from t5;


```
DEPTNO NAME                                  SAY
---------- ------------------------------ ----------
        30 jason                                5000
        30 susie                                8000
        40 jack                                 3000
        50 smith                                4000
        50 july                                 6000
```


查找每个部门(deptno)的员工姓名(name)，在一行显示

方法一：wmsys.wm_concat()

SQL> select deptno,wmsys.wm_concat(name) as name from t5 group by deptno;


```
DEPTNO NAME
---------- --------------------------------------------------
        30 jason,susie
        40 jack
        50 smith,july
```


方法二：listagg()

listagg实现方式一

SQL> select deptno,listagg(name,',') within group(order by say) as name from t5 group by deptno;


```
DEPTNO NAME
---------- --------------------------------------------------
        30 jason,susie
        40 jack
        50 smith,july
```

   
listagg实现方式二
    
SQL> select distinct deptno,listagg(name,',') within group(order by say) over(partition by deptno) as name from t5;


```
DEPTNO NAME
---------- --------------------------------------------------
        40 jack
        30 jason,susie
        50 smith,july
```


如果相同deptno、name有两条记录，想要去掉name重复记录，如下：

SQL> insert into t5 values(30,'jason',8000);

1 row created.

SQL> commit;

Commit complete.

SQL> select * from t5;


```
DEPTNO NAME                                                      SAY
---------- -------------------------------------------------- ----------
        30 jason                                                    5000
        30 susie                                                    8000
        40 jack                                                     3000
        50 smith                                                    4000
        50 july                                                     6000
        30 jason                                                    8000
```

6 rows selected.

SQL> select deptno,wmsys.wm_concat(name) as name from t5 group by deptno;


```
DEPTNO NAME
---------- --------------------------------------------------
        30 jason,jason,susie
        40 jack
        50 smith,july
```


SQL> select deptno,wmsys.wm_concat(distinct name) as name from t5 group by deptno;


```
DEPTNO NAME
---------- --------------------------------------------------
        30 jason,susie
        40 jack
        50 july,smith
```
