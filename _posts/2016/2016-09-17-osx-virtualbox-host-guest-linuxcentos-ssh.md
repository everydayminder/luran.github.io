---
layout: single
title: OSX VirtualBox의 host에서 guest linux(CentOS)로 ssh 접속 설정하려면?
date: 2016-09-17 19:32:05.000000000 +09:00
categories:
- system
tags: [virtualbox, ssh]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
윈도우즈 기반 VirtualBox 환경에서는,
1. 게스트의 네트워크를 브릿지 네트워크로 설정하거나
2. NAT로 설정하고, 포트포워딩 설정을 VirtualBox에서 해줘서 ssh 접속하는 방식으로 사용했다.

OSX에서도 물론 방식 1로 하면 이상없이 사용할 수 있다.
브릿지 방식이 아닌, 호스트 네트워크로 설정해 보자.

어댑터2를 새로 추가하고 호스트 전용 어댑터를 선택하면, "잘못된 설정 감지됨" 에러가 뜬다.
이를 해결하기 위해,

VirtualBox > 환경설정 > 네트워크 > 호스트 전용 네트워크 탭 선택 후,

({{ site.url }}/images/201609/e18489e185b3e1848fe185b3e18485e185b5e186abe18489e185a3e186ba-2016-09-17-e1848be185a9e18492e185ae-6-54-09.png)
우측의 추가 버튼을 클릭한다.

({{ site.url }}/images/201609/e18489e185b3e1848fe185b3e18485e185b5e186abe18489e185a3e186ba-2016-09-17-e1848be185a9e18492e185ae-6-54-45.png)
우측의 추가 버튼을 클릭한다. 그러면, vboxnet0이 추가되고, 다시 이전의 vm 인스턴스 설정 화면에서 네트워크 설정을 변경하면, 이제 호스트 전용 네트워크 (어댑터2)를 선택하더라도 잘못된 설정이라고 뜨지 않는다.

VM 인스턴스 시작 후, 네트워크 설정을 원하는대로 마친 후 호스트에서 게스트로 직접 ssh 연결을 시도하면 될 것이다.

