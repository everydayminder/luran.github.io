---
layout: single
title: Ubuntu에 Eclipse 설치하기
date: 2008-07-21 06:53:03.000000000 +09:00
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
1. 먼저, java를 설치한다.
```
  sudo apt-get install sun-java6-jdk
```

2. 시스템에서 기본적으로 동작할 java 를 선택해준다.
```
 sudo update-alternatives --config java
```
 원하는 java를 선택한다.

3. eclipse를 설치한다.
```
 sudo apt-get install eclipse
 ```

4. 원하는 패키지를 추가로 설치한다.
```
 sudo synaptic
``` 
 예) PyDev : Python 개발 플러그인

