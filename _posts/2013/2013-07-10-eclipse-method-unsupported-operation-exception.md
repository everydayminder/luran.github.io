---
layout: single
title: Eclipse의 method 템플릿을 Unsupported Operation Exception을 던지도록 바꾸자
date: 2013-07-10 01:07:25.000000000 +09:00
categories:
- development 
tags: [eclipse, java]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
Windows > Preferences > Java > Code Style > Code Templates > Code > Method body부분을 살펴보면
기본값으로

```
// ${todo} Auto-generated method stub
 ${body_statement}
```
와 같이 지정되어 있다. 이것을 다음과 같이 바꿔서 써보자.

```java
throw new UnsupportedOperationException();
```

그러면, 나중에 특정 interface를 implement하는 경우와 같이 자동으로 method를 구현시키는 경우,
자동으로 UnsupportedOperationException을 던지는 메소드를 작성하게 된다.
자동으로 구현된 메소드를 그냥 의미없이 null을 리턴하거나 아무 일도 하지 않는 채로 두는 것보다,
UnsupportedOperationException을 던지는 채로 두어야 나중에 손보게 될 확률이 높아진다. (꼭 챙기게 됨)

