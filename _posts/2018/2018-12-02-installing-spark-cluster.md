---
layout: single
title: Docker 기반 Local Spark Cluster 설치하기 
date: 2018-12-02 23:35:00.000000000 +09:00
categories:
- development
tags: [spark, docker]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
Baremetal 서버나 cloud, VM 등에 설치하기에 앞서, docker기반으로 spark cluster 환경을 빨리 쉽게 구축하고 사용할 수 있다. 어떻게 동작하는지 살펴보거나, 간단한 프로젝트를 하고자 한다면 나쁘지 않은 선택이 될 것이다.

# Spark Cluster Project 받기
인터넷에서 다음의 프로젝트를 받아서 설치한다.
```shell
git clone https://github.com/big-data-europe/docker-hadoop-spark-workbench.git
```

# Spark Cluster 시작하기
위 프로젝트를 clone한 디렉토리에서, 다음과 같이 실행하면 프로젝트내 기술된 docker-compose.yml을 바탕으로 spark cluster가 기동된다. (OSX 기준, docker-for-mac 등이 설치되어 있어야 함)

```shell
docker-compose up
```

모든 이미지들을 최초 다운로드 받는데 다소 시간이 걸리지만,
이후에는 곧바로 실행된다.

# 실행중인 프로세스 확인
위와 같이 클러스터를 시작하면, 원본 github 페이지에 안내된 바와 같이 아래 프로세스들이 동작하는 것을 확인할 수 있다.

```shell
docker ps
```
실행하면, 아래와 같이 동작 중인 container들이 나타난다.
```shell
CONTAINER ID        IMAGE                                             COMMAND                  CREATED             STATUS                    PORTS                                                      NAMES
bfbe4579b5e8        bde2020/spark-worker:2.1.0-hadoop2.8-hive-java8   "entrypoint.sh /bin/…"   36 minutes ago      Up 36 minutes (healthy)   0.0.0.0:8081->8081/tcp                                     docker-hadoop-spark-workbench_spark-worker_1
47e1225e695c        bde2020/hadoop-datanode:1.1.0-hadoop2.8-java8     "/entrypoint.sh /run…"   36 minutes ago      Up 36 minutes (healthy)   0.0.0.0:50075->50075/tcp                                   docker-hadoop-spark-workbench_datanode_1
00effc501b21        bde2020/spark-master:2.1.0-hadoop2.8-hive-java8   "entrypoint.sh /bin/…"   36 minutes ago      Up 36 minutes (healthy)   0.0.0.0:7077->7077/tcp, 6066/tcp, 0.0.0.0:8080->8080/tcp   spark-master
2ad2864b8ef2        bde2020/hadoop-namenode:1.1.0-hadoop2.8-java8     "/entrypoint.sh /run…"   36 minutes ago      Up 36 minutes (healthy)   0.0.0.0:50070->50070/tcp                                   namenode
19f6e280b334        bde2020/spark-notebook:2.1.0-hadoop2.8-hive       "/entrypoint.sh /run…"   36 minutes ago      Up 36 minutes             0.0.0.0:9001->9001/tcp                                     spark-notebook
5284894c2597        bde2020/hdfs-filebrowser:3.11                     "/entrypoint.sh buil…"   36 minutes ago      Up 36 minutes             0.0.0.0:8088->8088/tcp                                     docker-hadoop-spark-workbench_hue_1
```

이는 프로젝트 github에 안내된 바와 같이 

Namenode: http://localhost:50070
Datanode: http://localhost:50075
Spark-master: http://localhost:8080
Spark-notebook: http://localhost:9001
Hue (HDFS Filebrowser): http://localhost:8088/home

의 내용을 포함하고 있다.
즉, 브라우저에서 위의 주소를 입력하면 동작여부를 가시적으로 확인할 수 있다.


# Spark Application 실행하기
다음과 같이 spark-submit 명령어로 애플리케이션을 실행할 수 있다.

```shell
#!/bin/bash

spark-submit \
    --deploy-mode client \
    --num-executors 10 \
    --driver-memory 40g \
    --driver-cores 8 \
    --executor-memory 80g \
    --executor-cores 10 \
    --conf spark.yarn.executor.memoryOverhead=4096 \
    --jars {참조할 jar 경로 포함한 이름} \
    --class {패키지명 포함한 실행 대상 클래스 이름} \
      {클래스 파일이 들어있는 jar 파일이름}
```

