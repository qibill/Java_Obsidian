
```
SELECT segment_name AS TABLENAME,
       BYTES B,
       BYTES / 1024 KB,
       BYTES / 1024 / 1024 MB
  FROM user_segments
where segment_name = upper('GROUND_TP_CREATE_DATA');
```
