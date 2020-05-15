---
layout: single
title: 현재 클래스 이름, 메소드 이름, 라인 넘버 얻기
date: 2009-04-07 08:55:13.000000000 +09:00
categories:
- development
tags: [python]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
현재 실행하는 클래스의 이름과 메소드 이름, 라인 넘버를 얻어보자.
클래스의 인스턴스에서 호출한다는 가정하에,

```python
def trace(obj, toList = False):
	import sys
	className = obj.__class__.__name__

	methodName = callersname()
	lineNumber = sys.exc_info()[2].tb_lineno

	if toList:
		return className, methodName, lineNumber
	else:
		return "%s.%s[%d]" % (className, methodName, lineNumber)
```
단, callersname()은

```python
def callersname():
	return sys._getframe(2).f_code.co_name
```

그러나, 호출하는 클래스가 특정 클래스를 상속한 경우,
부모에 정의된 함수를 자식 클래스에서 그대로 사용하면서 trace()를 호출하였다면,
부모에 정의된 메소드를 실행하고 있더라도, 자식 클래스의 이름을 
현재 클래스의 이름으로 리턴한다.

즉, A에 a()가 정의되어 있고, b = B(A)이나, b.a()를 실행도중 trace()를 호출하면, 
현재 클래스의 이름이 A로 출력되는 대신 B로 출력된다.

