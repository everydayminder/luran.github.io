---
layout: single
title: Windows에서 overlay icon이 보이지 않을 때
date: 2014-03-16 23:32:41.000000000 +09:00
categories:
- system
tags: [windows]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
요즘에는 각종 설치하는 프로그램마다 오버레이 아이콘을 제각각 지원하는 프로그램들이 많다.
문제는, 그런 프로그램들이 많기 때문에 정작 내게 필요한 아이콘이 나오지 않아서 불편함이 생긴다는 것이다.
특히, 윈도우즈에서는 기본적으로 등록 우선순위상 15개의 아이콘만 보여주기 때문에,
사용상 불편함이 초래될 가능성이 높은 편이다.

Regedit을 실행시켜서 다음의 경로를 찾아가 보자.
```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\explorer\ShellIconOverlayIdentifiers
```
그러면 여러 개의 아이콘들이 이미 등록된 것을 확인할 수 있다.
이 때, 불필요해보이는 오버레이 아이콘 정보를 지우자.
내 경우, Tortoise SVN의 아이콘들이 출력되었으면 하는 정보였기 때문에,

Tortoise SVN외의 다른 불필요한 정보는 지워줘서, 적어도 Tortoise SVN의 오버레이 아이콘이 15등 안에 들게 변경해 주었다.

