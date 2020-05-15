---
layout: single
title: DRBD, Pacemaker, Corosync 기반 PostgreSQL H/A 구성환경 설정
date: 2014-05-21 23:42:01.000000000 +09:00
categories:
- linux
tags: [drbd, pacemaker, corosync, postgresql]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
### 환경 구성
- VM 2개(node 설정용, 필수, VirtualBox/VMWare 사용) + 1개(테스트용, 선택)
- CentOS 6.4 (64bits)

### 노드별 사전 준비사항
- NIC : 2개 (VMWare/VirtualBox 관리 메뉴에서 추가)
- 별도 하드 파티션 (DRBD 전용) 설정 (VMWare/VirtualBox 관리 메뉴에서 추가)

### 주요 설치 순서
- 노드 네트워크 설정
- DRBD 전용 파티션 설정
- PostgreSQL 설치
- Pacemaker, Corosync, DRBD, Heartbeat 설치
- DRBD 환경설정
- postgreSQL + DRBD 동작확인
- corosync 환경설정
- corosync 동작 확인
- pacemaker 환경설정 (리소스 등록/관리)
- 실행 검사

### 설치/ 환경설정
#### 표기/참고
제목 뒤에 해당 노드를 표기하고, 모든 노드에서 동일하게 수행해야 하는 작업은 both로 표기한다.
초기 환경 설정/ 프로그램 설치까지 중복되는 작업일 수 있으므로, 한 vm에 어느 정도 설정을 한 후 clone하는 것이 더 편리할 수 있다.
본 설치 예제에서는, node1.mycluster, node2.mycluster라는 이름으로 노드를 구성하였다.

#### 설치 전 환경 설정 (both)
1) VMWare/VirtualBox 메뉴상 설정
- Network Interface를 각 노드별 1개씩 추가한다.
- Hard disk를 각 노드별 1개씩 추가한다. 이후, 부팅하여 fdisk -l로 확인하면, 추가된 디스크가 /dev/sdb 등과 같이 나타날 것이다.
새로 추가한 /dev/sdb를 향후 DRBD 전용으로 사용한다.

2) 네트워크 대역 구성
- LAN : 10.0.0.x (DB의 virtual IP도 이 대역으로 할당)
- Crossover : 172.16.0.x (두 노드 간의 통신 신뢰도 및 성능 향상을 위해 구성)

3) 노드 구성
전체 노드는 다음과 같이 구성하였다.
<a href="http://luran.me/wp-content/uploads/2014/05/postgresql-ha-configuration-new-page1.png"><img id="i-588" class="size-full wp-image" src="{{ site.baseurl }}/images/201405/postgresql-ha-configuration-new-page1.png" alt="Image" /></a>

* node1
- node1.mycluster : 10.0.0.191 (LAN), 172.16.0.1 (cross)
* node2
- node2.mycluster : 10.0.0.192 (LAN), 172.16.0.2 (cross)
* DB virtual IP (상황에 따라 active node로 설정 변경됨)
- dbip.mycluser : 10.0.0.190
* 실제 application이 접속하게 되는 IP address

#### 노드 기본 설정(특별한 언급이 없는 한, root 계정에서 실행)
1) SELINUX 설정 해제 (both)

```shell
getenforce
```
를 실행하여, SELINUX가 활성/해제 상태인지 확인한다. Enforcing으로 출력된다면, 활성화된 상태이므로,

```shell
vi /etc/selinux/config
SELINUX=disabled
```

라고 변경한다.

2) hostname 지정

```shell
vi /etc/sysconfig/network
```

[node1]
```shell
NETWORKING=yes
NETWORKING_IPV6=no
HOSTNAME=node1.mycluster
GATEWAY=10.0.0.9 (네트워크 구성상의 GW)
```

[node2]
```shell
NETWORKING=yes
NETWORKING_IPV6=no
HOSTNAME=node2.mycluster
GATEWAY=10.0.0.9 (네트워크 구성상의 GW)
```