# Spark Worker Node Scale Out
혹시, 이미 동작중인 클러스터내 worker의 수를 더 늘리고 싶다면? 다음의 명령어로 worker의 수를 늘릴 수 있다.

```shell
docker-compose scale spark-worker=3
```

단, 위와 같이 프로젝트 파일을 클론한 기본 상태에서는, scale out 시도시 port 매핑 충돌이 발생하여 다음과 같은 에러가 발생한다.
```text
WARNING: The scale command is deprecated. Use the up command with the --scale flag instead.
WARNING: The "spark-worker" service specifies a port on the host. If multiple containers for this service are created on a single host, the port will clash.
Starting docker-hadoop-spark-workbench_spark-worker_1 ... done
Creating docker-hadoop-spark-workbench_spark-worker_2 ... error
Creating docker-hadoop-spark-workbench_spark-worker_3 ... error

ERROR: for docker-hadoop-spark-workbench_spark-worker_3  Cannot start service spark-worker: b'driver failed programming external connectivity on endpoint docker-hadoop-spark-workbench_spark-worker_3 (e6579099d4cff6a3d7986dae5ebdd0ab594584d2ed2e2bd3974174016ffe9d57): Bind for 0.0.0.0:8081 failed: port is already allocated'

ERROR: for docker-hadoop-spark-workbench_spark-worker_2  Cannot start service spark-worker: b'driver failed programming external connectivity on endpoint docker-hadoop-spark-workbench_spark-worker_2 (8bc751510f81453fb3ec2225d6ee0f4e1900569412644b64eafdec213a80e995): Bind for 0.0.0.0:8081 failed: port is already allocated'
ERROR: Cannot start service spark-worker: b'driver failed programming external connectivity on endpoint docker-hadoop-spark-workbench_spark-worker_3 (e6579099d4cff6a3d7986dae5ebdd0ab594584d2ed2e2bd3974174016ffe9d57): Bind for 0.0.0.0:8081 failed: port is already allocated'
```
따라서, docker-compose.yml 파일 중, worker의 포트 매핑 정보를 주석처리 또는 삭제하고 다시 실행한다.

```xml
  spark-worker:
    image: bde2020/spark-worker:2.1.0-hadoop2.8-hive-java8
    depends_on:
      - spark-master
    environment:
      - SPARK_MASTER=spark://spark-master:7077
#    ports:
#      - 8081:8081
    env_file:
      - ./hadoop.env
```

위의 커맨드를 실행하면,
```shell
Starting docker-hadoop-spark-workbench_spark-worker_1 ... done
Creating docker-hadoop-spark-workbench_spark-worker_2 ... done
Creating docker-hadoop-spark-workbench_spark-worker_3 ... done
```
와 같이 메시지가 뜨고, docker container 리스트에도 반영된다.

```shell
CONTAINER ID        IMAGE                                             COMMAND                  CREATED             STATUS                    PORTS                                                      NAMES
dcc35f0449ce        bde2020/spark-worker:2.1.0-hadoop2.8-hive-java8   "entrypoint.sh /bin/…"   47 seconds ago      Up 46 seconds (healthy)   8081/tcp                                                   docker-hadoop-spark-workbench_spark-worker_2
3915f1e48222        bde2020/spark-worker:2.1.0-hadoop2.8-hive-java8   "entrypoint.sh /bin/…"   47 seconds ago      Up 46 seconds (healthy)   8081/tcp                                                   docker-hadoop-spark-workbench_spark-worker_3
bfbe4579b5e8        bde2020/spark-worker:2.1.0-hadoop2.8-hive-java8   "entrypoint.sh /bin/…"   3 hours ago         Up 3 hours (healthy)      0.0.0.0:8081->8081/tcp                                     docker-hadoop-spark-workbench_spark-worker_1
47e1225e695c        bde2020/hadoop-datanode:1.1.0-hadoop2.8-java8     "/entrypoint.sh /run…"   3 hours
```

앞서 표기한, master 노드의 웹에 들어가서 확인해도 이 내용이 반영되어 있음을 확인 가능하다.
![spark-workernode3.png]({{site.baseurl}}/images/201812/spark-workernode3.png)


# Spark Cluster 종료하기
실행 중인 클러스터는 다음과 같이 종료한다.

```shell
docker-compose down
```



