---
layout: single
title: pyenv + virtualenv + autoenv
date: 2018-02-11 23:55:12.000000000 +09:00
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
앞서 
1. virtualenv 기반 환경 설정 방법
2. virtualenvwrapper를 사용하여 환경 전환 편의성을 높이는 방법
에 대해 정리해 뒀다.


python2.x와 python3.x가 로컬에 이미 설치되어 있을 때는 위 방법만으로도 충분할 수도 있다.

그런데, 더 다양한 버전의 python을 설치하고 테스트해 봐야한다면 이 방법만으로도 괜찮을까?


pyenv를 써서, 다양한 버전의 python을 쉽게 설정할 수 있다는 것도 알아봤는데, 이와 virtualenv를 조합하면 어떨까?

본 포스트에서는, 다양한 버전의 python을 pyenv와 virutalenv를 조합 사용하는 방안에 대해 기록하고자 한다.


## pyenv-virtualenv 설치

```
brew install pyenv-virtualenv
```

위와 같이 입력하고 실행하면 잠시 후 설치완료 후, 다음과 같은 메시지가 뜬다.


```
To enable auto-activation add to your profile:
  if which pyenv-virtualenv-init > /dev/null; then eval "$(pyenv virtualenv-init -)"; fi
```


## pyenv를 활용한 virtualenv 생성

앞서 설정한 python 버전들은 다음과 같다.

```
  system
  2.7.14
* 3.6.4 (set by /Users/luran/.pyenv/version)
```

이제 3.6.4 버전의 virtualenv를 만들어 보자.

```
pyenv virtualenv 3.6.4 venv-3.6.4
```

이후, pyenv versions를 실행해 보면, 이제 새로 만든 virtual environment가 목록에 추가되었음을 확인할 수 있다.


```
* system (set by /Users/luran/.pyenv/version)
  2.7.14
  3.6.4
  3.6.4/envs/venv-3.6.4
  venv-3.6.4
```

이제 다음과 같이, 해당 virtual environment에 진입하고, 나올 수 있다.


```
# 명시적으로 activate
pyenv activate venv-3.6.4
pyenv deactivate

# shell 기준 적용
pyenv shell venv-3.6.4

# local 기준 적용 : 해당 디렉토리에 .python-version 파일이 생기며, 이 디렉토리에 진입하면 항상 이 virtual env로 전환됨

pyenv local venv-3.6.4

# 원복
pyenv local system
or
rm .python-version


# global 기준 적용 : 무조건 전체 적용
pyenv global venv-3.6.4
pyenv global system
```


## autoenv를 통한 가상 환경 적용 자동화

autoenv라는 툴을 먼저 설치하자. 이 녀석은 이름이 뜻하는 바와 같이, 자동으로 환경을 잡아주는 역할을 도와준다.

```
brew install autoenv
```

.bash_profile(bash) 혹은 .zshrc(zsh)에 다음과 같이 내용이 반영되도록 하자.


```
echo 'source /usr/local/opt/autoenv/activate.sh' >> .zhsrc
```

이제 원하는 디렉토리에 .env를 작성하고, 실행을 원하는 내용을 적으면 된다.

```
pyenv shell venv-3.6.4
```

예를 들어, 설정한 디렉토리가 test 디렉토리라 하면,


```
cd test
```

라고 치는 것만으로, pyenv shell 명령어가 실행될 것이다.


## autoenv vs. pyenv local

pyenv shell을 하면, 그 shell의 세션이 유지되는 동안 (venv-3.6.4)가 유지될 것이다.

다른 디렉토리로 간다하더라도.


pyenv activate를 하면, 그 디렉토리에서 나갈 때도 어딘가에서는 autoenv를 통해 deactivate를 해줘야 자동으로 원상복구되지 않을까?

아니면, 수동으로 매번 pyenv deactivate 하던가.


autoenv를 쓰는 목적이, 그 디렉토리에 진입할 때마다 오로지 특정 virtualenv를 자동으로 설정하고 싶은 것이라면,

굳이 autoenv를 쓰지 않아도 되지 않나 생각한다. pyenv local을 쓰면 그 디렉토리에서 나가면 자동으로 원래대로 돌아간다.

