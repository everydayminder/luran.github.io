---
layout: single
title: virtualenvwrapper를 사용한 python3 virtualenv 설정
date: 2018-01-28 11:13:34.000000000 +09:00
categories:
- development
tags: [python, vim, virtualenv, virtualenvwrapper]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
보통 python 2.7.x가 설치가 되어 있고, python3은 별도 설치해야 하는데,
여전히 ver 2.x에 대한 의존도가 있어서, 별도 개발환경을 3.x로 맞추고 싶다.

```
brew install python3
```

로 osx에 python3을 설치하자.
설치를 완료하면,

```
$ python

Python 2.7.10 (default, Jul 30 2016, 18:31:42)
[GCC 4.2.1 Compatible Apple LLVM 8.0.0 (clang-800.0.34)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
```

/usr/local/bin/python3에 symbolic link가 있는 것을 확인하고,
```
$ python3

Python 3.6.4 (default, Jan  6 2018, 11:51:15)
[GCC 4.2.1 Compatible Apple LLVM 9.0.0 (clang-900.0.39.2)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
```

매번, python3라고 실행할 수는 없지 않나?
그리고, 라이브러리 의존성도 생각해야지.

virtualenvwrapper가 설치된 상태에서, 다음과 같이 실행하자.

```
mkvirtualenv -p /usr/local/bin/python3 python3
```

그러면, $WORKON_HOME내, python3 프로파일이 설정되고, 

```
workon python3
```

를 실행하는 것으로, python3 환경으로 전환할 수 있다.

```
(python3) $ python

Python 3.6.4 (default, Jan  6 2018, 11:51:15)
[GCC 4.2.1 Compatible Apple LLVM 9.0.0 (clang-900.0.39.2)] on darwin

Type "help", "copyright", "credits" or "license" for more information.
```

물론, venv에서 벗어나려면 deactivate 