3) network 설정 (node1, node2 각각)
```shell
vi /etc/sysconfig/network-scripts/ifcfg-eth0

DEVICE=eth0
BOOTPROTO=static
IPADDR=10.0.0.191 (node2의 경우 10.0.0.192)
NETMASK=255.255.255.0
ONBOOT=yes
HWADDR=시스템설정값사용(VMWare/VirtualBox)
```

```shell
vi /etc/sysconfig/network-scripts/ifcfg-eth1

DEVICE=eth1
BOOTPROTO=static
IPADDR=172.16.0.1 (node2의 경우 172.16.0.2)
NETMASK=255.255.255.0
ONBOOT=yes
HWADDR=시스템설정값사용(VMWare/VirtualBox)
```

```shell
vi /etc/hosts (both)

10.0.0.190 dbip.mycluster dbip
10.0.0.191 node1.mycluster node1
10.0.0.192 node2.mycluster node2
```

#### 프로그램 설치 (postgreSQL, pacemaker, corosync, DRBD)
1) 관련 패키지 설치 (both)

```shell
yum install gcc gcc-c++ make autoconf readline wget readline-devel zlib zlib-devel openssl openssl-devel gettext gettext-devel python python-devel
```

2) postgres 사용자 생성/설정 (both)
```shell
useradd -d /home/postgres postgres
passwd postgres
su - postgres
```

vi .bash_profile
아래 내용을 복사하고, 저장

```shell
PATH=$PATH:$HOME/bin
POSTGRES_HOME=/usr/local/pgsql
PGLIB=$POSTGRES_HOME/lib
PGDATA=/var/lib/pgsql/data
PATH=$POSTGRES_HOME/bin:$PATH

export PATH
export POSTGRES_HOME
export PGLIB
export PGDATA

LD_LIBRARY_PATH=$PGLIB
export LD_LIBRARY_PATH
```

3) postgresql 설치 (both)
postgresql 을 공식 사이트로부터 다운로드 하여,
/usr/local/src로 복사

```shell
tar -jxf postgresql-9.3.4.tar.bz2
cd /usr/local/src/postgresql-9.3.4
./configure --prefix=/usr/local/pgsql --enable-depend --enable-nls=ko --with-python
make
make install
```

4) pacemaker, corosync 설치 (both)
CentOS6.x에서는 pacemaker와 corosync 설치를 위해 별도의 yum repository를 추가할 필요가 없다. (이전 버전에서는 필요)

```shell
yum install pacemaker corosync
```

5) DRBD 설치
아래와 같이 ELREPO를 추가한 후,

```shell
rpm -Uvh http://mirror.web24.net.au/elrepo/elrepo/el6/x86_64/RPMS/elrepo-release-6-5.el6.elrepo.noarch.rpm
yum install drbd84-utils kmod-drbd84 heartbeat
```

또는
```shell
heartbeat-3.0.4-2.el6.x86_64.rpm
heartbeat-libs-3.0.4-2.el6.x86_64.rpm
kmod-drbd84-8.4.4-1.el6.elrepo.x86_64.rpm
drbd84-utils-8.4.4-2.el6.elrepo.x86_64.rpm
```

을 복사/직접 설치한다.

### DRBD 설정
1) DRBD 환경설정 (both)
```shell
vi /etc/drbd.conf
```

You can find an example in  /usr/share/doc/drbd.../drbd.conf.example

와 같은 문구가 보인다.
해당 디렉토리를 찾아가서 보면, drbd.conf.example이 존재하고, 다양한 설정 방법에 대한 예를 볼 수 있다.

```shell
include "drbd.d/global_common.conf";
include "drbd.d/*.res";
```

라는 문구가 보이는 것처럼,
drbd.d 디렉토리 내에, postgres용 환경설정 파일을 만들어놓고 include 시키면 된다.

