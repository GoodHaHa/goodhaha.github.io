---
layout: post
author: sun
title:  "Cloud Foundry 安装问题汇总"
date:   2015-09-20 16:02:20
categories: CLoudFoundry
---

# cloud foundry 安装错误汇集

------

##环境错误

```python
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