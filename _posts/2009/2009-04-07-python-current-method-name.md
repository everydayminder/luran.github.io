---
layout: single
title: 현재 함수의 이름 얻기
date: 2009-04-07 06:39:32.000000000 +09:00
categories:
- development
tags: [python, method]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
### 현재 함수의 이름 얻기
```python
def whoami():
 import sys

 return sys._getframe(1).f_code.co_name
```

### 현재 함수의 caller 이름 얻기
```python
def callersname():
 import sys

 return sys._getframe(2).f_code.co_name</font></font>
```

출처 : Python Recipe 66062: Determining Current Function Name

