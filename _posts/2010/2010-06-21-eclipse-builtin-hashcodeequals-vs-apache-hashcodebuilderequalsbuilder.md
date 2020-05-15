---
layout: single
title: Eclipse의 내장 hashCode()+equals() VS. Apache의 HashCodeBuilder+EqualsBuilder
date: 2010-06-21 07:20:23.000000000 +09:00
categories:
- development
tags: [eclipse, hash, equals]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
Eclipse에 내장된 기능으로도 특정 객체의 hashCode()와 equals()를 작성할 수 있다.
사용법은,
마우스 오른쪽 클릭하여 나오는 context 메뉴로부터 Source > Generate hashCode() and equals() ... 를 클릭하면 된다.

<img src="{{ site.baseurl }}/images/201007/cfile8-uf-127b26174c1f10fe3caa61.png" class="aligncenter" width="700" height="825" alt="" />
그러면, 다음과 같은 코드가 생성될 것이다.

```java
	@Override
	public int hashCode() {
		final int prime = 31;
		int result = 1;
		result = prime * result + ((link == null) ? 0 : link.hashCode());
		result = prime * result
				+ ((menuCode == null) ? 0 : menuCode.hashCode());
		result = prime * result
				+ ((menuName == null) ? 0 : menuName.hashCode());
		result = prime * result
				+ ((upperCode == null) ? 0 : upperCode.hashCode());
		return result;
	}

	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		MenuItem other = (MenuItem) obj;
		if (link == null) {
			if (other.link != null)
				return false;
		} else if (!link.equals(other.link))
			return false;
		if (menuCode == null) {
			if (other.menuCode != null)
				return false;
		} else if (!menuCode.equals(other.menuCode))
			return false;
		if (menuName == null) {
			if (other.menuName != null)
				return false;
		} else if (!menuName.equals(other.menuName))
			return false;
		if (upperCode == null) {
			if (other.upperCode != null)
				return false;
		} else if (!upperCode.equals(other.upperCode))
			return false;
		return true;
	}
```

그런데, Apache의 라이브러리를 사용하면, 보다 깔끔한(!) 코드와 가독성을 얻을 수 있다.
우선, common-lang*.jar를 구하고, 다음과 같이 작성한다.

```java
import org.apache.commons.lang.builder.EqualsBuilder;
import org.apache.commons.lang.builder.HashCodeBuilder;

...
	@Override
	public int hashCode() {
		return new HashCodeBuilder(3, 11)
		.append(upperCode)
		.append(menuCode)
		.append(menuName)
		.append(link)
		.toHashCode();
	}
	
	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		
		MenuItem other = (MenuItem) obj;
		
		return new EqualsBuilder()
		.append(this.upperCode, other.upperCode)
		.append(this.menuCode, 	other.menuCode)
		.append(this.menuName, 	other.menuName)
		.append(this.link, 		other.link)
		.isEquals();
	}
```


