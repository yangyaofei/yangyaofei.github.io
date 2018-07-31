---
layout	: post
title	: "Linux服务器安装配置JDK,tomca和Mysql"
date	: 2014-10-7
author	: "yangyaofei"
tags	:
    - Linux
    - web
---

# 一 安装和配置jdk

## 1 安装jdk

安装Oracle的jdk有两种方法:
一般安装jdk使在Oracle网站上下载二进制压缩包,然后解压配置.很麻烦,而且还要配置很多变量,有更新的时候需要自己更新.

ubutnu上有个ppa源webupd8team8team/java,这个里面的包可以自动配置jdk

首先更新以下软件

	sudo apt-get update
	sudo apt-get upgrade

添加ppa: ppa:webupd8team/java
更新并安装

	#安装java-installer PPA
	sudo add-apt-repository ppa:webupd8team/java
	sudo apt-get update
	sudo apt-get install oracle-java7-installer #安装Java
安装之后可能java7并没有配置环境变量等等问题,所以再安装这个:

	sudo apt-get install oracle-java7-set-default

有的主机会提示add-apt-repository没有这个命令,貌似没有安装,安装之

	sudo apt-get install python-software-properties
	#安装add-apt-repository

然后执行上述命令就可以安装成功了,运行下列代码来验证是否安装成功`javac --version`,如果上述返回的不是1.7的版本号,可能是机器上已经安装了别的jdk,除了安装时上面的软件进行设置.也可以用下面的命令来进行设置:

	sudo update-java-alternatives -s java-7-oracle
具体更详细的见ppa[教程](http://www.webupd8.org/2012/01/install-oracle-java-jdk-7-in-ubuntu-via.html)

## 2 配置jdk

配置JAVA_HOME,因为很多用java的软件依赖于这个变量,没有的话没法启动.

	vim /etc/environment
	#编辑内容,增加 JAVA_HOME=”/usr” CLASSPATH=”/usr/share/java”
	source /etc/environment #使其生效


# 三 安装和配置tomcat

和上面的一样,tomcat也有两种安装方法,但是不同的是软件仓库的安装方法是官方的,有官方的安装包谁会自己配置啊.何况自己安装的tomcat的会有权限问题,这个问题我们后面讲.

## 1 安装tomcat
	sudo apt-get install tomcat7 tomcat7-manager

## 2 配置
+ 进入`/var/lib/tomcat7/webapps/ROOT`下.
+ 删除并复制自己的web工程修改`/etc/tomcat7/Catalina/localhost/` 下对应的xml,文件名和目录要保持一致.
+ 可以在`localhost:8080/manager`下进行部分tomcat的配置,用户名和密码在对应的`/etc/tomcat7/tomcat-users.xml`下的进行配置

## 3 权限问题
如果是用二进制包自己进行安装的tomcat,运行的时候是root权限,这种情况下tomcat如果有漏洞的话,攻击着可以就此获取root权限,所以要为tomcat新建一个权限足够低的用户,用哪个用户区运行它才是安全的.
而用软件仓库自动安装的tomcat已经是在一个低权限的用户下运行的了,所以是安全的.当然也可以再将其权限进一步削弱.软件仓库安装的tomcat是用tomcat7为用户名,tomcat7为用户组来进行运行的.这个是tomcat7,如果是别的版本数字可能不同,查看进程就知道了

## 4 tomcat的运行和日志位置

用以下命令运行

	/etc/init.d/tomcat7 start
	# 或者
	start tomcat7

日志放在`/var/lib/tomcat7/logs`下的`catalina.日期`文件中输出查看即可

## 5 tomcat读写目录设置

有时候需要上传一些图片和文件,这时候如果用的是低权限运行的tomcat会无法新建目录和文件.因为目录和文件的所有者是root(在服务器上大部分是这样的),所以,当选定上传目录之后应该将目录的所有者改成tomcat运行的用户
以用户和组都是tomcat7为例应该`chown image tomcat7:tomcat7`

这样便不会报错,否则在查看日志的时候会发现有permission denied的错误

## 6 80端口转发

如果是以root权限运行的tomcat,在发布整个网站的时候只要将`/etc/tomcat7/server.xml`下的中的port参数修改成80即可.
但是如果是以低权限运行的时候是无法将端口设置成80的,因为在linux下,所有的1024一下的端口都要用root权限运行的程序才能bind到.所以需要进行端口转发.
用root权限运行下面的代码,就会把所有80端口的信息转发到8080上,这样网站也就可以发布了

	iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-port 8080

## 7 日常调试log
上面有说,我不重复了.单说一下是因为log很重要.在服务器上没法debug,log就成了最好的工具.

# 四 MySql的安装配置和数据库导入导出

## 1 mysql 安装
	sudo apt-get install mysql-server

## 2 字符编码配置
	mysql -u root -p

进入mysql管理界面输入下面代码查询
	show variables like "char%";

发现有很多的编码格式是latin1
因为mysql版本的问题,mysql设置默认字符的方法也不尽相同,在mysql5.5上修改如下
编辑`/etc/mysql/my.conf`:

	[mysqld]
	+>character-set-server=utf8
	+>collation-server=utf8_general_ci

重启

	$sudo service mysql restart

当然还有一点就是在`create` database和table的时候在最后加上charset:utf8

# 3 数据导入导出
导出:

	mysqldump -u root -p database [tables] > my.sql

导入

	mysql -u root -p #进入数据库
	create database database-name;#新建数据库
	use database-name;#选择数据库
	source my.sql; #导入
