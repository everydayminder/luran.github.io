---
layout: single
title: 불필요 ethernet interface 정보 정리하기
date: 2008-07-18 01:05:55.000000000 +09:00
categories: [linux, vm]
tags: [linux]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
VMWare를 쓰면서, 만들어둔 이미지를 여러번 복사하면서 쓰다보니
불필요한 ethernet interface 정보가 생성됐다.


eth0, eth1만 필요한데,
막상 인터페이스 정보를 보면, eth4, eth5로 생성되어 있는 이 현상을
정리하려면 다음과 같이 하면 된다.

```
$ cd /etc/udev/rules.d
$ vi 70-persistent-net.rules
```

파일을 열어서, 내용을 다 지워주고, 재부팅한다.
재부팅 후, 네트워크 인터페이스를 조사하면, 다시 eth0, eth1 등으로 
재 설정된 것을 확인할 수 있다.


이제 IP를 원하는 대로 설정하고 깨끗하게 쓰자.

