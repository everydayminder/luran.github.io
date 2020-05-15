---
layout: single
title: yum lock 해제하기
date: 2013-10-23 08:21:42.000000000 +09:00
categories:
- linux
tags: [tips]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
만약, 비정상적인 종료 등으로 인해 yum이 lock이 걸렸고,
기다려도 풀리지 않는다면, 다음과 같이 강제로 lock을 해제시킬 수 있다.

```
cat /var/run/yum.pid
```

을 실행시켜서 확인 후,


불필요하다면 해당파일을 지워준다.

