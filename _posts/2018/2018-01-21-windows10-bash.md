---
layout: single
title: windows10에 bash 설정
date: 2018-01-21 17:12:08.000000000 +09:00
categories:
- system
tags: [windows, bash]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
제어판 검색 창으로부터, "개발자 기능 사용"을 입력한다.
개발자용 > 개발자 기능 사용 중, "개발자 모드"를 선택한다.
다시 검색 창에서, Linux용 Windows 하위 시스템을 선택한다.

cmd를 관리자 권한으로 실행 후,

```
lxrun /install
```

를 입력한다.
일정 시간 경과 후, 만들 계정 (username, password)을 설정하면 완료된다.