```shell
/etc/drbd.d/postgres.res
resource postgres {
	startup {
		wfc-timeout 30;
		outdated-wfc-timeout 20;
		degr-wfc-timeout 30;
	}

	net {
		cram-hmac-alg sha1;
		shared-secret sync_disk;
	}

	syncer {
		rate 100M;
		verify-alg sha1;
	}

	on node1.mycluster {
		device      /dev/drbd0;
		disk        /dev/sdb;
		address     172.16.0.1:7791;
		meta-disk   internal;
	}

	on node2.mycluster {
		device      /dev/drbd0;
		disk        /dev/sdb;
		address     172.16.0.2:7791;
		meta-disk   internal;
	}
}
```

위 설정은 앞서 추가한 디스크인 /dev/sdb를 각각 논리 디바이스 /dev/drbd0으로 매핑하여 사용하도록 한다. DRBD는 7791 포트를 통해 통신하도록 설정하였으므로, 양 노드에서 방화벽 등록을 해준다.

```shell
vi /etc/sysconfig/iptalbes

-A INPUT -m state --state NEW -m tcp -p tcp --dport 7791 -j ACCEPT
```

2) meta data 생성 (both)
앞서 선언한 resource인 postgres라는 이름으로 meta data를 생성한다.

```shell
drbdadm create-md postgres
```

만약, postgres라는 이름의 리로스가 없다고 뜬다면, 호스트이름과 on 뒤에 선언한 node 이름들이 일치하는지 확인한다.
혹은

```shell
drbdadm create-md all
```
이라고 실행시키면, 선언된 resource들에 대해 메타 데이터를 생성하려 시도하는데, 문제가 되는 부분을 좀 더 자세하게 출력해 준다. (예: on 부분에 선언된 hostname이 없거나, 오타가 의심된다는 등)

즉, on 뒤에 기록한 host이름과

```shell
uname -n
```

으로 조회한 이름이 일치해야 한다.
정상적으로 실행된다면,

```shell
Writing meta data...
initializing activity log
NOT initializing bitmap
New drbd meta data block successfully created.
```

과 같은 메시지가 출력된다.

3) DRBD 서비스 시작  (both)

```shell
service drbd start
```

정상적으로 실행되면, 다음과 같은 메시지가 출력된다.

```shell
Starting DRBD resources: [
create res: postgres
prepare disk: postgres
adjust disk: postgres
adjust net: postgres
]
```

4) Disk Sync (node1)
두 노드를 강제로 동기화 시킨다.

```shell
drbdadm -- --overwrite-data-of-peer primary all
```

양 노드에서 아래와 같이 실행시켜보면, 동기화 진행상태를 확인할 수 있다.

[node1]
```shell
cat /proc/drbd
```

version: 8.4.4 (api:1/proto:86-101)
GIT-hash: 599f286440bd633d15d5ff985204aff4bccffadd build by phil@Build64R6, 2013-10-14 15:33:06
0: cs:SyncSource ro:Primary/Secondary ds:UpToDate/Inconsistent C r---n-
ns:44960 nr:0 dw:0 dr:48608 al:0 bm:2 lo:0 pe:7 ua:4 ap:0 ep:1 wo:f oos:8345308
[>....................] sync'ed:  0.6% (8148/8188)M

finish: 0:09:39 speed: 14,336 (14,336) K/sec


[node 2]
```shell
cat /proc/drbd
```

version: 8.4.4 (api:1/proto:86-101)
GIT-hash: 599f286440bd633d15d5ff985204aff4bccffadd build by phil@Build64R6, 2013-10-14 15:33:06
0: cs:SyncTarget ro:Secondary/Primary ds:Inconsistent/UpToDate C r-----
ns:0 nr:137472 dw:137448 dr:0 al:0 bm:8 lo:3 pe:5 ua:2 ap:0 ep:1 wo:f oos:8250868
[>....................] sync'ed:  1.7% (8056/8188)M

finish: 0:08:59 speed: 15,272 (15,272) want: 31,720 K/sec
동기화가 일단 100%가 될 때까지 기다린다.

