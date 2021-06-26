---
title : SQL*Plusでviを使う
tags : ["SQL*PLUS", "vim"]
publish-on : 2018-12-01
published : true
---

```sql
SQL> DEFINE _EDITOR = vi
SQL> edit
SQL> /   -- 実行
```

glogin.sqlに設定することで`DEFINE _EDITOR = vi`を省略できる
