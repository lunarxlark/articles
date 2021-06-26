---
title : 実行している現在のMakefileのディレクトリ取得方法
tags : ["Makefile"]
publish-on : 2018-07-12
published : true
---

```Makefile
MAKEFILE_DIR := $(dir $(lastword $(MAKEFILE_LIST)))
```
