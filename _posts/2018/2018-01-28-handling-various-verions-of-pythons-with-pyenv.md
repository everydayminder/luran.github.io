---
layout: single
title: '여러 버전의 python을 관리하는 또 다른 방법 : pyenv'
date: 2018-01-28 19:24:45.000000000 +09:00
categories:
- python
tags: [pyenv]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
python3가 출시된지 오래되었으나, 아직 python2는 많이 사용되고 있다.
결국, python2와 python3의 버전 변경은 아직도 자주 겪는 일이 될 수 밖에 없을 것이다.

앞서, virtualenvwrapper를 통해 python2와 python3 switch를 하는 방법을 포스팅했으나,
다른 방법으로 여러 버전의 python을 컨트롤 하는 방법에 대해 기록으로 남긴다.

pyenv라는 툴을 사용해 보자.

## pyenv 설치

```
brew update
brew install pyenv
```

설치 후,

```
echo $PATH
```

를 해보면, 기존과 변화가 보일 것이다.


```
/Users/사용자계정/.pyenv/shims:
```

가 자동으로 추가되어있을 것이다. pyenv가 동작하면서, shims를 일종의 proxy 하기 때문에 자동으로 추가되는데,

항상 동작하도록 하기 위해, 본인의 shell profile(.bash_profile, .zshrc, .zshenv) 등에 다음과 같이 추가한다.


```
if which pyenv > /dev/null; then eval "$(pyenv init -)"; fi
```


## 필요한 버전 python 설치

다음과 같이, 설치 가능한 버전의 python을 조회할 수 있다.


```
pyenv install -list
```


다음과 같이 여러 버전의 python을 설치해 보자.

```
pyenv install 2.7.14
pyenv install 3.6.4
pyenv install 3.7-dev
```


## python version 확인

이제 시스템에 설치된 python들의 버전을 확인하자.
현재 python의 버전을 확인한다.

```
pyenv version
```


현재 python 및 설치되어 있는 (변경 가능한) 버전을 모두 확인한다.

```
pyenv versions
```


아래와 같이 출력된다.

* system (set by /Users/사용자계정/.pyenv/version)
  2.7.14
  3.6.4
  3.7-dev


## 불필요한 버전 삭제

불필요한 버전은

```
pyenv uninstall 3.7-dev
```

와 같이 버전을 직접 명시하면 삭제 가능하다. (지우는 건 순식간에 됨)


## python version 변경

현재, 내 시스템의 python 버전은 2.7.10이다.


```
python version
```
을 실행하면, 

Python 2.7.10
이 출력된다.


다음과 같이 python version을 변경해 보자.

```
pyenv global 3.6.4

```

pyenv versions를 실행해보면, * 마크가 표시된 곳이 변경된다.

  system
  2.7.14
* 3.6.4 (set by /Users/사용자계정/.pyenv/version)


```
python --version
```

하면, 변경된 버전을 확인할 수 있다.
이와 같이, 버전을 변경하는 모드에는 세 가지가 있다.


```
pyenv global 버전
pyenv local 버전
pyenv shell 버전
```

global은 전체 버전을 변경하는 command이고, local은 해당 디렉토리내 .python-version 파일을 기록하여 해당 디렉토리 한정 특정 버전으로 변경 유지한다. shell은 해당 shell의 세션내 버전 변경을 유지해준다. 필요한 것으로 사용하자.

local로 설정한 옵션은 또 local 커맨드로 overwrite 하거나, --unset 파라미터를 주거나, 직접 .python-version 파일을 지워주면 원복할 수 있다.

아래는, global은 3.6.4로 test 디렉토리에서는 2.7.14로 설정한 경우의 버전 출력 예이다.

```
~ $ pyenv version
3.6.4 (set by /Users/사용자계정/.pyenv/version)


~/test $ pyenv version
2.7.14 (set by /Users/사용자계정/test/.python-version)


~/test $ cat .python-version
2.7.14
```

