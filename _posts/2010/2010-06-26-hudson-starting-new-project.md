---
layout: single
title: hudson - 프로젝트 생성하기
date: 2010-06-26 11:47:37.000000000 +09:00
categories:
- development
tags: [hudson, jenkins]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
Hudson을 설치했으므로, 이제 프로젝트를 생성하자.

"새작업"을 클릭하여, 새 프로젝트를 생성한다.
임의의 프로젝트 이름을 입력하고, "Build a free-style software project"를 선택한다.

다음과 같은 세부 설정 화면을 볼 수 있다.
필요한 정보를 모두 입력한다.

<img src="{{ site.baseurl }}/images/201006/cfile24-uf-173ea1044c25e4766b5f63.png" class="aligncenter" width="700" height="485" alt="" />
이번에 www.unfuddle.com에 생성한 무료 SVN을 연결하여 프로젝트를 생성하기로 한다.
Source Code Management 메뉴로부터, Subversion을 선택하면, 다음 화면을 볼 수 있다.

<img src="{{ site.baseurl }}/images/201006/cfile7-uf-11247e194c25e51b03a182.png" class="aligncenter" width="700" height="253" alt="" />
Repository URL 옆의 ? 버튼을 클릭하여, SVN 위치와 인증 정보를 모두 입력하자.
아래의 화면과 같이 나올텐데, "this link"를 클릭하면 인증 정보를 입력할 수 있는 화면이 나온다.

<img src="{{ site.baseurl }}/images/201006/cfile10-uf-1222571b4c25e815b58177.png" class="aligncenter" width="700" height="109" alt="" />
다음 작업은 Hudson으로 build 작업을 지정하여 소스 변경시 혹은 주기적으로 Continuous Integration이 되도록 해야 할 것이다.