5) DRBD 동기화 간단 테스트
[시나리오]
- node1에서 DRBD용 디렉토리 마운트
- 해당 디렉토리에 임의의 파일 생성
- node1을 primary -> secondary로 변경
- node2를 secondary -> primary로 변경
- DRBD 디렉토리를 마운트하여 확인

[node1]
```shell
mkfs.ext4 /dev/drbd0
mkdir /mnt/test
mount /dev/drbd0 /mnt/test
cd /mnt/test
touch a
cd ..
umount /mnt/test
drbdsetup /dev/drbd0 secondary
```

[node2]
```shell
mkdir /mnt/test
drbdsetup /dev/drbd0 primary
mount /dev/drbd0 /mnt/test
cd /mnt/test
ls -al
```

### PostgreSQL on DRBD 테스트
1) PostgreSQL 디렉토리 설정 (node1)
각 노드에서 모두 drbd를 시작시킨다.

```shell
service drbd start
```

node1이 primary로 정상적으로 동작하는지 확인한다.
```shell
cat /proc/drbd
```

version: 8.4.4 (api:1/proto:86-101)
GIT-hash: 599f286440bd633d15d5ff985204aff4bccffadd build by phil@Build64R6, 2013-10-14 15:33:06
0: cs:Connected ro:Primary/Secondary ds:UpToDate/UpToDate C r-----
ns:8388320 nr:0 dw:4 dr:8388997 al:1 bm:512 lo:0 pe:0 ua:0 ap:0 ep:1 wo:f oos:0
postgresql에서 사용할 디렉토리를 지정/설정한다.

```shell
mkdir /var/lib/pgsql
mount -t ext4 /dev/drbd0 /var/lib/pgsql
chwon postgres.postgres /var/lib/pgsql
```

2) PostgreSQL DB 초기화 (node1)
postgresql DB를 다음과 같이 초기화한다.
```shell
su - postgres
initdb /var/lib/pgsql/data
```

다음과 같은 메시지가 출력될 것이다.

The files belonging to this database system will be owned by user "postgres".
This user must also own the server process.

The database cluster will be initialized with locale "en_US.UTF-8".
The default database encoding has accordingly been set to "UTF8".
The default text search configuration will be set to "english".

Data page checksums are disabled.
creating directory /var/lib/pgsql/data ... ok
creating subdirectories ... ok
selecting default max_connections ... 100
selecting default shared_buffers ... 128MB
creating configuration files ... ok
creating template1 database in /var/lib/pgsql/data/base/1 ... ok
initializing pg_authid ... ok
initializing dependencies ... ok
creating system views ... ok
loading system objects' descriptions ... ok
creating collations ... ok
creating conversions ... ok
creating dictionaries ... ok
setting privileges on built-in objects ... ok
creating information schema ... ok
loading PL/pgSQL server-side language ... ok
vacuuming database template1 ... ok
copying template1 to template0 ... ok
copying template1 to postgres ... ok
syncing data to disk ... ok

WARNING: enabling "trust" authentication for local connections
You can change this by editing pg_hba.conf or using the option -A, or
--auth-local and --auth-host, the next time you run initdb.

Success. You can now start the database server using:
postgres -D /var/lib/pgsql/data
or
pg_ctl -D /var/lib/pgsql/data -l logfile start

3) hba(host based authentication) file 편집
node1, node2, db virtual ip을 추가한다.

```shell
vi /var/lib/pgsql/data/pg_hba.conf
host all all 10.0.0.190/32 trust
host all all 10.0.0.191/32 trust
host all all 10.0.0.192/32 trust
```

4) postgresql.conf 편집
```shell
vi /var/lib/pgsql/data/postgresql.conf

listen_addresses = '*'
```

로 변경하고, 주석을 해제한다.

```shell
port=5432
```
의 값을 원하는 포트로 변경하고,주석을 해제한다. (당연히 기본값을 그대로 사용해도 됨)

5) start script 작성 (both)
```shell
cp /usr/local/src/postgresql-9.3.4/contrib/start-scripts/linux /etc/init.d/postgresql
chmod 775 /etc/init.d/postgresql
```
postgresql 파일의 내용 중, 디렉토리 등의 설정을 자신의 설치환경에 맞게 변경한다. (예: PGDATA, PGUSER 등)

