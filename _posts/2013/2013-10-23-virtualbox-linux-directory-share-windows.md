---
layout: single
title: VirtualBox Linux- 호스트 Windows간 폴더 공유 설정하기
date: 2013-10-23 05:56:38.000000000 +09:00
categories:
- system
tags: [virtualbox, host]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
VirtualBox에 설치한 CentOS instance와 내 PC의 OS인 Windows간 폴더 공유를 위한 설정 도중, 발생하는 일에 대해
메모를 남겨둔다.
일단, VirtualBox상 CentOS이미지로부터 설정 &gt; 공유폴더 메뉴를 활성화 시키면,
어떤 폴더를 공유할 것인지 설정할 수 있다.


예)
  폴더경로 d:/vbox/shared
  폴더이름 shared

그 후, Linux로 들어가면 끝!
..

이면, 정말 좋겠다.

shared라는 이름으로 공유하기로 했다만, linux상에서 저 녀석을 알 수 있도록 해줘야 할 것이다.
linux에 들어가서, 다음과 같이 입력해보자.


```
cd /mnt
mkdir shared
mount -t vboxsf shared /mnt/shared
```

아마, 별다른 설정을 예전에 하지 않았다면,

```
/sbin/mount.vboxsf: mounting failed with the error: No such device
```

와 같은 에러가 발생할 것이다.
그래서, 저 device를 사용할 수 있도록 VirtualBox 화면 상단의 메뉴탭으로부터 "장치 &gt; 게스트확장 설치"를 찾아 설치를 시작한다.
이전에 별도 설정을 하지 않았다면 또다시 다음과 같은 에러가 발생할 수 있다.


```
Building the main Guest Additions module [FAILED]
(Look at /var/log/vboxadd-install.log to find out what went wrong)
```

그래서, 저 로그 파일을 찾아 열어보니, 다음과 같은 내용이 있었다.
```
/tmp/vbox.0/Makefile.include.header:97: *** Error : unable to find the source of your current Linux kernel. Specify KERN_DIR=&amp;lt;directory&amp;gt; and run
Make again. Stop.
```

일단, kernel을 업데이트하자.

```
yum update kernel*
reboot
```

이제 패키지를 설치하자.
```
yum install gcc kernel-devel kernel-headers dkms make bzip2
```

이제 다시, 게스트 확장 설치를 시도하자.
바탕화면에 존재할 이미지를 클릭하여 곧바로 실행시키거나,

```
mkdir /media/VirtualBoxGuestAdditions
mount -r /dev/cdrom /media/VirtualBoxGuestAdditions
```

와 같이 마운트한 후, 해당 디렉토로 이동하여 실행시키자.
그러면, 이전에 실패한 부분이 정상적으로 패스되는 것을 알 수 있을 것이다.
이제 다시 마운트시켜보자.

```
mount -t vboxsf shared /mnt/shared
```

앞서 발생한 device를 발견할 수 없다는 에러 대신, 원래 공유하고자 했던 내용이 정상적으로 리스트로 나타날 것이다.
재부팅 후, 다시확인해보자.
그런데, /mnt/shared에 가보면, 내용이 보이지 않는다. 즉, 다시 마운트 해줘야 된다. 번거롭게.
한편, /media 디렉토리에 가보면, sf_로 시작하는 디렉토리가 있을 것이다.

위의 설정대로라면, sf_shared라는 디렉토리. 결국, 제일 먼저 설정한 VirtualBox에서 항상 마운트하기 옵션을 활성화 시켰을 때,
버추얼박스에서 sf_라는 접두어를 붙여 자동으로 마운트해준다.

결국, /media/sf_내가부여한이름으로 액세스할 수 있게 된다.
만약, 앞서 테스트한 바와 같이 /mnt/shared로 계속 액세스 하고자 한다면, /etc/profile에 mount 명령을 적어주자.

/mnt/profile에

```
mount -t vboxsf shared /mnt/shared
```
그러면, /media/sf_shared로도, /mnt/shared로도 공유폴더를 액세스할 수 있을 것이다.

