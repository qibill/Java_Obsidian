## job的运行频率设置

1.每天固定时间运行，比如早上8:10分钟：Trunc(Sysdate+1) + (8*60+10)/24*60

2.Toad中提供的：

每天：trunc(sysdate+1)

每周：trunc(sysdate+7)

每月：trunc(sysdate+30)

每个星期日：next_day(trunc(sysdate),'SUNDAY')

每天6点：trunc(sysdate+1)+6/24

半个小时：sysdate+30/1440

3.每个小时的第15分钟运行，比如：8:15，9:15，10:15...：trunc(sysdate,'hh')+75/1440。原先我设置的是trunc(sysdate,'hh')+15/1440，发现居然不行。

## 二。JOB为什么不运行？

1.先来了解一下JOB的参数说明：与job相关的参数一个是job_queue_processes，这个是运行JOB时候所起的进程数，当然系统里面JOB大于这个数值后，就会有排队等候的，最小值是0，表示不运行JOB，最大值是36，在OS上对应的进程时SNPn，9i以后OS上管理JOB的进程叫CJQn。可以使用下面这个SQL确定目前有几个SNP/CJQ在运行。

初始化相关参数job_queue_processes

alter system set job_queue_processes=39 scope=spfile;//最大值不能超过1000 ;job_queue_interval = 10 //调度作业刷新频率秒为单位

job_queue_process 表示oracle能够并发的job的数量，可以通过语句　　


```
show parameter job_queue_process;
```


```
select * from v$parameter where name='job_queue_processes';
```

来查看oracle中job_queue_process的值。当job_queue_process值为0时表示全部停止oracle的job，可以通过语句


```
ALTER SYSTEM SET job_queue_processes = 10;
```

来调整启动oracle的job。

select * from v$bgprocess，这个paddr不为空的snp/cjq进程就是目前空闲的进程，有的表示正在工作的进程。

另外一个是job_queue_interval，范围在1--3600之间，单位是秒，这个是唤醒JOB的process，因为每次snp运行完他就休息了，需要定期唤醒他，这个值不能太小，太小会影响数据库的性能。

2.诊断：先确定上面这两个参数设置是否正确，特别是第一个参数，设置为0了，所有JOB就不会跑，确认无误后，我们继续向下。

3.使用下面的SQL察看JOB的的broken,last_date和next_date，last_date是指最近一次job运行成功的结束时间，next_date是根据设置的频率计算的下次执行时间，根据这个信息就可以判断JOB上次是否正常，还可以判断下次的时间对不对，SQL如下：

select * from dba_jobs

有时候我们发现他的next_date是4000年1月1日，说明job要不就是在running，要不就是状态是break(broken=Y)，如果发现JOB的broken值为Y，找用户了解一下，确定该JOB是否可以broken，如果不能broken，那就把broken值修改成N，修改再使用上面的SQL察看就发现他的last_date已经变了，JOB即可正常运行，修改broken状态的SQL如下：

declare

BEGIN

DBMS_JOB.BROKEN(<JOB_ID>,FALSE);

END;

4.使用下面的SQL查询是否JOB还在Running

select * from dba_jobs_running

如果发现JOB已经Run了很久了还没有结束，就要查原因了。一般的JOB running时会锁定相关的相关的资源，可以查看一下v$access和v$locked_object这两个view，如果发现其他进程锁定了与JOB相关的Object，包括PKG/Function/Procedure/Table等资源，那么就要把其他进程删除，有必要的话，把JOB的进程也删除，再重新跑看看结果。

5.如果上面都正常，但是JOB还不run，怎么办？那我们要考虑把JOB进程重启一次，防止是SNP进程死了造成JOB不跑，指令如下：

alter system set job_queue_processes=0 --关闭job进程，等待5--10秒钟

alter system set job_quene_processes=5 --恢复原来的值

6.Oracle的BUG

Oracle9i里面有一个BUG，当计数器到497天时，刚好达到它的最大值，再计数就会变成-1，继续计数就变成0了，然后计数器将不再跑了。如果碰到这种情况就得重启数据库，我们这边有一个生产型的数据库版本是9205，就发生过这样一次问题，后来和用户约时间重启后就没问题了。但是其他的Oracle7345和Oracle8i的数据库没有发现这个问题。

7.数据库上的检查基本上就这多，如果JOB运行还有问题，那需要配合用户察看一下是否是程序本身的问题，比如处理的资料量大，或者网络速度慢等造成运行时过长，那就需要具体情况具体分析了。我们可以通过下面的SQL手工执行一下JOB看看：

declare

begin

dbms_job.run(<job>_ID)

end;

如果发现JOB执行不正常，就要结合程序具体分析一下。