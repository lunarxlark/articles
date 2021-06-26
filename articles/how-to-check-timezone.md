---
title : MySQLでのtimezone確認
tags : ["MySQL"]
publish-on : 2018-05-26
published : true
---


```sql
> show variables like '%time_zone%';
+------------------+--------+
| Variable_name    | Value  |
+------------------+--------+
| system_time_zone | JST    |
| time_zone        | SYSTEM |
+------------------+--------+

> select NOW();
+---------------------+
| NOW()               |
+---------------------+
| 2018-11-16 10:45:53 |
+---------------------+
```
