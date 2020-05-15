---
layout: single
title: 내 VIM이 설치된 곳은 어디?
date: 2008-10-29 13:08:22.000000000 +09:00
categories:
- system
tags: [vim]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
linux에서 vi를 쓰다보면, 어디에 설치되었는지 모르는 웃지 못할 상황이 생기곤 한다.
type/ which 명령어를 써도 명확히 알기 어렵다.

이럴 때!!
vi를 실행시킨 상태에서 확인이 가능하다는 사실!

```
:!echo $VIMRUNTIME
```
