---
layout: single
title: Ubuntu console mode로 로그인하기
date: 2008-07-22 09:15:36.000000000 +09:00
categories:
- system
tags: []
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
Ubuntu-desktop은 GUI모드로 부팅된다.
redhat 계열 리눅스와는 달리 runlevel을 수정하는 것만으로는 동작하지 않는다.
```
$ sudo mv /etc/rc3.d/S30gdm /etc/rc3.d/K30gdm
```

(내 경우에는 rc2.d에서 해줘야 먹혔음)
참고로,
Ubuntu에서 X윈도우를 터미널창에서 끄고 시작하려면,
```
$ sudo /etc/init.d/gdm start(stop, restart)
```

Ubuntu에서 X윈도우를 자동으로 실행하지 않고 터미널로 시작되도록 설정하는
명령어
```
$ sudo update-rc.d -f gdm remove
```

다시, X윈도우를 자동으로 띄우려면,
```
$ sudo update-rc.d gdm defaults
```
