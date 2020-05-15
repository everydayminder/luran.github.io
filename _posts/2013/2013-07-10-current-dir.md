---
layout: single
title: 현재 파일 경로는 어디?
date: 2013-07-10 10:34:36.000000000 +09:00
categories: 
- development
tags: []
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
현재 애플리케이션이 실행되는 위치(경로)를 알기 어려울 때가 있다.
웹 애플리케이션을 작성하면서, 특정 위치에 있으려니 싶어 접근하다보면, 위치가 예상과 달라
configuration을 잡는데 의외로 많은 시간이 걸리곤 한다.

1. 꼼수
- 임의의 파일을 만든다.
- 해당 파일의 경로를 살펴본다.

```
System.out.println((new File(&quot;test.txt&quot;)).getAbsolutePath());
```

2. 보다 고급스러운 방법

```
System.getProperty(&quot;user.dir&quot;)
```

