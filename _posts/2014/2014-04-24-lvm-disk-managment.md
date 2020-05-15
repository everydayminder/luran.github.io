---
layout: single
title: 두 개의 서로 다른 물리 디스크를 하나로 묶어서 쓰려면? - lvm 설정 예
date: 2014-04-24 00:25:26.000000000 +09:00
categories:
- system
tags: [linux, lvm]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
* OS : CentOS6 (64 bits)
* Virtualization S/W : VirtualBox

대략 순서는 다음과 같다.
하드드라이브설치/인식 -> 파티션설정(lvm) -> 물리볼륨설정 -> 볼륨그룹생성 -> 논리볼륨설정 -> 파일시스템설정

1. VirtualBox 하드 디스크 추가 설정
기존에 존재하는 vm 이미지의 context menu로부터 repository를 추가한다.
가정한 시나리오는 물리 디스크 두 개이므로, 하드 디스크 두 개를 추가하였다.
해당 vm instance를 시작시켜서, 추가한 하드디스크가 정상적으로 인식되는지 확인한다.

```
# fdisk -l
```
을 수행하여,
/dev/sdb
/dev/sdc

두 개가 추가된 것을 확인한다.

2. LVM 파티션 생성
fdisk를 사용하여 하드디스크 /dev/sdb, /dev/sdc에 LVM 타입의 파티션을 다음과 같이 생성한다.

```
fdisk /dev/sdb
- n (새 파티션)
- p (primary partition)
- 1 (partition number)
- 엔터 (default)
- 엔터 (default)
- t (change partition's system id)
- l (list codes)
- 8e (LVM type)
- w (write &amp; exit)
```

이후 fdisk -l /dev/sdb 실행하면, System 부분이 Linux LVM으로 출력된다.
/dev/sdc에 대해서도 동일하게 작업 수행한다.

각 디스크에 대해 한 개의 파티션만 생성했으므로,
위의 작업이 완료되면 /dev/sdb1, /dev/sdc1이 생성된 것을 확인할 수 있다.

3. 물리볼륨 생성
위에서 생성한 LVM 파티션에 물리볼륨을 생성한다.

```
# 물리볼륨 생성
pvcreate /dev/sdb1
pvcreate /dev/sdc1

# 물리볼륨 조회
pvdisplay
```

4. 볼륨그룹 생성
앞서 생성한 물리볼륨이 하나로 동작하도록 설정하기 위해 그룹으로 묶어준다.

```
# vgcreate 볼륨그룹명 물리볼륨1 물리볼륨2 ...
vgcreate jsvg /dev/sdb1 /dev/sdc1

# 볼륨그룹 조회
vgdisplay
```

5. 볼륨그룹내 논리볼륨 생성
시나리오상, 두 개의 물리디스크를 하나의 논리디스크로 사용하겠다고 했으므로, 다음과 같이 설정한다.

```
# lvcreate -n 논리볼륨명 -l 100%FREE 볼륨그룹명
# -l 100%FREE : 전체 크기를 하나의 볼륨으로 쓰겠다
lvcreate -n lv -l 100%FREE jsvg
```

만약, 앞서 가정한 시나리오 대신 10GB와 나머지 크기의 두 논리 디스크로 나누겠다면,
다음과 같이 설정할 수 있다.

```
lvcreate -n lv1 -L 10G jsvg
lvcreate -n lv2 -l 100%FREE jsvg

# 논리볼륨 조회
lvdisplay
```

6. 파일시스템설정
앞서 생성한 논리볼륨을 포맷하고, 마운트해보자.

```
# 앞서 생성한 논리볼륨을 ext3으로 포맷
mkfs.ext3 /dev/jsvg/lv

# mount할 포인트 생성
mkdir /mnt/data

# mount
mount -t ext3 /dev/jsvg/lv /mnt/data
```

이제 /mnt/data로 디렉토리를 이동하여 살펴보자.
df -l 을 수행하여, 디렉토리의 용량이 의도한 대로 (두 디스크의 용량이 합쳐진 것으로) 설정되었는지 검토한다.
부팅시 해당 파일 시스템이 자동으로 마운트 되도록

vi /etc/fstab을 수정한다.

```
/dev/jsvg/lv /mnt/data ext3 defaults 0 0
```

와 같이 변경한다.
