---
layout: single
title: Toad + Oracle Thin Client
date: 2013-07-23 06:04:26.000000000 +09:00
categories:
tags: [tips]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
우선, Oracle 사이트에서 oracle thin client를 다운로드 해야 한다.
이 때, 본인이 실제 사용하는 OS의 bit가 아닌, Toad의 bit와 동일한 버전의 thin client가 필요하다.
즉, 64bit Windows라도 32bit Toad 라면, 32bit Oracle thin client 설치 필요.

이후, 환경 설정은 Oracle thin client의 환경 변수 설정이다.

1. regedit를 통해 레지스트리를 변경하거나,
2. 시스템 환경변수로 선언하거나,
3. batch 파일내 선언하여 toad가 실행될 때 참조하도록 하는 방법 등이 있다.

설정의 편의상 3 방법이 가장 편할 것이다.

<strong>1. 레지스트리 변경</strong>


regedit를 실행하고, 아래 내용을 등록한다.
```
HKEY_LOCAL_MACHINESOFTWAREORACLE내
ORACLE_HOME=설치위치
NLS_LANG=KOREAN_KOREA.KO16MSWIN949
```


<strong>2. 시스템 환경변수로 선언</strong>


```
PATH에 Oracle thin client 경로 추가
ORACLE_HOME=설치위치
TNS_ADMIN=설치위치
NLS_LANG=KOREAN_KOREA.KO16MSWIN949
```


<strong>3. batch 파일로 등록</strong>


```
set PATH=설치위치;%PATH%
set ORACLE_HOME=설치위치
set TNS_ADMIN=설치위치
set NSL_LANG=KOREAN_KOREA.KO16MSWIN949
start &quot;toad&quot; &quot;toad설치위치&quot;
```

