---
layout: single
title: CentOS 6, KVM에 guest OS를 bridge로 네트워크 설정하려면
date: 2012-02-20 14:37:59.000000000 +09:00
categories:
- system
tags: [kvm, bridge, network]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
동기는 다음과 같다.
일단 호스트는 CentOS로 두고, 웬만하면 깨끗한 상태로 유지하려고 한다.
게스트로 CentOS를 설치하고, 여기에 이런저런 작업 및 테스트를 해보려고 한다.
그런데, VMWare는 상용이고, 리눅스용은 설치하자니 번거로울 것 같기도 하고, 마침 CentOS가 가상화를 지원한다.
그러므로, OS에서 지원하는 가상화를 써보자!

CentOS6에서는 가상화로 KVM을 기본적으로 지원한다.
예전에는 Xen과 KVM을 선택하여 썼다고 하는데, 이제는 KVM만 지원한다.
일단, 무작정 KVM에 guest OS로 CentOS를 설치했더니
NAT로만 동작하는 것을 확인했다.

VMWare를 사용했을 때는 guest OS의 네트워크를 NAT 또는 bridge로 추가/ 변경 설정이 쉬웠는데,
KVM Virtualization Manager에서는 GUI상에서 설정할 수는 없는 것 같다.

인터넷에서 검색해 보니, 설정과 관련된 글을 여러 개 발견할 수 있었다.

## KVM 설치
CentOS를 설치할 때, 세부 설정을 선택할 수 있다.
Virtualization항목을 선택하면, KVM을 설치할 수 있다.
만약, 나중에 별도로 설치하고자 한다면,

```
yum groupinstall KVM
```
을 실행시키자.

## bridge-utils 설치
만약, 설치가 안되어 있다면 설치하자.
```
yum install bridge-utils
```

## network-scripts 변경 (host)
호스트가 기본적으로 사용하는 eth0의 설정을 변경하고, 브릿지로 맺어줄 설정을 추가해야 한다.
/etc/sysconfig/network-scripts/ifcfg-eth0 파일을 열어서 한 줄을 추가하고, 저장한다.

```
BRIDGE=br0
```
이제 br0의 내용을 만들자.
/etc/sysconfig/network-scripts/ifcfg-br0 파일을 만들자.

```
DEVICE="br0"
BOOTPROTO=none
IPV6INIT="yes"
IPV6_AUTOCONF="yes"
ONBOOT="yes"
TYPE="Bridge"
IPADDR=192.168.1.7
GATEWAY=192.168.1.1
DEROUTE=yes
IPV4_FAILURE_FATAL=yes
DNS1=192.168.1.1
DELAY=0
```
위 내용은 직접 설정한 값이다.
이와 같이 설정을 마치면, 일단 eth0과 br0은 연결될 것이다.

이제 KVM에서 guest OS를 설치할 때, Advanced Option을 살펴보면,
네트워크 설정 옵션으로 Bridge이 선택 가능할 것이다. (기존에는 NAT만 설정 가능)
이대로 설치하면, guest OS를 bridge network로 설정한 상태가 된다.

## NetworkManager, network 설정
```
chkconfig NetworkManger off
chkconfig network on
service NetworkManager stop
service network restart 
```

## network-scripts 변경 (guest)
이제 guest OS를 시작시키자.
마찬가지 방법으로, guest OS의 IP 등 정보를 원하는 대로 수정하자.
이렇게 설정을 하고, guest OS에 ssh 접속하였더니 내가 설정한 IP로 접속이 잘 된다.
그런데, host OS에 ssh 접속을 하였더니 갑자기 잘 안된다!

살펴보니, 나의 경우 위와 같이 설정을 하고 설치를 하지 않고, 먼저 무작정 설치를 한 까닭에
NAT로 설치가 되며 호스트 OS의 네트워크 설정에 변화가 생겨버렸다.

## virbr0 삭제
ifconfig으로 조회해 보니, virb0이 발견되었다.
가상 네트워크 리스트를 다음과 같이 확인할 수 있다.

```
virsh net-list
```

상태에 default 네트워크라고 이름이 뜨고, 상태는 active라고 표시되어 있다. 
이것을 지워주자. 
```
virsh net-destroy default
virsh net-undefine default
service libvirtd restart 
```
다시 virsh net-list를 실행하여, 정상적으로 작업이 적용되었는지 확인하자.
나의 경우, virbr0 삭제까지 마친 후, 정상적으로 host, guest OS를 원하는 IP로 맞춰서 쓸 수 있게 되었다.

교훈)
- KVM에 guest OS를 bridge를 쓰려면 먼저 bridge 설정부터 하자.
- bridge 설정을 마친 후, 새로 install 하면 간단하다.