6) 계정 생성/샘플 데이터 준비 (node1)
suser라는 super user 계정을 생성하고, test 데이터베이스를 생성한다.

```shell
su - postgres
createuser --superuser suser --pwprompt
psql -U suser -d postgres
postgres=# create database test;
postgres=# \q
psql -U suser -d test
test=# create table department (
id int primary key not null,
dept char(50) not null,
emp_id int not null
);

test=# insert into department(id, dept, emp_id) values(1, 'sales', 100);
test=# select * from department;

id |                        dept                        | emp_id

----+----------------------------------------------------+--------

1 | sales                                              |    100


test=# \q
```

7) PostgreSQL on DRBD 테스트
[node1]
postgreSQL을 내린다.

```shell
service postgresql stop
```

DRBD 디바이스를 언마운트시키고, 현재 노드를 primary -> secondary로 전환시킨다.

```shell
umount /dev/drbd0
drbdadm secondary postgres
```

[node2]
위 작업의 역순으로 실행하여, node2에서 postgreSQL이 DRBD 기반으로 동작하는지 확인한다.

```shell
drbdadm primary postgres
```

위 명령어를 수행하기 전에는

```shell
cat /proc/drbd
```

version: 8.4.4 (api:1/proto:86-101)
GIT-hash: 599f286440bd633d15d5ff985204aff4bccffadd build by phil@Build64R6, 2013-10-14 15:33:06
0: cs:Connected ro:Secondary/Secondary ds:UpToDate/UpToDate C r-----
ns:0 nr:8437416 dw:8437416 dr:0 al:0 bm:512 lo:0 pe:0 ua:0 ap:0 ep:1 wo:f oos:0

수행 후에는

```shell
cat /proc/drbd
```

version: 8.4.4 (api:1/proto:86-101)
GIT-hash: 599f286440bd633d15d5ff985204aff4bccffadd build by phil@Build64R6, 2013-10-14 15:33:06
0: cs:Connected ro:Primary/Secondary ds:UpToDate/UpToDate C r-----
ns:0 nr:8437416 dw:8437416 dr:664 al:0 bm:512 lo:0 pe:0 ua:0 ap:0 ep:1 wo:f oos:0

이제, DRBD device를 마운트하고 postgresql을 시작시킨다.

```shell
mkdir /var/lib/pgsql
mount -t ext4 /dev/drbd0 /var/lib/pgsql
service postgresql start
```
node1에서 작업한 내용을 확인하자.

```shell
su - postgres
psql -U suser -d test
test=# select * from department;

id |                        dept                        | emp_id

----+----------------------------------------------------+--------

1 | sales                                              |    100
```

8) 테스트 환경 원복
앞서 테스트하느라 변경한 상태를 corosync 설정을 하기에 앞서 원래대로 돌려 놓자.
마지막에 테스트를 node2에서 수행했으므로, node2부터 변경한다.

[node2]
```shell
service postgresql stop
umount /dev/drbd0
drbdadm secondary postgres
```

[node1]
```shell
drbdadm primary postgres
```

[both]
DRBD나 postgreSQL은 이후, Pacemaker를 통해 시작/종료 등 관리할 것이므로, 자동 시작되지 않도록 설정을 변경한다.

```shell
service drbd stop
chkconfig --level 35 drbd off
chkconfig --level 35 postgresql off
```

### Corosync 환경설정
1) 기본 설정 (both)
```shell
cp /etc/corosync/corosync.conf/example /etc/corosync/corosync.conf
vi /etc/corosync/corosync.conf
compatibility: whitetank

totem {
	version: 2
	secauth: off
	threads: 0
	interface {
		ringnumber: 0
		bindnetaddr: 172.16.0.0
		mcastaddr: 226.94.1.1
		mcastport: 5405
		ttl: 1
	}
}


logging {
	fileline: off
	to_stderr: no
	to_logfile: yes
	to_syslog: yes
	logfile: /var/log/cluster/corosync.log
	debug: off
	timestamp: on
	logger_subsys {
		subsys: AMF
		debug: off
	}
}


amf {
	mode: disabled
}


aisexec {
	user: root
	group: root
}

service {
	# Load the Pacemaker Cluster Resource Manager
	name: pacemaker
	ver: 0
}
```

