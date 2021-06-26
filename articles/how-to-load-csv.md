---
title : MySQLへcsvをloadする方法
tags : ["MySQL"]
publish-on : 2018-06-15
published : true
---


```sql
  load data infile 'account.csv' into table authapi.account
    fields
      terminated by ','
      enclosed by '\\"'
      escaped by '\\'
  lines
    terminated by '\\\\n'
```
