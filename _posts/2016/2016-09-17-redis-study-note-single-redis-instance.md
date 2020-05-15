layout: single
title: Redis Study Note - single 인스턴스 설치
date: 2016-09-17 14:53:32.000000000 +09:00
categories:
- system
tags: [redis]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
Redis single instance를 다음과 같이 설치해 본다.

* OS : CentOS 6.8 / VirtualBox
* Redis 버전 : 3.2.3

<strong>공식사이트</strong>
Redis 공식 사이트 : http://redis.io

<strong>다운로드</strong>
2016/9/16현재 최신 stable버전인 3.2를 다운로드 한다.

<strong>설치</strong>
공식 사이트에 안내된 바와 같이, 단일 인스턴스 설치 과정 자체는 무척 간단하다. 

http://download.redis.io/releases/redis-3.2.3.tar.gz

에서 다운로드하고,

```
$ tar xzf redis-3.2.3.tar.gz
$ cd redis-3.2.3
$ make
```

이대로 설치를 진행하면, gcc부터 시작하여 여러 라이브러리가 없다고 줄줄이 에러가 발생한다.
README.md파일을 읽어보면, 의존성 라이브러리들이 deps 디렉토리에 존재한다는 것을 알 수 있다.
일단 다음과 같이 실행한다. (추후 다른 라이브러리들도 필요할 것 같아, 일단 epel-release도 함께 설치하였다.)

```
$ yum install epel-release
$ yum install gcc
```

설치시 의존성 문제가 발생하면, 아래와 같이 초기화하고 다시 설치해 본다.

```
$ make distclean
```

정상적으로 설치를 마쳤다면, 다음과 같은 메시지가 눈에 띌 것이다.
```
Hint: It's a good idea to run 'make test' ;)
```
테스트를 실행해보면, 이번에는 tcl을 설치하라고 한다.
```
$ yum install tcl
```
tcl을 설치 후, 이제 make test를 실행하면
```
$ make test
```
수많은 테스트를 알아서 수행한다.

```
Execution time of different units:
  1 seconds - unit/type/incr
  1 seconds - unit/printver
  2 seconds - unit/auth
  2 seconds - unit/keyspace
  0 seconds - unit/quit
  3 seconds - unit/scan
  2 seconds - unit/multi
  7 seconds - unit/protocol
  11 seconds - unit/expire
  23 seconds - unit/type/list
  11 seconds - integration/aof
  4 seconds - integration/rdb
  3 seconds - integration/convert-zipmap-hash-on-load
  2 seconds - integration/logging
  1 seconds - unit/pubsub
  2 seconds - unit/slowlog
  46 seconds - unit/aofrw
  51 seconds - unit/other
  1 seconds - unit/introspection
  57 seconds - unit/type/hash
  2 seconds - unit/limits
  58 seconds - integration/replication
  7 seconds - unit/introspection-2
  19 seconds - unit/scripting
  10 seconds - unit/bitfield
  72 seconds - unit/type/string
  81 seconds - unit/type/set
  38 seconds - unit/bitops
  96 seconds - integration/replication-2
  102 seconds - unit/type/zset
  63 seconds - unit/maxmemory
  117 seconds - unit/dump
  127 seconds - unit/sort
  58 seconds - unit/memefficiency
  121 seconds - integration/replication-psync
  86 seconds - unit/geo
  156 seconds - unit/type/list-2
  159 seconds - unit/type/list-3
  155 seconds - integration/replication-4
  160 seconds - integration/replication-3
  92 seconds - unit/hyperloglog
  114 seconds - unit/obuf-limits

\o/ All tests passed without errors!

Cleanup: may take some time... OK
```

make까지 성공했으면 src/redis-server로 실행가능한 상태가 되었을 것이다.
아래와 같이 실행하면, /usr/local/bin에 redis-server, cli 파일 등이 복사된다.

```
$ make install
```

환경설정 파일 등의 절차를 쉽게 하도록, utils/install_server.sh를 실행한다.
포트, 로그 위치, config에 관련된 정보/ 위치에 대해 물으나, 기본 설정으로 모두 그대로 enter를 쳤고,
executable의 위치는 다른 가이드에 나온대로 기본 인식이 되지 않아, /usr/local/bin/redis-server를 직접 입력하였다.

redis 기본 포트가 6379라, /etc/init.d/redis_6379 파일이 생성되었다.
아래의 명령어로 서비스를 시작/ 정지시킬 수 있다.

```
$ service redis_6379 start
$ service redis_6379 stop
```

redis가 정상적으로 떠 있는 상태라면,
```
$ ps -ef | grep redis

root     22694     1  0 00:25 ?        00:00:05 /usr/local/bin/redis-server 127.0.0.1:6379
```
이제, redis client로 접속하면,
```
$ /usr/local/bin/redis-cli
127.0.0.1:6379&gt;
```
와 같이 접속 상태가 안내되고, 다음과 같이 기본 동작 상태를 확인할 수 있다.

```
27.0.0.1:6379&gt; set test 1234
OK

127.0.0.1:6379&gt; set name &quot;abc def&quot;
OK

127.0.0.1:6379&gt; exists name
(integer) 1

127.0.0.1:6379&gt; get name
&quot;abc def&quot;

127.0.0.1:6379&gt; get test
&quot;1234&quot;
```

