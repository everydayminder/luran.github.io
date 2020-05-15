---
layout: single
title: c3p0 timeout과 idle_test_period의 설정
date: 2013-10-23 04:55:31.000000000 +09:00
categories:
- development
tags: [hibernate, connnection]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
hibernate를 사용하는 도중, DB connection이 끊긴다면?
DB connection이 계속 유지되도록 하려면, 가장 간단한 방법은 주기적으로 사용해야 할 것이다.
dummy query를 실행시켜서 connection 자체를 유지시키면 되는데, connection pool library인 c3p0에서는 이를 옵션으로 설정할 수 있도록 가이드한다.

hibernate에서는 이 속성들을 그대로 선언하여 connection을 유지시킬 수 있다.
바로 c3p0.timeout과 c3p0.idle_test_period 값이다.

jboss에서는 다음과 같이 안내한다.

<blockquote>
idleTestPeriod Must be set in hibernate.cfg.xml (or hibernate.properties), Hibernate default: 0
If this is a number greater than 0, c3p0 will test all idle, pooled but unchecked-out connections, every this number of seconds.
timeout Must be set in hibernate.cfg.xml (or hibernate.properties), Hibernate default: 0
The seconds a Connection can remain pooled but unused before being discarded. Zero means idle connections never expire
</blockquote>

사실, 개발자들이 주고 받은 Q&amp;A가 더 유용한데, StackOverFlow에 다음과 같은 내용들이 공유되어 있었다.

<blockquote>

The database server may close a connection on its side after a certain amount of time - causing some error in your application, because it'll attempt to send a query on a connection which is no longer available on the server side.

In order to avoid this you can let the pool periodically check a connection (Think of a ping) for it's validity. This is what idle_test_period is for.
timeout is the timespan after which the pool will remove a connection from the pool, because the connection wasn't checked out (used) for a while and the pool contains more connections than c3pO.min_size.

<a href="http://stackoverflow.com/questions/10175957/the-use-of-c3p0-idle-test-period">http://stackoverflow.com/questions/10175957/the-use-of-c3p0-idle-test-period</a>

</blockquote>

사실 좀더 끌리는 설명은 다음과 같다.

<blockquote>
Actually this is probably too late, but the problem is quite simple: hibernate.c3p0.idle_test_periods must not be higher than hibernate.c3p0.timeout or connections closed by the database will not be properly detected.

Moreover, the deadlock detection warnings look like some part of your code is not properly returning the connections to the pool (i.e. session.close())
The MysqlIO exceptions occur when your application idles and MySQL closes the connection on the server. Now if C3P0 does not properly check whether a connection is still actually connected you get the EOFExceptions.

I hope this might be helpful.

<a href="http://stackoverflow.com/questions/475893/what-are-the-required-c3p0-settings-for-hibernate-in-order-to-avoid-deadlocks">http://stackoverflow.com/questions/475893/what-are-the-required-c3p0-settings-for-hibernate-in-order-to-avoid-deadlocks</a>
</blockquote>

"끊기기 전에 주기적으로 connection 유지 여부를 확인할 것."

