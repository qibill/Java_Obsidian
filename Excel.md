## 字符串拼接

```
="INSERT INTO GROUND_TL_PATIENT (PATIENTID, BELONG_STATUS, DIAGNOSIS_STATUS, DISEASE_NAME, DIAGNOSIS_DATE, SOURCE) VALUES ("&A1&", "&IF(B1=4,2,1)&", "&B1&", '"&IF(D1="","",D1)&"', "&IF(C1="","''","TO_DATE('"& TEXT(C1,"YYYY-MM-DD hh:mm:ss")&"', 'yyyy-mm-dd hh24:mi:ss')")&", 1);"
```
