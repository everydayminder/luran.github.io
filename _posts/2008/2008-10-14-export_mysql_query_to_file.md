---
layout: single
title: 테이블 내용을 파일로 출력하기
date: 2008-10-14 11:44:51.000000000 +09:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- mysql
tags: [tips]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
```
mysql > select * into outfile '파일명' fields terminated by '구분자' from 테이블명
```



