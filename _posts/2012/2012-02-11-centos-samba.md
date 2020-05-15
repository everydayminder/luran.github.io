---
layout: single
title: CentOS samba 설치/설정
date: 2012-02-11 06:56:46.000000000 +09:00
categories:
- system
tags: [centos, samba, smb]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
## 1. samba 설치
우선 samba를 설치하자.
```shell
yum install samba
```

### 2. 사용자 추가
samba를 사용할 사용자를 추가하고, 그 사용자를 samba 사용자로 등록하자.

```shell
useradd luran
passwd luran
```

luran이라는 사용자를 추가하였다.
그러면, 이 계정은 다음과 같이 samba 사용자로 추가할 수 있다.

```shell
smbpasswd -a luran
```

### 3. samba 환경 설정
/etc/samba/smb.conf를 수정하자.

```shell
workgroup = WORKGROUP
hosts allow = 192.168.1.
```
으로 설정하였다.
나의 데이터가 있는 곳을 추가해 주었다.

```shell
[mydata]
 comment = my data
 path = /mnt/data/share
 public = yes
 writable = yes
 write list = luran
 create mask = 0777
 directory mask = 0777
```

### 4. selinux 설정
selinux가 사용중으로 설정되어 있으면 samba 접속이 안되므로, 사용 해제하자.
다음 방법 중, 한 가지를 하면 되겠다.

```shell
setenforce 0

또는

vi /etc/selinux/config
SELINUX= disabled (기본 값은 enforcing)
```

sestatus -v 를 실행하면, selinux의 상태를 확인할 수 있다.

### 5. 방화벽 설정
vi /etc/sysconfig/iptables 를 열어서, 다음과 같이 추가한다.
```shell
-A INPUT -m state --state NEW -m tcp -p tcp --dport 137:139 -j ACCEPT
-A INPUT -m state --state NEW -m udp -p udp --dport 137:139 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 445 -j ACCEPT
```
추가한 후에 저장하고, iptables를 재시작 시킨다.

### 6. samba 시작
일회적으로 혹은 지금 당장 실행시키려면,

```shell
service smb start
```
로도 충분할 것이다.
그러나, 부팅마다 자동으로 실행시키고 싶다면, 다음과 같이 하자.

```shell
# 현재 samba 설정 상태 조회
$ chkconfig --list smb
smb       0:off  1:off  2:off  3:off  4:off  5:off  6:off

# ### 부팅시 자동 시작되게 바꾸기
$ chkconfig smb on
$ chkconfig --list smb

smb       0:off  1:off  2:on  3:on  4:on  5:on  6:off</div>

# runlevel 3, 5에서 시작되게 바꾸기
$ chkconfig --level 35 smb on
$ chkconfig --list smb</div>
smb       0:off  1:off  2:off  3:on  4:off  5:on  6:off
```

### 7. 윈도우즈에서 접속 테스트
탐색기나, 실행 창을 띄워서 \접속아이피를 입력하자.
그러면, 인증 창이 뜨고, 앞서 설정한 계정 정보를 요청할 것이다.

### 8. 한글 디스플레이 설정
global 옵션에 다음과 같이 추가한다.

```shell
dos charset = cp949
unix charset = cp949
```

### 9. socket option 최적화

사람들이 많이 하는 옵션이다. 이런 건 따라하자.
```shell
socket option = TCP_NODELAY SO_RCVBUF=8192 SO_SNDBUF=8192
```

### 10. 기타
내 경우, 위와 같이 설정한 후에 윈도우즈에서 연결했으나, 제한된 리소스라는 에러가 떴으나,
service nmb restart 도 해줬더니 동작이 되었다.




