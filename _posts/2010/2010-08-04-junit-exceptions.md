---
layout: single
title: JUnit에서의 예외 인식
date: 2010-08-04 13:02:51.000000000 +09:00
categories:
- development
tags:
- exception
- junit4
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
JUnit에서 작성한 어떤 테스트케이스가 Exception을 던지고, 
그 Exception이 던져진 것이 맞는 상황임을 검증하고자 한다면,

JUnit3에서는

```java
	public void testDivideByZeroV3() {
		try {
			int a = 3/0;
		} catch(Exception e) {
			assertSame(e.getClass(), ArithmeticException.class);
		}
	}
```
반면, JUnit4에서는
```java
	@Test(expected=ArithmeticException.class)
	public void testDivideByZeroV4() {
		int a = 3/0;
	}
```
예외 처리만으로도 JUnit4가 JUnit3보다 간략하다는 것을 확인할 수 있다.
