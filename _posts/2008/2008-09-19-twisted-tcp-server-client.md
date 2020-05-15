---
layout: single
title: Twisted를 사용하여 TCP Server와 Client를 한번에
date: 2008-09-19 02:28:44.000000000 +09:00
categories:
- development
tags: [twisted, python]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
Twisted에서 제공하는 TAC를 사용하면, 여러개의 서비스를 동시에 묶어 application service로 등록 사용할 수 있다.
앞서 만들었던 TestClient에 추가적으로 Server의 요소를 넣고자 한다.
예를 들면, Administrator의 목적으로.

이를 위해, 몇몇 패키지를 import 한다.

```python
from twisted.internet.protocol import ServerFactory
from twisted.application import internet, service>
```

추가적으로 서버의 프로토콜과 팩토리를 정의한다.

```python
class AdminProtocol(LineReceiver):
    delimiter = 'n';

    def lineReceived(self, line):
        print line

    def connectionMade(self):
        print '$NEW CLIENT : %d clients are connected' % self.factory.numProtocols

        if self.factory.numProtocols &gt; 100:
            self.transport.write("Too many connections, try later")
            self.transport.loseConnection()

    def connectionLost(self, reason):
        self.factory.numProtocols = self.factory.numProtocols - 1       
        print '$CONNECTION LOST : %d clients are remained' % self.factory.numProtocols

    def startedConnecting(self, connector):
        print '$started connecting..'

class AdminFactory(ServerFactory):
    protocol = AdminProtocol
    numProtocols = 0
    ID = 0

    def __init__(self):
        print 'initialized'
```

Service로 등록하기 위해,
service.Service를 상속받아 각각 ServerFactory와 ClientFactory를 리턴하도록 구현한다.  

```python
class AdminService(service.Service):
    def getTestFactory(self):
        f = TestFactory()
        f.protocol = TestClient
        return f

    def getAdminFactory(self):
        f = AdminFactory()
        f.protocol = AdminProtocol
        return f</td>

애플리케이션 서버로 묶어 주기 위해, 다음과 같이 등록한다.

```python
application = service.Application('test', uid=1, gid=1)
f = AdminService()

serviceCollection = service.IServiceCollection(application)
internet.TCPServer(8002, f.getAdminFactory()).setServiceParent(serviceCollection)
internet.TCPClient('localhost', 8001, f.getTestFactory()).setServiceParent(serviceCollection)
```

이와 같이 작성한 파이썬 파일을 tac 파일로 변경하면, twisted application으로 실행할 수 있게 되는데, 이대 twistd 명령어로 실행시켜야 한다.
```
twistd -ny finger.tac   #just like before
twisted -y finger.tac   # daemonize, keep pid in twistd.pid
