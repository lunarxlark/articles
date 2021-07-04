---
title : EC2へssh with GitHubに登録している公開鍵
tags : ["ssh","EC2"]
date : 2021-07-04
published : true
---

isuconの練習時にGitHubに登録している公開鍵でEC2へssh出来るようにする際、sedでのエスケープの仕方でハマったのでメモ。

コンソールで作成時にkey pairを指定しなくても公開鍵でssh出来ることを確認済。

```
#!/bin/bash

su isucon -c 'mkdir -p /home/isucon/.ssh && touch /home/isucon/.ssh/authorized_keys'
su isucon -c "curl https://api.github.com/users/{{github_user_id}}/keys | grep \"key\" | sed -e 's/    \"key\": \"//g' -e 's/\"//g' >> /home/isucon/.ssh/authorized_keys"
```