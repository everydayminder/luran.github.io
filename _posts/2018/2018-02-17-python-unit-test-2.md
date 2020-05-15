---
layout: single
title: 'python에서의 unit test #2 - 디렉토리 구조 구성하기 + unittest 적용하기'
date: 2018-02-17 12:21:06.000000000 +09:00
categories:
- development
tags: [python, test, unittest]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
이제 프로젝트의 메인 모듈과 테스트 파일을 분리해서, 기존과 같이 동작하는지 확인해 보자.
구조는

project
ㄴ lib
  ㄴ func.py
ㄴ tests
  ㄴ test_func.py

의 구성을 따른다고 가정한다.
앞서 작성한 test_func.py의 내용의 구현부와 테스트부를 별도 파일로 나눠놓자.

```python
# lib/func.py

def add_1(x):
    return x + 1

# tests/test_func.py
def test_add_1_1plus1():
    assert add_1(1) == 2

def test_add_1_1plus2():
    assert add_1(2) == 1
```

이렇게 파일을 분리하고, nosetests를 돌리면, add_1 function을 찾을 수 없다는 에러가 나온다.
```
test_func.test_add_1_1plus1 ... ERROR
test_func.test_add_1_1plus2 ... ERROR

======================================================================
ERROR: test_func.test_add_1_1plus1
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/Library/Python/2.7/site-packages/nose/case.py", line 197, in runTest
    self.test(*self.arg)
  File "/Users/luran/devwork/unit_test/tests/test_func.py", line 2, in test_add_1_1plus1
    assert add_1(1) == 2
NameError: global name 'add_1' is not defined

======================================================================
ERROR: test_func.test_add_1_1plus2
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/Library/Python/2.7/site-packages/nose/case.py", line 197, in runTest
    self.test(*self.arg)
  File "/Users/luran/devwork/unit_test/tests/test_func.py", line 5, in test_add_1_1plus2
    assert add_1(2) == 1
NameError: global name 'add_1' is not defined

----------------------------------------------------------------------
Ran 2 tests in 0.001s
```
파일을 분리해 놓고, import를 안 해줬으니 이제 테스트 코드에 import를 해준다.

```python
# tests/test_func.py

from lib import func

def test_add_1_1plus1():
    assert func.add_1(1) == 2

def test_add_1_1plus2():
    assert func.add_1(2) == 1
```

다시 실행하면, 다른 에러가 나온다. 이번에는 lib를 찾을 수 없다는 에러이다.
```
Failure: ImportError (No module named lib) ... ERROR

======================================================================
ERROR: Failure: ImportError (No module named lib)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/Library/Python/2.7/site-packages/nose/loader.py", line 418, in loadTestsFromName
    addr.filename, addr.module)
  File "/Library/Python/2.7/site-packages/nose/importer.py", line 47, in importFromPath
    return self.importFromDir(dir_path, fqname)
  File "/Library/Python/2.7/site-packages/nose/importer.py", line 94, in importFromDir
    mod = load_module(part_fqname, fh, filename, desc)
  File "/Users/luran/devwork/unit_test/tests/test_func.py", line 1, in <module>
    from lib import func
ImportError: No module named lib

----------------------------------------------------------------------
Ran 1 test in 0.001s

FAILED (errors=1)
```

project내부에서 디렉토리 구조를 서로 인식하게 해주기 위해, tests 디렉토리와, lib 디렉토리 내에서 __init__.py를 생성해 준다.

```
touch __init__.py
```

그러면, 디렉토리 구조는 다음과 같이 변경된다.
project
ㄴ lib
  ㄴ __init__.py
  ㄴ func.py
ㄴ tests
  ㄴ __init__.py
  ㄴ test_func.py

이제 앞서 실행했던 테스트를 실행하면, 처음에 실행한 것과 같은 결과를 볼 수 있다.

```
$ nosetests -v test_func.py --with-coverage

tests.test_func.test_add_1_1plus1 ... ok
tests.test_func.test_add_1_1plus2 ... FAIL

======================================================================
FAIL: tests.test_func.test_add_1_1plus2
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/Library/Python/2.7/site-packages/nose/case.py", line 197, in runTest
    self.test(*self.arg)
  File "/Users/luran/devwork/unit_test/tests/test_func.py", line 7, in test_add_1_1plus2
    assert func.add_1(2) == 1
AssertionError

Name              Stmts   Miss  Cover
-------------------------------------
lib/__init__.py       0      0   100%
lib/func.py           2      0   100%
-------------------------------------
TOTAL                 2      0   100%
----------------------------------------------------------------------

Ran 2 tests in 0.002s

FAILED (failures=1)
```

이제, unittest를 적용해 보자.
기존 테스트 케이스를 아래와 같이 변경한다.
```python
import unittest
from lib import func

class MyTest(unittest.TestCase):
    def test_add_1_case1(self):
        self.assertEqual(func.add_1(1), 2)

    def test_add_1_case2(self):
        self.assertEqual(func.add_1(1), 1)
```

기존보다 읽기는 더 어려워졌다.
그러나, assertion 실패시 좀더 구체적인 에러 메시지를 보여준다.

```python
    self.assertEqual(func.add_1(1), 1)
AssertionError: 2 != 1
```

assert문의 종류에는 assertEqual외에도 assertNotEqual, assertTrue, assertFalse 등을 활용할 수 있다.
그런데, 부가적으로 작성해줘야 하는 코드가 너무 많은 것 또한 사실이다.


