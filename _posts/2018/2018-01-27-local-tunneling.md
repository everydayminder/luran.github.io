---
layout: single
title: local tunneling을 통해 외부로부터 접속 허용하기
date: 2018-01-27 16:43:44.000000000 +09:00
type: post
categories:
- system
tags: [local, tunneling]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
SSH tunneling을 통한 port forwarding을 하고자, putty나 secure crt로 설정하고 썼다.
혹은 집의 내부 서버에 DNSEver 같은 DDNS 서비스랑 연결하고, 내부에서 포트 포워딩을 하거나.
그러다가, 손쉽게 local tunneling을 구축할 수 있는 서비스를 찾아보게 되었고, 간단하게 비교해 보았다.

## ngrok : https://ngrok.com/

```
NAME:
ngrok - tunnel local ports to public URLs and inspect traffic

DESCRIPTION:
ngrok exposes local networked services behinds NATs and firewalls to the
public internet over a secure tunnel. Share local websites, build/test
webhook consumers and self-host personal services.
Detailed help for each command is available with 'ngrok help <command></command>'.
<command></command> Open http://localhost:4040 for ngrok's web interface to inspect traffic.

EXAMPLES:
ngrok http 80 # secure public URL for port 80 web server
ngrok http -subdomain=baz 8080 # port 8080 available at baz.ngrok.io
ngrok http foo.dev:80 # tunnel to host:port instead of localhost
ngrok tcp 22 # tunnel arbitrary TCP traffic to port 22
ngrok tls -hostname=foo.com 443 # TLS traffic for foo.com to port 443
ngrok start foo bar baz # start tunnels from the configuration file

VERSION:
2.2.8

AUTHOR:
inconshreveable - <alan@ngrok.com>

COMMANDS:
authtoken save authtoken to configuration file
credits prints author and licensing information
http start an HTTP tunnel
start start tunnels by name from the configuration file
tcp start a TCP tunnel
tls start a TLS tunnel
update update ngrok to the latest version
version print the version string
help Shows a list of commands or help for one command
```

<img class="alignnone wp-image-840 size-full" src="{{ site.baseurl }}/images/201802/ngrok_running.png" alt="" width="610" height="161" />
위에서 보는 바와 같이, localhost:32769를 dd8b0a83.ngrok.io로 연결시켜준다.
문제는, 실행할 때마다 저 subdomain이 매번 바뀐다는 점.

-subdomain 옵션을 사용하면, subdomain을 원하는 고정 값으로 사용할 수 있으나, 유료.

장점)
- 사용하기 쉽다.
```
ngrok http 8080
```
이라고만 하면, 곧바로 설정된다.
- 실행하면, 곧바로 console 창에 사용중인 트래픽이 뜨기도 하고, 별도의 웹으로도 조회가 가능하다.

단점)
- 무료 버전의 경우, 분당 40 요청만 처리 가능하다.
- 저 주소가 실행할 때마다 바뀐다. 못 외움. 일시적으로 잠깐 쓰기는 좋음

## LocalTunnel : https://localtunnel.github.io/www/
```
[num]

Options:
-h, --host Upstream server providing forwarding
[default: "http://localtunnel.me"]
-s, --subdomain Request this subdomain
-l, --local-host Tunnel traffic to this host instead of localhost, override
Host header to this host
-o, --open opens url in your browser
-p, --port Internal http server port [required]
--help Show this help and exit [boolean]
--version Show version number [boolean]
```
위의 옵션을 사용하여, 앞서 ngrok를 설정한 것처럼 다음과 같이 띄울 수 있다.

```
lt -s mytest -p 32769
```

그러면, https://mytest.localtunnel.me로 해당 매핑이 설정된다! 무료로!

장점)
- 무료
- 별도 유료 정책 등 신경쓸게 없다.
- 서브도메인을 쉽게 설정할 수 있다.

단점)
- 다소 느린 듯?
- npm을 깔아야 한다.

## pagekite : https://pagekite.net/
<img class="alignnone wp-image-841 size-full" src="{{ site.baseurl }}/images/201802/pagekite_pricing.png" alt="" width="787" height="439" />

트라이얼 기간동안 일정 사용량이 있다.
설치하면, 사용할 subdomain을 입력하라고 안내한다.

장점)
- python 2.7 기준으로 동작한다.
- 소스까지 무려 공개한다.
- localtunnel 보다 빠른 것 같다.
- 서브 도메인을 쉽게 지정할 수 있고, 옵션이 많다.

단점)
- python 3.x에서는 안된다고 한다.
- 빠른데는 이유가 있다. quota 제도임
