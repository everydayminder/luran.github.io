---
layout: single
title: "[oracle] 사용자 조회/ 비밀번호 변경하기"
date: 2010-09-09 01:31:23.000000000 +09:00
categories:
- system
tags:
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
1. 어떤 사용자 id가 등록되어 있는지 보려면,
```
select * from dba_users;
```

2. 특정 사용자의 비밀번호를 변경하려면,
```
alter user 사용자명 identified by <span style="color:rgb(165,42,42);background-color:yellow;font-weight:bold;">비밀번호</span>;
```

