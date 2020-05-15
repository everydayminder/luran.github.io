---
layout: single
title: 현재 클래스의 이름 얻기 + 현재 클래스의 메소드 얻기
date: 2009-02-04 08:51:04.000000000 +09:00
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
## 현재 클래스의 메소드 이름 얻기
class Base 에 대해, Base.__dict__ 라고 하면, Base에 선언된 정보를 얻을 수 있으나,

이중, 메소드 이름만 추출하고 싶다면,
```python
from types import *

def getMethodNames():
  result = []
  for attr, val in Base.__dict__.items():
    if type(val) == FunctionType:
       result.append(attr)
  return result
```

과 같이 함으로써, method 이름을 추출할 수 있다.
그러나,  이미 특정 class로부터 instance를 만든 경우에는 위의 방법을 그대로 사용할 수 없다.
즉, Base.getMethodNames()라고 하면 결과를 얻을 수 있지만,

```python
aa = Base()
aa.getMethodNames()
```

라고 하면, 데이터를 얻을 수 없다.
따라서, aa 로부터 instance하기 전의 클래스가 무엇인지 알아내야 한다.

```python
self.__class
```
라고 하면, 현재의 클래스를 리턴한다.
따라서, 위의 for 문에서 Base.__dict__items() 대신

```python
self.__class__.__dict__items()
```
라고 쓰면 된다.
이때, 클래스의 이름을 self.__class__.__name__ 으로 하여 확인할 수도 있는데,
self.__class__.__dict__.itesm() 와 eval(self.__class__.__name__).__dict__.items()는

동일한 결과물을 뽑아준다.

즉, class와 eval(class의 이름)이 동일하게 취급된다는 것.

