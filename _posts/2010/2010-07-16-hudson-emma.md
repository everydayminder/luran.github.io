---
layout: single
title: hudson - emma와 연동하기 (1/2)
date: 2010-07-16 06:51:58.000000000 +09:00
categories:
- development
tags: [ant, ci, coverage, emma, hudson, java, test]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
지난 글t에서 hudson에 JUnit 테스트를 수행하는 방법에 대해 소개하였다.
물론, 코드의 품질은 어떤 테스트 코드를 어떻게 작성하느냐에 코드의 신뢰도가 달라진다.

그렇다면, 좋은 테스트는 테스트 케이스의 수에 단순히 비례할까?
두 말할 필요 없이 얼마나 양질의 테스트가 어떻게 수행되었는지가 중요할 것이다. 

본 포스트에서 말하고자 하는 metric은 테스트의 커버리지(coverage)이다.
즉, 테스트의 커버리지가 높은 프로젝트 코드들은 검증을 거친 부분이 많으므로, 상대적으로 양질이라고
볼 수 있다.

Emma는 프로젝트 코드와, 프로젝트 코드를 테스트하는 테스트 코드를 조합하여 비교함으로써,
주어진 테스트 코드가 원본 소스 코드에 대해 어느 정도의 커버리지를 갖는지를 조사해 준다.

<span style="font-weight:bold;">Emma는 어디에?</span>
Emma는 <a title="[http://emma.sourceforge.net/]로 이동합니다." target="_blank" href="http://emma.sourceforge.net/">http://emma.sourceforge.net/</a>에서 다운로드하거나, 설명을 찾아볼 수 있다.

<span style="font-weight:bold;">Emma의 기능은?</span>
Emma의 공식 사이트에 소개된 기본 기능은 다음과 같다.

<ul>
<li>
EMMA can instrument classes for coverage either
	<em>offline</em> (before they are loaded) or <em>on
	the fly</em> (using an instrumenting application classloader).
</li>
<li>

Supported coverage types:
	<em>class</em>, <em>method</em>,
	<em>line</em>, <em>basic&nbsp;block</em>. EMMA
	can detect when a single source code line is covered only partially.
</li>
<li>

Coverage stats are aggregated at method, class,
	package, and "all classes" levels.
</li>
<li>

Output report types: plain text, HTML, XML. All
	report types support drill-down, to a user-controlled detail
	depth. The HTML report supports <em>source code
	linking</em>.
</li>
<li>

Output reports can highlight items with coverage
	levels below user-provided thresholds.
</li>
<li>

Coverage data obtained in different
	instrumentation or test runs can be merged together.
</li>
<li>

EMMA does not require access to the source
	code and degrades gracefully with decreasing amount of debug
	information available in the input classes.
</li>
<li>

EMMA can instrument individial
	<strong>.class</strong> files or entire
	<strong>.jar</strong>s (in place, if desired). Efficient <em>coverage
	subset filtering</em> is possible, too.
</li>
<li>

Makefile and ANT build integration are supported
	on equal footing.
</li>
<li>

EMMA is quite <em>fast</em>: the runtime
        overhead of added instrumentation is small (5-20%) and the
bytecode instrumentor
        itself is very fast<!-- TODO: add a data point or link -->
(mostly
        limited by file I/O speed). Memory overhead is a few hundred
bytes per Java class.
</li>
<li>

EMMA is 100% pure Java, has no external
        library dependencies, and works in any Java 2 JVM (even 1.2.x).
</li>
</ul>

좋은 기능이지 아니한가? -_-

<span style="font-weight:bold;">그렇다면, Hudson과의 연동은?</span>
위의 기능 중,

<ul style="list-style-type:disc;">
<li>Report가 가능하다</li>
<li>XML로 report를 생성한다.</li>
<li>Ant task로 수행할 수 있다.</li>
</ul>

등의 기능을 활용하여, 기존의 build.xml에 연동해 볼 것이다.

<span style="font-weight:bold;">Emma를 설치하자</span>
앞서 말한, emma 사이트로부터 emma 배포본을 다운로드한다. 
본인은 emma-release > 2.0.5312 > emma-2.0.5312.zip 를 다운로드 하였다.

Hudson이 설치된 서버의 적당한 작업공간에 해당 파일을 압축 해제한다.

예) c:devemma-2.0.5312

