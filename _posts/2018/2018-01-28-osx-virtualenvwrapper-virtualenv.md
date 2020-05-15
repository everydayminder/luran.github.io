---
layout: single
title: OSX) virtualenvwrapper로 virtualenv 편하게 쓰기
date: 2018-01-28 10:39:15.000000000 +09:00
categories:
- development
tags: [python, virtualenv, virtualenvwrapper]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
python 개발 환경 설정 중, virtualenv를 쓰다 매번 activate를 하기 위해 설정위치/bin/activate로 활성화하고, deactivate로 비활성화 하는 것이 번거로웠는데, 이를 좀더 쉽게 도와주는 virtualenvwrapper를 알게 되었다.

1. 설치

```
sudo pip install virtualenv virtualenvwrapper

```

로 하면 될텐데, 내 경우는 패키지 설치도중 permission 에러가 발생하여,


```
sudo pip install --ignore-installed virtualenv virtualenvwrapper
```

로 설치완료했다.


2. 환경 설정

```
mkdir ~/virtualenvs
``` 


.bash_profile 또는 .zshrc 등과 같은 본인 SHELL 설정 파일에

```
export WORKON_HOME=~/virtualenvs
source /usr/local/bin/virtualenvwrappper.sh
```
를 추가한다.


설정 완료 후, iterm 등 shell을 열어서,

```
echo $WORKON_HOME
lsvirtualenv
```

등과 같은 명령을 실행시켜서 정상 설정 여부를 확인하자.


3. 주요 명령어

```
virtualenvwrapper
```

라고 실행시키면, 다음과 같이 주요 명령어 리스트가 출력된다.


```
  add2virtualenv: add directory to the import path
  allvirtualenv: run a command in all virtualenvs
  cdproject: change directory to the active project
  cdsitepackages: change to the site-packages directory
  cdvirtualenv: change to the $VIRTUAL_ENV directory
  cpvirtualenv: duplicate the named virtualenv to make a new one
  lssitepackages: list contents of the site-packages directory
  lsvirtualenv: list virtualenvs
  mkproject: create a new project directory and its associated virtualenv
  mktmpenv: create a temporary virtualenv
  mkvirtualenv: Create a new virtualenv in $WORKON_HOME
  rmvirtualenv: Remove a virtualenv
  setvirtualenvproject: associate a project directory with a virtualenv
  showvirtualenv: show details of a single virtualenv
  toggleglobalsitepackages: turn access to global site-packages on/off
  virtualenvwrapper: show this help message
  wipeenv: remove all packages installed in the current virtualenv
  workon: list or change working virtualenvs
```
이 help에 안내된 바와 같이,
설정을 만들 때는, mkvirtualenv를 사용하고, 설정한 프로파일로 변경할 때는 workon을 사용하고, 빠져나올 때는 기존처럼 deactivate를 하면 된다.

mkvirtualenv: Create a new virtualenv in $WORKON_HOME

라고 되어 있듯이,설정을 해당 디렉토리에 모아서 생성해 주는 일을 한다.
결국, $WORKON_HOME으로 이동해서 source $WORKON_HOME/설정프로파일 디렉토리/bin/activate를 직접 실행시켜도 동일하게 동작한다. (원래 virtualenv 사용법)


예를 들어 python3 디렉토리에 python3 설정을 해뒀다면,

```
source python3/bin/activate
(개발)
deactivate
```

라고 작업하는 것과 다음 작업이 동일하다.

```
workon python3
(개발)
deactivate
```

