---
layout	: post
title	: vim下使用syntastic和flake8进行静态语法分析和代码pep8规范
date	: 2016-05-14
author	: yangyaofei
tags	:
    - 折腾
    - vim
    - python
---

>python大法好,但是python也是磨人的小妖精........

用了python半年多了,很好用,想法什么的可以快速的进行实践.但是动态语言的问题就是很多时候只有运行到某个地方,错误才会出现,特别是传值错误什么的.这很让人懊恼......

这个时候你就会想起c++/java的好......

当然在我的不懈努力google下找到了解决这个的方法,就是静态语言分析器PyFlakes,当然也找到了相应的vim插件,google PyFlakes的时候发现还有了flake8,是个集成了PyFlakes语法分析器和pep8规范的包,想必一起用是极好的,就开始了使(bugui)用(lu).

## 1 安装syntastic
接看官方的[guide](https://github.com/scrooloose/syntastic)就清楚了,不多说.
对了,对于vim的那个小配置反正我加了.

## 2 安装flake8
很简单直接用pip安装

	sudo pip -H install flake8

## 3 设置flake8
其实到这里整个环境就已经装完了,打开一个python文件,它们(vim,syntastic啊什么的)就会告诉你你的程序哪里有问题,那里不规范,对着改就好了,然后保存下看看错误和警告还有没有就好了.

但是对于pep8规范中的tab使用我很不喜欢,习惯了用tab缩进,你让我全部改成4space....nonono,我做不到.....所以我就稍微设置一下让他不对tab缩进报错

flake8在文档中有写[这里](http://flake8.pycqa.org/en/latest/config.html#global)他给出了很多歌配置文件,我想让别人看我的项目的时候也知道我用了tab,所以我用的是项目中添加一个文件的方法

##### 1 添加steup.cfg文件
添加在项目根目录

##### 2增加配置
为了让pep8页可以识别到,所以我添加了两个配置

	[pep8]
	ignore = W191
	[flake8]
	ignore = W191

W191就是对应的出错信息的代码

然后就能愉快的使用了0.0
