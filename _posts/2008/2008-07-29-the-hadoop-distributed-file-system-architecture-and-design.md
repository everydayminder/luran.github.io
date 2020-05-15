---
layout: single
title: 'The Hadoop Distributed File System : Architecture and Design 요약'
date: 2008-07-29 06:37:54.000000000 +09:00
categories:
- system
tags: []
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
본 내용은 해당 링크내의 원본을 읽고, 키워드 등의 관점에서 축약하였음

원본 링크 : 
<a href="http://hadoop.apache.org/core/docs/current/hdfs_design.html">http://hadoop.apache.org/core/docs/current/hdfs_design.html</a>

본 내용은 해당 링크내의 원본을 읽고, 키워드 등의 관점에서 축약하였음

■ Introduction
  - HDFS is highly fault-tolerant and is designed to be deployed on low-cost hardware.
  - HDFS provided high throughtput access to application data and is suitable for applications that have large data sets.


■ Assumptions and Goals
 - Hadrware Failure : detection of faults and quick, automatic recovery
 - Streaming Data Access : It is designed more for batch processing rather than interactive use by users
 - Large Data Sets : It provides high aggregate data bandwidth and scale to hundreds of nodes in a single cluster.
 - Simple Coherency Model : write-once-read-many access model for files
 - "Moving Computation is Cheaper than Moving Data" : It is often better to migrate the computation closer to where the data is located rather than moving the data to where the application is running. HDFS provides interfaces for applications to move themselves closer to where the data is located.
 - Portability Across Heterogeneous Hardware and Software Platforms : HDFS is easily portable from one platform to another.

