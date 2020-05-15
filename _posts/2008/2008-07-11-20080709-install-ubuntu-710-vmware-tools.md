---
layout: single
title: 20080709 Install Ubuntu 7.10 & VMware-tools
date: 2008-07-11 05:13:19.000000000 +09:00
categories: [system]
tags: [tomcat]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
---
[C] Installiation of Ubuntu 7.10
--------------------------------
* flow
 * downloaded the image file 
 * installation complete (without security packages)

* comments
 * installed Ubuntu 8.04 (the latest version)
 * failed to install other development packages on it
 * version 8.04 seems NOT stable as 7.xx -&gt; let's install version 7.10 instead
 * it takes too long!! (about an hour) 
 &nbsp; -&gt; backing up the vmware image file after installation will be desirable


[C] Installation of vmware-tools 
--------------------------------
* choose 'Install VMware-tools' from the VMWare menu
* mount VMware tools from the desktop
* copy VMware tools tar.gz file to anywhere that i want
* extract the archive file, and change the permission to root user by 'sudo -i'
* execute vmware-install.pl

