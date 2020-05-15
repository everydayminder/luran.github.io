---
layout: single
title: Twisted Matrix로 TCP Client 만들기
date: 2008-09-19 02:01:38.000000000 +09:00
categories:
- development
tags: [twisted, tcp]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
앞서 간단히 만든 서버에 접속할 클라이언트를 만들어 봤다.
### 프로토콜
```python
class TestClient(LineReceiver):
	def connectionMade(self):
		self.sendLine("A new connection has been made!")
		self.factory.clientReady(self)
	
	def lineReeived(self, line):
	    print "$ got msg [%s]rn" % line

    def connectionLost(self, reason):
	    reactor.stop()
```
### 팩토리
```python
class TestFactory(ClientFactory):
    protocol = TestClient
    
    def __init__(self):
        self.startFactory()
    
    def clientConnectionFailed(self, connector, reason):
        reactor.stop()
        
    def clientConnectionLost(self, connector, reason):
        reactor.stop()
    
    def startFactory(self):
        print '-- startFactory() called'
        self.messageQueue = []
        self.clientInstance = None

    def clientReady(self, instance):
        print '-- clientReady() called'
        self.clientInstance = instance
        for msg in self.messageQueue:
            self.sendMessge(msg)

    def sendMessage(self, msg):
        if self.clientInstance is not None:
            print '$ client instance is not null'
            self.clientInstance.sendLine(msg)
        else:
            print '$ client instance is null'
            self.messageQueue.append(msg)
```

참고로, 창의적으로 고안해서 만든게 아니라 googling 해서 뜨는 걸 보고 
작성했다. 아마도 메일링리스트 였던 듯. (나중에 출처를 찾게 되면 꼭 수정해야겠다)

이와 같이, 클라이언트용 프로토콜과 팩토리를 만들었으면 서버에 접속하도록 해보자.

```python
if __name__ == '__main__':
    f = TestFactory()
    reactor.connectTCP('127.0.0.1', 8001, f)
    reactor.run()
```

이렇게 하면, 앞서 띄운 서버에 연결을 하게 되고, 웬만하면 연결이 끊기지 않는다.
(기특하다)
물론, 몇몇 필요한 클래스는  앞서 import 해줘야 한다.

예)
```python
from twisted.protocols.basic import LineReceiver

from twisted.internet.protocol import ClientFactory, ServerFactory
from twisted.internet import reactor
```
