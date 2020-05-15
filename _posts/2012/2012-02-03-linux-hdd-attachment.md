---
layout: single
title: linux HDD 추가
date: 2012-02-03 15:19:00.000000000 +09:00
categories:
- system
tags: [fdisk, fstab, hdd, linux]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
마이크로서버를 사고, HDD 2TB를 새로 추가하였다.
다음과 같이 추가 하드를 장착하였다.

<b>1. 시스템 끈 상태에서 HDD 추가 장착</b>
<b>2. 파티션 추가</b>
```
fdisk /dev/sdb
```

* p : 파티션 정보 조회
* n : 파티션 생성

데이터용과, 백업용으로 나누려고 2개로 나누었다.
그래서, /dev/sdb1, /dev/sdb2가 생성되었다.
잘 나뉘었는지, fdisk -l /dev/sdb로 조회하자.

<b>3. ext3으로 포맷</b>
```
mkfs -t ext3 /dev/sdb1
mkfs -t ext3 /dev/sdb2&nbsp;
```

<b>4. 마운트 포인트 생성</b>
```
mkdir -p /mnt/data
mkdir -p /mnt/backup&nbsp;
```

<b>5. 부팅시 자동으로 인식되도록 설정</b>
```
mount /dev/sdb1 /mnt/data
mount /dev/sdb2 /mnt/backup&nbsp;
```
이라고 하면, 인식이 될 것이나 매번 실행하기는 힘들 것이다.


/etc/fstab에 등록하자.<b>&nbsp;</b>
```
/dev/sdb1 &nbsp;/data &nbsp;ext3 defaults 1 2
/dev/sdb2 &nbsp;/backup ext3 defaults 1 2&nbsp;
```

를 추가하자.

