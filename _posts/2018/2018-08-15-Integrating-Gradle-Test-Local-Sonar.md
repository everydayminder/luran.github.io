---
layout: single
categories:
- development
tags: [docker, sonarqube]
---
### Docker로 sonarqube 설치

```shell
docker pull sonarqube
```

sonarqube 이미지를 다운로드하고 실행시킨다.

```shell
docker run -d --name sonarqube -p 9000:9000 -p 9092:9092 sonarqube:latest
```

이제 로컬 sonar를 확인할 수 있다.

```shell
http://localhost:9000
```



### Spring Boot Gradle Sample Project 생성

```shell
spring init --build gradle --package-name=mytest --name=mytest -g=mytest -a=mytest -x
```

 

### Gradle Property 선언

```groovy
buildscript {
...
dependencies {
...
		classpath "org.sonarsource.scanner.gradle:sonarqube-gradle-plugin:2.6.2"
	}
}

apply plugin: "org.sonarqube"
apply plugin: 'groovy'
apply plugin: 'jacoco'

...
sonarqube {
	properties {
		property "sonar.host.url", "http://localhost:9090"
		property "sonar.sources", "src"
		property "sonar.language", "java" 
		property "sonar.sourceEncoding", "UTF-8"
		property "sonar.exclusions", "**/*Test*.*, **/Q*.java"
		property "sonar.test.inclusions", "**/*Spec.groovy, **/*Test.java"
		property "sonar.coverage.exclusions", "**/*Test*.*, **/Q*.java"
		property "sonar.java.junit.reportPaths", "${buildDir}/test-results"
		property "sonar.jacoco.reportPaths", "${buildDir}/jacoco/jacoco.exec"
	}
}

dependencies {
	testCompile 'org.spockframework:spock-core:1.1-groovy-2.4'
	testCompile 'org.spockframework:spock-spring:1.1-groovy-2.4'
	testRuntime 'cglib:cglib-nodep:3.2.4'
}

```

기존 build.gradle에 위 내용을 추가한다.



### Gradle task로 sonarqube 확인

```shell
./gradlew test sonarqube -Dsonar.host.url=http://localhost:9000
```

로컬에서 테스트한 결과를 sonarqube로 연동하여 곧바로 확인할 수 있다.

