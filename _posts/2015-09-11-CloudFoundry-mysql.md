---
layout: post
author: sun
title:  "Cloud Foundry mysql service 安装"
date:   2015-09-11 23:12:50
categories: CLoudFoundry
---

#Cloud  Foundry mysql service 安装

###根据上一篇中安装好Cloud Foundry之后，此时的CF是没有DB服务的，在CF体系中将DB服务以service服务的形式展现，那么想要一个完整的PaaS环境一个DB服务是必不可少的，下面将介绍mysql service的安装方式

##**前期准备：**
----------

###已经部署成功的CF组件环境

----------

##**安装CF Mysql Service服务：**

###1.从github上获取”安装包”，下载mysql-release

####cd workspace目录下

~~~java
cd ~/workspace
git clone https://github.com/cloudfoundry/cf-mysql-release.git
~~~

####其目录结构如下：
~~~java
└── workspace
	    ├── bosh-lite
	    ├── cf-mysql-release
	    ├── cf-release
	    └── go
~~~

###2.上传cf mysql release

####确认cf-mysql-<N>.yml为当前安装版本，若此时是第一次安装则会下载会很慢，mysql的安装包一样会下载在/home/.bosh目录下，后续可手动安装使用

~~~java
 bosh upload release releases/cf-mysql-<N>.yml
 我这里是
 bosh upload release releases/cf-mysql-22.yml
~~~

###3.创建cf-mysql-manifest.yml

####需要先安装spiff，执行以下脚本

~~~java 
/home/vagrant/workspace/cf-mysql-release/bosh-lite/make_manifest
~~~

####此时会在/home/vagrant/workspace/cf-mysql-release/bosh-lite/manifests路径下生成并指定cf-mysql-manifest.yml部署文件，若没指定则可手动指定

~~~java 
bosh deployment /home/vagrant/workspace/cf-mysql-release/bosh-lite/manifests/cf-mysql-manifest.yml
~~~

###4.执行部署

####以上准备就绪后则可执行

~~~java 
bosh deploy 
~~~

####开始部署，一般会20分钟左右

###7.查看部署情况

~~~java 
bosh vms 
~~~

####来验证是否安装成功，若每个Container 都为running则无异常

##**若想正常使用mysql service则还需如下步骤：**

###8.创建mysql service  broker

~~~java
 bosh run errand broker-registrar 
~~~

###8.创建Security Groups **注意此步骤非常关键，如不设置则CF app 无法访问所绑定的Service**

####创建rule.json文件，内容如下，具体根据IP设定

~~~java 

	[
		{
			"destination": "10.244.4.0-10.254.0.0",
			"protocol": "all"
		},
		{
			"destination": "10.0.0.0-10.254.0.0",
			"protocol": "all"
		}
	]

~~~

####为p-mysql service 服务创建绑定Security group

~~~java
cf create-security-group p-mysql rule.json
cf bind-running-security-group p-mysql 
~~~

###9.测试mysql是否能够正常使用

~~~java 
bosh run errand acceptance-tests 
~~~