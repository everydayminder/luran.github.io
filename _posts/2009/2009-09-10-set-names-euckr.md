---
layout: single
title: set names euckr
date: 2009-09-10 23:47:04.000000000 +09:00
categories:
- mysql
tags: [encoding, db, mysql, korean]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
만약, 
DB의 한글 설정이 UTF-8로 되어 있고 (show variables like 'c%'&nbsp; 로 확인)
웹서버든 프로그램 상에서든 인코딩을 맞춰서 넣어준다고 치자.

컴퓨터에서 HeidiSQL 같은 프로그램을 써서 DB에 접속해 보면,
한글이 문제 없이 디스플레이 된다.

그러나, command로 mySQL client 접속해보면,
한글이 깨지는 경우가 있다.

그럴 때, set names euckr 이라고 하면,
테이블의 데이터가 정상으로 디스플레이된다.

물론, 그 상태에서 update 문을 실행해도 이상없이 업데이트가 수행된다.

