---
layout: single
title: docker + postgresql
date: 2017-03-05 23:18:33.000000000 +09:00
categories: system
tags: [docker, postgres]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
테스트를 위해 간단하게 써보고자 할 때, 굳이 번거롭게 설치하지 말고 docker를 써보자.
다음의 명령어만으로도, docker 기반으로 postgres를 쓸 수 있다.


```
docker run -p 5432:5432 --name postgres -e POSTGRES_PASSWORD=postgres -d postgres
```

그런데, 띄우고 나서도 직접 container에 들어가서 psql도 직접 접속해보고, \dt 도 해보고 싶을 수도 있잖아?


그럴 때는, 직접 container에 다음과 같이 접속하자.


```
docker exec -it postgres bash
```

