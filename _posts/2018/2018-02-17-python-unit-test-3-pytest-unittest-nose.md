---
layout: single
title: 'python에서의 unit test #3 - pytest (unittest + nose의 대안)'
date: 2018-02-17 16:27:08.000000000 +09:00
categories:
- development
tags: [python, unittest, pytest]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
unittest를 많이 쓰고 있는 것 같으나, 너무 많은 bolierplate를 작성해 줘야 한다는 느낌이 들어 대안을 찾아본다.
py.test를 실험해 보자.

설치는 다음과 같다.
```
pip install pytest
```

앞서 unittest용으로 작성한 테스트를 변경한다. 원래 버전에 가깝게 되었다.
```python
# test_by_pytest.py

from lib import func

def test_add_1_case1():
    assert func.add_1(1) == 2

def test_add_1_case2():
    assert func.add_1(1) == 1
```

이제 pytest로 해당 결과를 보면, 다음과 같이 더 가독성이 좋은 리포트가 나온다.

```
pytest -v test_by_pytest.py

================================================================================================= test session starts ==================================================================================================
platform darwin -- Python 3.6.4, pytest-3.4.0, py-1.5.2, pluggy-0.6.0 -- /Users/luran/.pyenv/versions/3.6.4/envs/venv-3.6.4/bin/python
cachedir: .pytest_cache
rootdir: /Users/luran/devwork/unit_test/tests, inifile:
collected 2 items

test_by_pytest.py::test_add_1_case1 PASSED                                                                                                                                                                       [ 50%]
test_by_pytest.py::test_add_1_case2 FAILED                                                                                                                                                                       [100%]

======================================================================================================= FAILURES =======================================================================================================
___________________________________________________________________________________________________ test_add_1_case2 ___________________________________________________________________________________________________

    def test_add_1_case2():
>       assert func.add_1(1) == 1
E       assert 2 == 1
E        +  where 2 = <function add_1 at 0x10bb53840>(1)
E        +    where <function add_1 at 0x10bb53840> = func.add_1

test_by_pytest.py:7: AssertionError
========================================================================================== 1 failed, 1 passed in 0.04 seconds ==========================================================================================
```

nose에서 coverage를 측정했듯이, pytest로 coverage를 측정하기 위해, 툴을 하나 더 설치하자.

```
pip install pytest-cov
```
다음과 같이 실행하면, coverage를 포함한 결과가 나타난다.

```
pytest --cov=lib tests/test_by_pytest.py

...

---------- coverage: platform darwin, python 3.6.4-final-0 -----------
Name              Stmts   Miss  Cover
-------------------------------------
lib/__init__.py       0      0   100%
lib/func.py           2      0   100%
-------------------------------------
TOTAL                 2      0   100%

....
```
coverage report는 아래와 같이 옵션을 지정하여 추출할 수 있다.

```
--cov_report html
--cov_report xml
```
동시에 지정할 수도 있는데, html의 경우는 디렉토리로, xml의 경우는 단독 파일로 결과가 export된다.
pytest로 테스트 코드를 작성하는데 unittest에 비해 더 적은 노력이 들고, 똑같이 coverage도 측정된다.
(물론, fixture나 mock 등도 더 살펴 봐야할 것이지만)

