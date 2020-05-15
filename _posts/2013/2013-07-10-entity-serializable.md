---
layout: single
title: "@Entity 모델 객체는 Serializable을 꼭 구현해야 하나?"
date: 2013-07-10 10:25:48.000000000 +09:00
categories:
- development
tags: [hibernate, serializable]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
"JSR 220: Enterprise JavaBeansTM,Version 3.0 Java Persistence API Version 3.0, Final Release May 2, 2006"에 따르면,
다음과 같이 기술하고 있다.

"If an entity instance is to be passed by value as a detached object (e.g., through a remote interface), the entity class must implement the Serializable interface."

따라서, 이 객체를 어딘가로 전송하거나 세션에 기록하거나 등등 정말 serialization을 위한 용도가 아니라면,
Hibernate상에서는 굳이 Serailizable을 구현하지 않아도 된다는 뜻이지만,

사람들은 흔히 권하기를 그래도 웬만하면 Serializable을 구현하는 것이 좋은 습관일 것이라고 권한다.
실제 임의의 클래스를 작성하여, implements Serializable을 하거나, 하지 않더라도 정상적으로 DB에 persist되는 것을 확인할 수 있다.
