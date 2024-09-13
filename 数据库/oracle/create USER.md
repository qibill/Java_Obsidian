## 导出
```
exp nbxs/biosan#17@orcl file=d:\nbxs.dmp full=y
```
```
exp fujian181123/biosan#1047@ORCL owner=fujian181123 file=d:\daochu.dmp
```

```
select FILE_NAME, TABLESPACE_NAME
from dba_data_files; 
```

```
create tablespace test
datafile 'E:\APP\ADMINISTRATOR\ORADATA\ORCL\test.DBF'  
size 1024m  
autoextend on;

CREATE USER fujiantest 
IDENTIFIED BY biosan#1047
DEFAULT TABLESPACE test
TEMPORARY TABLESPACE temp;

GRANT CONNECT TO fujiantest;  
GRANT RESOURCE TO fujiantest;  
GRANT dba TO fujiantest;


imp fujiantest/biosan#1047@ORCL file= E:\BaiduNetdiskDownload\daochu.dmp fromuser=fujian181123 touser=fujiantest ignore=y statistics=none buffer=100000000
```
