---
layout	: post
title	: apk反编译,修改,打包,签名去除app对于型号的限制
date	: 2015-07-24
author	: yangyaofei
tags	:
    - 折腾
    - 反编译
    - android
---
# 1 工具

- 反编译:
	- dex2jar:	用来得到jar包,用于查看
	- baksmali:	用来把classes反编译成smali文件,用于修改
- Jar查看:
	- jd-gui
- 重新编译:
	- smali 和上面的baksmali是一个工具集合
- 签名:
	- android SDK中自带的signapk.jar就可以了,但是我没有SDK(删了).所以用Auto-Sign中的jar包和key.pk8,key.x509.pem
- 重中之重:
	- java,以及其环境变量

# 2 反编译

## 1 反编译得到jar

反编译得到jar的主要用途是查看其代码,并不能修改其代码.因为可修改的smali代码接近于机器码可读性很差,所以需要反编译得到这个.如果觉得直接看smali没问题,jar也可以不反编译

直接利用dex2jar中的脚本,linux下和win下都有

	dex2jar.bat app.apk

这样会得到一个jar包,用jd-gui打开便可以查看其代码了

## 2 反编译得到smali

首先解压apk文件,得到其中的classes.dex,然后反编译之

	java -jar baksmali.jar -x classes.dex

这样会在当前目录输出一个out目录,其中就是smali源码

# 3 代码修改

这次我的目的是为了除去app对机器的型号的限制.
查看源代码发现是程序用`android.os.Build.MODEL`获取的机器型号,在上传服务器比较后确定的所以在jar中搜索所有的`android.os.Build`.找到之后,打开对应的smali源码修改为可用的机器型号后保存

# 4 重新编译

使用smail.jar工具

	java -jar smali.jar -o classes.dex out

其中out为源码的文件夹,输出的classes.dex直接替换原来apk文件中的classes.dex即可

# 5 签名

apk会校验自身的,所以需要签名

	java -jar signapk.jar key.x509.pem key.pk8 未签名.apk 签名.apk

然后卸载原来的app,装上这个就好了