그리고, emma의 설치 디렉토리를 환경 변수로 지정한다.

예) EMMA_LIB=c:devemma-2.0.5312

<span style="font-weight:bold;">build.xml에 emma 설정을 추가하자</span>

build.xml에 다음과 같이 emma의 기본 환경변수를 선언한다.
```
<!-- emma configuration/directories -->
	<property name="emma.enabled" 		value="true"/>
	<property name="emma.lib.dir"		value="${env.EMMA_LIB}"/>
	<property name="emma.coverage.dir" 	value="${report.home}/emma"/>
	<property name="emma.origin.dir"	        value="${build.home}"/>
	<property name="emma.instr.dir"		value="${basedir}/instr"/>

	<!-- emma libraries -->
	<path id="emma.lib">
		<pathelement location="${emma.lib.dir}/emma.jar"/>
		<pathelement location="${emma.lib.dir}/emma_ant.jar"/>
	</path>
```	

emma가 수행하는 태스크를 일단, 다음의 순서대로 설정하고자 하였다.
setup -> instrument -> emma 기반 test -> emma report 생성

ant 태스크의 정의로부터, emma.setup, emma.instrument, emma-test, emma-report의 target은 다음과 같이 선언한다.

```
<!-- target : emma-setup -->
	<taskdef resource="emma_ant.properties" classpathref="emma.lib"/>
	
	<target name="emma.setup" depends="compile,test-compile" description="emma setup">
		<mkdir dir="${emma.instr.dir}"/>
		<mkdir dir="${emma.coverage.dir}"/>
	</target>
	
	<!-- target : emma-instrument -->
	<target name="emma.instrument" depends="emma.setup" description="emma instrument">
		<emma enabled="${emma.enabled}">
			<instr instrpath="${emma.origin.dir}"
				destdir="${emma.instr.dir}"
				metadatafile="${emma.coverage.dir}/metadata.emma"
				merge="true">
				<filter value="" />
			</instr>
		</emma>
	</target>
	
	<!-- target : emma-test -->
	<target name="emma.test" depends="emma.instrument" description="junit test with emma">
		<mkdir dir="${report.home}/junit" />
		<junit printsummary="yes" maxmemory="512m" haltonfailure="false" fork="true">
			<!--<classpath refid="test.classpath" />-->
			<classpath>
				<pathelement location="${emma.instr.dir}"/>
				<pathelement location="${emma.origin.dir}"/>				
				<pathelement location="${test.build.home}"/>
				<path refid="emma.lib"/>
			</classpath>
			<classpath refid="project.classpath" />
			<!-- <formatter type="brief" usefile="false" /> -->
			<formatter type="xml"/>

			<jvmarg value="-Demma.coverage.out.file=${emma.coverage.dir}/coverage.emma"/>
			<jvmarg value="-Demma.coverage.out.merge=true"/>
				
			<batchtest todir="${report.home}/junit">
				<fileset dir="${test.build.home}" includes="**/*Test.class">
				</fileset>
			</batchtest>
		</junit>

		<junitreport todir="${report.home}/junit">
			<fileset dir="${report.home}/junit">
				<include name="TEST*.xml"/>
			</fileset>
			<report format="frames" todir="${report.home}/junit"/>
		</junitreport>
	</target>
	
	<!-- traget : emma-report -->
	<target name="emma.report" depends="emma.test" description="generate emma report">
		<emma enabled="${emma.enabled}" verbosity="verbose">
			<report sourcepath="${src.home}" sort="+block,+name,+method,+class"
				metrics="method:70,block:80,line:80,class:100">
				<fileset dir="${emma.coverage.dir}">
					<include name="*.emma" />
				</fileset>
				<!--
				<fileset dir="${basedir}" >
					<include name="*.ec" />
				</fileset>
				-->
				<xml outfile="${emma.coverage.dir}/coverage.xml" depth="method"/>
				<html outfile="${emma.coverage.dir}/coverage.html" depth="method"/>
			</report>
		</emma>	
	</target>
```

이제 해당 디렉토리로부터 ant emma.report를 실행하면, 커버리지 리포트 디렉토리로 설정해 놓은 곳에 
coverage.html과 coverage.xml이 생성되어 있는 것을 확인할 수 있다. 

다음에는 이렇게 생성한 리포트를 hudson에 연동해보자.