매뉴얼에 따르면, corosync가 사용하는 multicast 포트 설정은 설정값 - 1을 사용하여, send/receive에 사용한다고 한다. 즉, 5405 포트를 선언하면, 5404 포트도 필요하다.
방화벽 룰에 추가한다.

```shell
vi /etc/sysconfig/iptables
-A INPUT -m state --state NEW -m udp -p udp --dport 5404 -j ACCEPT
-A INPUT -m state --state NEW -m udp -p udp --dport 5405 -j ACCEPT
```

변경 사항을 저장하고,
```shell
mkdir /var/log/cluster
serivce iptables restart
service corosync start
```

모든 노드로부터

```shell
crm_mon -1
```

을 실행했을 때,
```
Last updated: Thu May 15 22:43:41 2014
Last change: Thu May 15 22:41:25 2014 via crmd on node1.mycluster
Stack: classic openais (with plugin)
Current DC: node1.mycluster - partition with quorum
Version: 1.1.10-14.el6-368c726
2 Nodes configured, 2 expected votes
0 Resources configured

Online: [ node1.mycluster node2.mycluster ]
```
등과 같이 조회되어야 한다.

양 노드에 corosync를 서비스로 항상 실행되도록 설정한다.

```shell
chkconfig --level 35 corosync on
```

### Pacemaker 설정
1) crmsh 설치
예전 버전의 pacemaker, corosync 설치를 했다면 crmsh이 기본적으로 설치되어 있을 것이다.
최신 버전의 패키지들은 기본적으로 pcs 를 활용하여, 설치를 가이드하고 있다.

그러나, 아직 레퍼런스가 crm을 사용하는 경우가 많으므로, crm을 설치해서 진행하도록 한다. crm을 써서 pacemaker 리소스를 관리하려면, 별도로 crmsh를 찾아서 설치해 줘야 한다.

```shell
wget -P/etc/yum.repos.d/ http://download.opensuse.org/repositories/network:/ha-clustering:/Stable/CentOS_CentOS-6/network:ha-clustering:Stable.repo
yum install crmsh.x86_64
```

와 같이 하거나, 해당 리파지토리로부터
```shell
pssh-2.3.1-3.3.x86_64.rpm
crmsh-2.0+git46-1.1.x86_64.rpm
```

을 받아서 직접 설치해 줘야 한다.

2) 클러스터 일반 설정 (node1)

```shell
crm configure property stonith-enabled=false
crm configure property no-quorum-policy=ignore
crm configure rsc_defaults resource-stickiness=100
```

3) 클러스터 리소스 설정 (node1)
클러스터에 등록하는 FileSystem(DRBD), DBIP, Postgres 등등 모두가 클러스터의 리소스로 인식된다. 이들을 모두 등록한다.

[DBIP]
```shell
crm configure primitive DBIP ocf:heartbeat:IPaddr2 params ip=10.0.0.190 cidr_netmask=24 op monitor interval=30s
```

crm_mon -1

로 등록 상태를 확인하면, 아래와 같이 등록된 리소스를 확인할 수 있다.

```
Last updated: Fri May 16 01:16:13 2014
Last change: Fri May 16 01:14:57 2014 via cibadmin on pstg04
Stack: classic openais (with plugin)
Current DC: node1.mycluster - partition with quorum
Version: 1.1.10-14.el6-368c726
2 Nodes configured, 2 expected votes
1 Resources configured

Online: [ node1.mycluster node2.mycluster ]


DBIP   (ocf::heartbeat:IPaddr2):       Started node1.mycluster

[DRBD on cluster] (node1)
```
이어서, DRBD 설정도 순서대로 진행한다.

