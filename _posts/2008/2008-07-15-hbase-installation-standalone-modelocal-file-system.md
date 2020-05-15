---
layout: single
title: HBase Installation (Standalone mode/local file system)
date: 2008-07-15 08:48:02.000000000 +09:00
categories: [system]
tags: [hadoop]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
Requirements
-------------
- Java 1.5.x, preferably from Sun. 
- Hadoop 0.16.x. This version of HBase will only run on Hadoop 0.16.x.. 
- ssh must be installed and sshd must be running to use Hadoop's scripts 
&nbsp;&nbsp; to manage remote Hadoop daemons. 
- HBase currently is a file handle hog. The usual default of 1024 on *nix systems 
&nbsp;&nbsp; is insufficient if you are loading any significant amount of data into regionservers. 
&nbsp; See the FAQ: Why do I see "java.io.IOException...(Too many open files)" in my logs?&nbsp; for how to up the limit. 


[C] Installation of HBase
--------------------------
1. Java 1.5.x 
	- installed java 1.6.x
	- Not sure whether 1.6.x will work
2. Unzipped the Hadoop archive file (ver 1.3) at ~/hbase
3. ssh &amp; sshd configuration (execution)
	- sudo apt-get install openssh-server
	- sudo /etc/init.d/ssh start (stop)
4. edit the limit number of file handlers
	- checked /etc/security/limits.conf but there is no option to edit for this


[C] HBase Configuration
------------------------
1. ${HBASE_HOME} : the real location of hbase (/home/luran/hbase)
2. modify JAVA_HOME information on ${HBASE_HOME}/conf/hbase-env.sh
3. start HBase by ${HBASE_HOME}/bin/start_hbase.sh
4. stop HBase by ${HBASE_HOME}/bin/stop-hbase.sh

