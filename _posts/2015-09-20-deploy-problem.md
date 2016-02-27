---
layout: post
author: sun
title:  "Cloud Foundry 安装问题汇总"
date:   2015-09-25 11:12:49
categories: CLoudFoundry
---

# cloud foundry 问题汇集

------

##环境错误

```java

Failed compiling packages > galera/d15a1d2d15e5e7417278d4aa1b908566022b9623: Action Failed get_task: Task a2e933db-cb97-4507-714e-8005420d3700 result: Compiling package galera: Running packaging script: Command exited with 2; Stdout: , Stderr: + GALERA_VERSION=25.3.9
+ export HOME=/var/vcap/data
+ HOME=/var/vcap/data
+ cd galera
+ tar xfz galera-25.3.9-src.tar.gz
+ cd galera-release_25.3.9
+ cp -R /var/vcap/packages/boost/lib/libboost_program_options.a /var/vcap/packages/boost/lib/libboost_program_options.so /var/vcap/packages/boost/lib/libboost_program_options.so.1.55.0 /var/vcap/packages/boost/lib/libboost_system.a /var/vcap/packages/boost/lib/libboost_system.so /var/vcap/packages/boost/lib/libboost_system.so.1.55.0 /usr/lib/
+ cp -R /var/vcap/packages/boost/include/boost /usr/include/
+ cp -R /var/vcap/packages/check/lib/libcheck.a /var/vcap/packages/check/lib/libcheck.la /var/vcap/packages/check/lib/libcheck.so /var/vcap/packages/check/lib/libcheck.so.0 /var/vcap/packages/check/lib/libcheck.so.0.0.0 /var/vcap/packages/check/lib/pkgconfig /usr/lib/
+ cp -R /var/vcap/packages/check/include/check.h /var/vcap/packages/check/include/check_stdint.h /usr/include/
+ PATH=/var/vcap/bosh/bin:/usr/local/bin:/usr/local/sbin:/bin:/sbin:/usr/bin:/usr/sbin:/usr/X11R6/bin:/var/vcap/packages/python/bin
+ set +e
+ /var/vcap/packages/scons/bin/scons
+ BUILD_EXIT_CODE=2
+ set -e
+ '[' 2 -ne 0 ']'
+ tail -n 1000 build.err
+ exit 2

```

问题分析：

> * 在安装过程中需要执行编译，而执行部署的当前主机ruby不完整，将会出现该错误，此处为bundle未安装

##路由错误

该问题出自于，cf-mysql-service 部署好之后，但为成功启动。使用bosh ssh 登录到borker虚拟机，查看broker的log信息

```java

/var/vcap/packages/ruby/lib/ruby/2.1.0/net/http.rb:879:in `initialize': Connection timed out - connect(2) for "api.buaa.xip.io" port 80 (Errno::ETIMEDOUT)
	from /var/vcap/packages/ruby/lib/ruby/2.1.0/net/http.rb:879:in `open'
	from /var/vcap/packages/ruby/lib/ruby/2.1.0/net/http.rb:879:in `block in connect'
	from /var/vcap/packages/ruby/lib/ruby/2.1.0/timeout.rb:76:in `timeout'
	from /var/vcap/packages/ruby/lib/ruby/2.1.0/net/http.rb:878:in `connect'
	from /var/vcap/packages/ruby/lib/ruby/2.1.0/net/http.rb:863:in `do_start'
	from /var/vcap/packages/ruby/lib/ruby/2.1.0/net/http.rb:852:in `start'
	from /var/vcap/packages/ruby/lib/ruby/2.1.0/net/http.rb:1369:in `request'

```

问题分析：

> * mysql service安装好之后，若出现该问题说明cf-mysql-broker 虚拟机咩有 api.buaa.xip.io的路由规则，最简单的方式就是直接在/etc/hosts文件中加入（IP + api.buaa.xip.io）信息

##无法连接ccdb

看了下面这个帖子遇到了无法连接的问题

>https://github.com/nimbus-cloud/cloudfoundry-documentation/wiki/Connecting-to-the-PostgreSQL-Database

```java

root@b7ce202f-d03b-4078-b0ea-36511bb6d846:/var/vcap/data/packages/postgres/b63fe0176a93609bd4ba44751ea490a3ee0f646c.1-64917e330bb9c52377b380eb3742f1fae01a71b0/bin# ./psql -h 10.0.0.21 -p 5524  ccdb -U ccadmin
Password for user ccadmin: 
psql (9.0.3, server 9.4.2)
WARNING: psql version 9.0, server version 9.4.
         Some psql features might not work.
Type "help" for help.

Cannot read termcap database;
using dumb terminal settings.
Aborted

```

问题分析：

> * 在shell输入  infocmp -C > /etc/termcap   然后再./psql -h 10.0.0.21 -p 5524  ccdb -U ccadmin

