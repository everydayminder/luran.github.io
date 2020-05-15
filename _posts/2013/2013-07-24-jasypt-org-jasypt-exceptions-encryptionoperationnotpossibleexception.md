---
layout: single
title: jasypt 사용중 org.jasypt.exceptions.EncryptionOperationNotPossibleException가 발생한다면?
date: 2013-07-24 08:55:04.000000000 +09:00
categories:
- development
tags: [hibernate, java, jasypt]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
먼저, encrypt에 사용한 설정이 decrypt에 사용한 설정과 동일한지 확인하자.
그리고, DB의 필드가 충분히 긴지 확인하자.

나의 경우는, 위의 두 경우와 다른 경우였다.
output type을 default인 Base64에서 Hexadecimal로 변경하니, 위의 문제가 해결되었다.
이에 대한, jasypt의 공식 FAQ는 다음과 같다.

<blockquote>
I keep on receiving EncryptionOperationNotPossibleException exceptions when trying to decrypt my data.

EncryptionOperationNotPossibleException is a very general exception which jasypt raises whenever there is a problem with encryption or decryption operations. It does not provide any further information to prevent the encryption infrastructure from showing too much information about what is going on (we wouldn't want an attacker to get any algorithm-specific errors...)

When you get that error while decrypting, most of the times it will simply mean that the encrypted string you input was not adequate for the algorithm/password/keyObtentionIterations configuration you provided. Check that your encryptor is configured in exactly the same way as the one with which you originally encrypted the data.

Also, if you are storing your encrypted data into a database, check that the table columns that you use to store it are big enough to host the encrypted data (which is always bigger than the original data). If you are transmitting your encrypted data via HTTP, check that you are not having problems with the transmission of BASE64-encoded data as URL parameters (BASE64 uses characters which are forbidden in URL parameters, like "="). For these uses, try using hexadecimal output.
</blockquote>
