---
layout: single
title: 'PPAS에서 Oracle로 DB Link 연결하다 겪은 에러 - ORA-21561 : OID generation failed'
date: 2015-01-08 21:24:18.000000000 +09:00
categories:
- system
tags:
- dbms
- ppas
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
PPAS에서 Oracle로 DB Link를 연결하였고,
연결한 상태에서

```
SELECT * FROM xx@ora_link;
```

와 같이 실행했는데,

```
ERROR:  OCI error: ORA-21561: OID generation failed

********** Error **********
ERROR: OCI error: ORA-21561: OID generation failed
SQL state: 25000
```

와 같은 에러가 발생하였다.

문제를 확인하기 위해,
SQLPlus를 사용하여 동일한 문제가 발생하는지 다음과 같이 확인하였다.

```
sqlplus username/password@servicename
```

그랬더니, 동일한 에러가 발생하였다.
결국, PPAS 자체의 문제가 아니라 Oracle client가 접속할 때 생기는 문제.
인터넷을 찾아보고 다음과 같이 조치/ 해결하였다.

```
hostname
```

를 실행하여, 호스트이름을 확인한다.
/etc/hosts내에 다음의 내용을 추가한다.

```
127.0.0.1 localhost 호스트이름
```

그 후, sqlplus로 접속 시도를 했더니 정상적으로 연결이 되었으며,
앞서 생성한 DBLINK를 통한 연산도 정상적으로 수행되었다.

