---
layout: single
title: FindBugs - EQ_COMPARETO_USE_OBJECT_EQUALS 해결
date: 2010-06-21 06:56:44.000000000 +09:00
categories:
- development
tags: [java, findbugs, coverage]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
사용자가 compareTo()를 override 했을 경우, 볼 수 있는 warning이다.
FindBugs의 code inspection에 따르면,

<div style="border:1px dashed rgb(121,165,228);background-color:rgb(219,232,251);padding:10px;" class="txc-textbox">
 This class defines a <code>compareTo(...)</code> method but inherits
 its
  <code>equals()</code> method from <code>java.lang.Object</code>.
	Generally, the value of compareTo should return zero if and only if
	equals returns true. If this is violated, weird and unpredictable
	failures will occur in classes such as PriorityQueue.
	In Java 5 the PriorityQueue.remove method uses the compareTo method,
	while in Java 6 it uses the equals method.

From the JavaDoc for the compareTo method in the Comparable
interface:

<blockquote>
It is strongly recommended, but not strictly required that <code>(x.compareTo(y)==0)
 == (x.equals(y))</code>.
Generally speaking, any class that implements the Comparable interface
and violates this condition
should clearly indicate this fact. The recommended language
is "Note: this class has a natural ordering that is inconsistent with
equals."
</blockquote>
</div>

이라는 설명을 볼 것이다.

이 문제의 해결책은,
hashCode()와 equals()를 실제 구조에 맞게 다시 override함으로써 해결 가능하다.