```shell
crm configure primitive drbd_postgres ocf:linbit:drbd params drbd_resource="postgres" op monitor interval="15s"
crm configure ms ms_drbd_postgres drbd_postgres meta master-max="1" master-node-max="1" clone-max="2" clone-node-max="1" notify="true"
crm configure primitive postgres_fs ocf:heartbeat:Filesystem params device="/dev/drbd0" directory="/var/lib/pgsql" fstype="ext4"
```

[PostgreSQL on cluster] (node1)
PostgreSQL도 리소스로 등록해준다.

```shell
crm configure primitive postgresql ocf:heartbeat:pgsql op monitor depth="0" timeout="30" interval="30"
```

[Resource Grouping]
PostgreSQL과 관련하여 등록한 리소스들을 그룹으로 묶어주고, 순서를 부여한다.

```shell
crm configure group postgres postgres_fs DBIP postgresql
crm configure colocation postgres_on_drbd inf: postgres ms_drbd_postgres:Master
crm configure order postgres_after_drbd inf: ms_drbd_postgres:promote postgres:start
crm configure location master-prefer-node1 DBIP 50: node1.mycluster
```

[Pacemaker 추가 설정]
이 상태에서 곧바로 crm_mon -1 을 수행하면, 설정 과정 중 발생했던 failed actions 메시지들이 그대로 남게 된다.

```shell
crm resource cleanup resource명
```

명령어를 수행하여 해당 fail message를 지워 줄 수 있다.
일단 reboot 하고, 정상적으로 동작하는지 확인한다.
그리고 나서, 정상적으로 동작하면 다행인데, 위의 설정을 그대로 따랐다면, 보통 pacemaker에서 에러가 발생한다.

```shell
vi /usr/lib/ocf/resource.d/heartbeat/pgsql
```
파일을 열어 보자.

```shell
OCF_RESKEY_pgctl_default=/usr/bin/pg_ctl
OCF_RESKEY_psql_default=/usr/bin/psql
```
결국, postgreSQL을 설치한 위치가 달라서 pacemaker가 postgreSQL을 시작/중지 등을 시킬 수 없다는 에러가 발생할 것이다.
위 옵션의 경로에 지정된 디렉토리를 실제 설치된 곳으로 변경해주거나, 해당 디렉토리에 symbolic link를 생성해 주자.

crm_mon -1

을 수행하여, 해당 노드/ 리소스가 정상적으로 동작하는지 확인한다.

상태는 아래와 같이
```
Last updated: Fri May 16 03:27:27 2014
Last change: Fri May 16 01:31:08 2014 via cibadmin on node1.mycluster
Stack: classic openais (with plugin)
Current DC: node1.mycluster - partition with quorum
Version: 1.1.10-14.el6-368c726
2 Nodes configured, 2 expected votes
5 Resources configured

Online: [ node1.mycluster node2.mycluster ]

Master/Slave Set: ms_drbd_postgres [drbd_postgres]
Masters: [ node1.mycluster ]
Slaves: [ node2.mycluster  ]
Resource Group: postgres

postgres_fs        (ocf::heartbeat:Filesystem):    Started node1.mycluster
DBIP       (ocf::heartbeat:IPaddr2):       Started node1.mycluster

postgresql (ocf::heartbeat:pgsql): Started node1.mycluster
```
정상적으로 보이지만, 사실은 DRBD가 정상 상태가 아닐 수도 있다.

```shell
cat /proc/drbd
```

를 통해 두 노드가 정상적으로 동기화 되고 있는지 반드시 확인한다.
상태가 Primary/Secondary 혹은 Secondary/Primary로 출력되는지 확인한다. 만약, Unknown 메시지가 보인다면, DRBD가 동기화 되고 있지 않다는 뜻이다.

4) 동작 확인
일단 동작하는 노드를 확인하려면 다음과 같이 수행한다.

```shell
crm_mon -1
```

