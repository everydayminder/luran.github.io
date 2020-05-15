---
layout: single
title: queue의 push/pop 속도 비교
date: 2009-03-30 03:58:32.000000000 +09:00
categories:
- development
tags: [python, performance]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
ㅁ Queue.Queue
&nbsp;&nbsp; 1. push('a') : 6.39s
&nbsp;&nbsp; 2. push('a') + pop() : 11.37s

ㅁ collections.dequeue
&nbsp;&nbsp; 1. append('a') : 0.13s
&nbsp;&nbsp; 2. append('a') + popleft() : 0.27s

ㅁ list
&nbsp; 1. append('a') : 0.15s
&nbsp; 2. append('a') + pop() : 0.40s

Queue를 쓰려면, dequeue를 활용하는 것이 좋을 것 같다.

