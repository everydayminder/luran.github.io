---
layout: single
title: 'Python project를 위한 Docker 기반 Jenkins 설정하기 #1'
date: 2018-02-25 09:06:52.000000000 +09:00
categories:
- python
- tools
tags: []
meta:
  _edit_last: '2'
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
앞서 Docker의 Kitematic으로 Jenkins 이미지를 받아서 띄워봤는데, Python 프로젝트를 위해 몇몇 설정을 변경하기 위해
직접 Docker 이미지를 만들어보자.

<h3>1. Dockerfile 작성</h3>
Dockerfile을 만들고 아래와 같이 내용을 넣자.

```
# Dockerfile
FROM jenkins:latest
USER root

RUN apt-get update

# pip 설치
RUN apt-get install -y python-pip
ENV JAVA_ARGS -Xms512m -Xmx1024m

RUN apt-get update && \
    apt-get install -y make build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev wget

# pyenv 설치/ 설정
RUN git clone https://github.com/pyenv/pyenv.git .pyenv
ENV PYENV_ROOT $HOME/.pyenv
ENV PATH $PYENV_ROOT/shims:$PYENV_ROOT/bin:$PATH

# python v2, v3 설치
RUN pyenv install 2.7.14
RUN pyenv install 3.6.4
RUN pyenv rehash
```

<h3>2. Docker 이미지 생성</h3>
이제 앞서 작성한 Dockerfile을 사용하여 Docker 이미지를 만들자.

```
docker build -t "jenkins:python" yourpath
```

<h3>3. Docker 실행</h3>
다음과 같이 실행하자.

```
docker run -p 8080:8080 -v host_directory:container_directory jenkins:python
```

-p로 포트를 매핑하고, -v로 마운트할 디렉토리를 지정하자.
docker를 실행할 때마다, jenkins를 매번 초기화/ 설정하고 싶지 않다면, volume 마운트를 하자.
설정한 내용을 host에 기록하고, container를 띄울 때 마운트한다.
jenkins의 기본 디렉토리가 var/jenkins_home이므로, 아래와 같이 실행한다.

```
docker run -p 8080:8080 -v mydirectory:/var/jenkins_home jenkins:python
```

이후, jenkins를 unlock하고, 필요한 기본 plugin들을 업데이트하면 된다.
나중에 다시 jenkins를 다시 시작시켜도 기존에 설정한 내용들이 유지된다.