VirtualIP로 동작하는 것을 확인하고, Remote로 직접 접속하여, 앞서 수행한 것과 동일한 테스트를 해보자.

```shell
psql -h 10.0.0.190 -U suser -d test

test=# select * from test; (node1에서 실행됨)

id |                        dept                        | emp_id

----+----------------------------------------------------+--------

1 | sales                                              |    100
```

이 상태에서 primary를 내리면, secondary 노드가 대신 동작하는지 확인한다.
primary로부터 (node1)

```shell
crm node standby
```

앞서 맺어놓은 세션은 일단 끊길 것이나,

```shell
test=# select * from test;

FATAL: terminating connection due to administrator command
FATAL: terminating connection due to administrator command

The connection to the server was lost. Attempting reset: Succeeded.
```

세션을 다시 맺고 실행하면 정상 동작하는 것을 확인할 수 있다.

```shell
test=# select * from test; (node2에서 실행됨)

id |                        dept                        | emp_id

----+----------------------------------------------------+--------

1 | sales                                              |    100
```

결과는 동일하지만, 접속한 노드는 node1 → node2로 변경되었다.

5) Spit Braing (DRBD 동기화 깨지는 경우)
DRBD의 동기화 상태가 깨진 상태를 Split Brain이라 한다. 보통 데이터 확인 후 수동으로 복구하는 것을 권장하나, 자동 복구를 위한 설정도 가능하다. DRBD에서는 다음과 같은 옵션이 설정 가능하다.

- discarding modifications made on the younger primary
- discarding modifications made on the older primary
- discarding modifications on the primary with fewer changes
- graceful recovery from split brain if one host has had no intermediate changes

수동으로 작업하는 경우, 보통 아래의 작업을 수행하면 된다.

```shell
drbdadm -- --discard-my-data connect 리소스명
drbdadm disconnect 리소스명
drbdadm connect 리소스명
```

의 작업을 원하는 노드에서 선별적으로 수행하면, 보통 DRBD 세션은 붙게 된다.
만약, 위의 명령어로도 해결이 안 된다면,

```shell
drbdadm invalidate 리소스명
```

을 수행한 후, 재접속 시도를 시키면 될 것이다.
그러나, 실제 운영환경에서는 위와 같은 명령어를 수행하기에 앞서, 데이터 손실 여부/ 어떤 노드를 기준으로 삼고 동기화를 진행시킬지 적절한 의사 판단이 필요할 것이다.

6) Corosync 채널 이중화 설정
```shell
vi /etc/corosync/corosync.conf
```

rrp_mode : active로 선언하고,
interface를 추가로 선언하고,
해당 port 및 port -1 에 대해 방화벽 룰을 추가해 준다.


```shell
compatibility: whitetank
totem {
	version: 2
	secauth: off
	threads: 0
	rrp_mode: active
	interface {
		ringnumber: 0
		bindnetaddr: 172.16.0.0
		mcastaddr: 226.94.1.1
		mcastport: 5405
		ttl: 1
	}

	interface {
		ringnumber: 1
		bindnetaddr: 10.0.0.0
		mcastaddr: 227.94.1.1
		mcastport: 5407
		ttl: 1
	}
}

logging {
	fileline: off
	to_stderr: no
	to_logfile: yes
	to_syslog: yes
	logfile: /var/log/cluster/corosync.log
	debug: off
	timestamp: on
	logger_subsys {
		subsys: AMF
		debug: off
	}
}

amf {
	mode: disabled
}

aisexec {
	user: root
	group: root
}

service {
	# Load the Pacemaker Cluster Resource Manager
	name: pacemaker
	ver: 0
}
```

7) DRBD의 채널 이중화 가능성
확인한 바로는 DRBD의 통신 채널을 이중화할 수 있는 옵션은 없는 듯 하다. 결국, Corosync 등의 통신 채널을 이중화 하더라도, DRBD 채널에 문제가 생긴다면(split brain) 앞서 설명한 바와 같이 DRBD 동기화는 자동/수동으로 별도로 맞춰야 할 것이다.
