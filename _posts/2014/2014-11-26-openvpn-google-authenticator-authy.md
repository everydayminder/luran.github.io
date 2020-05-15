---
layout: single
title: OpenVPN-AS와 Google Authenticator/ Authy 연동 설정하기
date: 2014-11-26 00:45:02.000000000 +09:00
categories:
- system
tags: [openvpn, authy, google, otp]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
알고보니, OpenVPN-AS의 경우 Google Authenticator/ Authy 연동을 쉽게 할 수 있었다.

Authentication 메뉴에
- General
- PAM
- RADIUS
- LDAP

가 있길래, RADIUS 혹은 PAM으로 할까 했는데,
의외로 메뉴가 숨어 있었다.

Configuration &gt; Client Settings 메뉴로부터
설정 가능하다.

![Image Alt 설정화면]({{ site.baseurl }}/images/201411/gotp.png)

과 같은 체크박스가 있고, 체크해 주면 된다.
그리고, VPN 서버에 반영 혹은 재시작 시키자.

이것만으로는 접속시 OTP를 사용할 수 없다.
(아직 OTP 등록도 하지 않았으니까)

기존에 OTP 설정 전에 받아 놓은 opvn 프로파일을 사용하여 VPN 접속을 시도하면,
OTP를 묻지도 않고, 기존 방식과 동일하게 인증하고 접속시켜 버린다.

OTP 설정을 위해, 처음의 절차를 다시 밟자.
클라이언트로부터 https://주소:943을 접속해보면, 이전과는 다른 화면을 볼 수 있다.

ovpn 프로파일을 받는 부분 하단으로, google authenticator 안내문과
QR코드 그리고 직접 입력할 경우를 대비한 코드가 보일 것이다.

이 정보를 활용하여, QR코드로 찍든 코드를 직접 입력하든 선택하자.
이 단계에서 google authenticator를 사용하거나, authy를 사용하거나 상관없다.
내 경우는 authy를 사용해서 테스트 했고, 정상적으로 등록 가능했다.

그리고, 다시 OVPN 파일을 클라이언트로에 다운로드 하자.
OTP 설정 이전의 프로파일은 클라이언트로부터 삭제하자.

새로 받은 프로파일을 사용하여 VPN 접속을 시도하면,
이제 기존의 id/password와 함께 OTP도 함께 묻는 것을 확인할 수 있다.
