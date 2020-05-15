---
layout: single
title: FreeNX 설치 (CentOS 6.x; 64bits)
date: 2013-12-04 13:06:47.000000000 +09:00
categories: 
- system
tags: [freenx, linux]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
VNC보다 성능이 좋다고 이름이 나있는 FreeNX (www.nomachine.com)을 설치하는 방법은 의외로 간단하다.

환경 : 서버측 CentOS6.4 (64bits)
클라이언트측 Windows7 (64bits)

CentOS6.x 기준으로 설치하고, 클라이언트 접속을 테스트해보자.
먼저 yum 명령어로 freenx를 다음과 같이 간편하게 설치할 수 있다.
freenx는 extras repository에 이미 있기 때문에

```
yum install freenx
yum install nxagent
```

(내 경우, nxagent를 처음에 설치하지 않았더니,
freenx는 정상적으로 설치하고, 계정까지 생성했지만, 접속이 실패하였고
로그를 보았더니 nxagent를 찾을 수 없다는 에러가 발견되었다.)

이 명령만으로도 freenx의 설치는 일단 완료된다.
freenx의 설정관련 내용은 /etc/nxserver에 설치된다.

server의 환경설정은 node.conf를 수정하는 것만으로 완료되는데,
node.conf를 백업해두고, node.conf.sample의 사본으로 작업하자.

```
mv node.conf node.conf.bak
cp node.conf.sample node.conf
```

vi node.conf를 실행하여, nxserver의 환경설정을 다음과 같이 변경/주석 해제한다.

```
SSHD_PORT=22 (주석 해제)
ENABLE_PASSDB_AUTHENTICATION=&quot;1&quot;
ENABLE_SSH_AUTHENTICATION=&quot;1&quot;
```

파일을 저장한다.
nxserver를 실행하면 여러가지 실행 옵션이 있다.
nxserver --status를 실행하여 현재 상태를 확인한다.

NX&gt; 100 NXSERVER - Version 3.2.0-74-SVN OS (GPL, using backend: not detected)
NX&gt; 110 NX Server is running
NX&gt; 999 Bye

현재 설치된 버전의 3.2.0-74이고, 동작중이라는 것을 알 수 있다.

```
nxsever --stop
```
을 실행하면, freenx가 종료된다.
이제 사용자를 추가한다.

```
nxserver --adduser luran
```

freenx 접속을 위해 luran이라는 계정이 생성될 것이다.

```
nxserver --passwd luran
```

luran이라는 계정의 패스워드를 물어올 것이다.
여기까지 실행하면,
/home/luran/.ssh 디렉토리에 ssh 접속을 위한 키가 생성되었을 것이고,
/etc/nxserver에도 해당 ID용 공개키가 client.id_dsa.key 로 생성되었을 것이다.

이제 client.id_dsa.key파일 또는 파일의 내용을 실제 접속할 클라이언트에서 쓸 수 있도록 복사하자.
만약, SSH 기본 포트 이외의 다른 포트로 변경했다면 방화벽 룰도 변경해줘야 한다.

```
nxserver --start
```
를 실행하여 띄운다.
클라이언트에서 설정할 내용은 간단하다.
Windows의 경우, 3.5 버전의 NXClient를 구해서 설치한 후,

Host와 Port 정보를 기입하고, Key 버튼을 클릭하여 앞서 별도로 저장한 key 파일을 지정한다.
Desktop은 Unix/GNOME으로 하고, 접속속도는 원하는대로 설정한다.

앞서 생성한 계정정보를 입력하는 것으로, 설정을 모두 마쳤다.
이제 접속하여 창이 뜨는지 확인하자.

