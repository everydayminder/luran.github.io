---
layout: single
title: 'Unknown Entity Exception : @Entity 사용시'
date: 2013-07-10 04:20:16.000000000 +09:00
categories:
- development
tags: [java, hibernate]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
Hibernate를 사용하여, @Entity annotation을 사용하여 객체를 만들어 사용할 경우,
유의할 점이 있다.


@Entity를 import할 때,


```
org.hibernate.annotations.Entity
javax.persistence.Entity
```

의 두 가지 중에, 선택해야할 것은 javax.persistence.Entity이다.


위의 옵션을 선택한 경우, Unknown Entity Exception이 발생한다. Hibernate 4.1 이후 org.hibernate.annotations.Entity 는 deprecated 된다고 한다.


결론) javax.persistence.Entity를 import 하자.

