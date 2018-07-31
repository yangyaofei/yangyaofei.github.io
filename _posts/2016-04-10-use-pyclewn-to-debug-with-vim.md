---
layout	: post
title	: "用pyclewn作为vim后端来debug"
subtitle: "vim 变成 IDE"
date	: 2016-04-10
author	: "yangyaofei"
tags	:
    - vim
    - debug
    - pyclewn
---

>vim很好用,但是debug的时候就没有IDE舒服了,好在有各路有人码了各种轮子来让vim可以像vim那样debug,pyclew就是其中一款
>
其实对于debug,有gdb和pdb这样的工具有时候就足够了,但是最近在用curses这个python库的时候,因为这个库是做一个text-base的UI,pdb根本没法在shell里面输出正常的信息,输出的信息逼近挡住了界面,而且格式什么的都乱掉了.
>
这个时候就很需要一个IDE一样的东西了,索性一步到位,把vim变成IDE

# 1 安装pyclewn
安装并不难,用官方的介绍很详细,但是他涉及到了vimball这个东西,我不是很清除,导致很长时间没有安装上vim的插件.

	# 安装pyclewn库
	sudo -H pip install pyclewn
	# 导出vim所需的插件文件
	python -c "import clewn; clewn.get_vimball()"

这个时候会在当前目录下得到一个`pyclewn-x.x.vmb`的文件这个文件是vim的一个叫vimball插件管理插件的插件包,具体请谷歌吧,然后`vim -S pyclewn-2.2.vmb`一下就好了.如果你有别的插件管理的话,按照相应的文件及自己移动好了就好了.

# 2 使用pyclewn
其实按照文档走的话基本上都是很清楚地.用法和pdb和gdb的命令都基本一样,毕竟当时是因为curses才用这个的,所以具体说一下程序和debugger输出分离的问题.
这个一共有两种方法,一个是正常debug开始之后用`:Cinferiortty`命令打开一个终端,并把当前debug的程序的三个标准输出转移到打开的终端上.一般的程序这样就足够了,也很好,但是curses的程序不行,估计是动了标准输入输出上很多的东西把.

_**curses这类程序需要另一种方法:**_
这种方法的思路是先执行程序然后等待debugger连接,vim在另一个程序中打开,连接要调试的程序,这样就不用转移三个标准输出输出,就解决了上面出现的问题.
### 1 在程序中增加代码

	# 用于等待后台调试器连接
	import clewn.vim as vim
	vim.pdb()

### 2 启动vim连接程序

	vim
	:pyclewn pdb hehehe.py

### 3 注意
这个程序只能用`:Cbreak+行号`这种形式增加断点,如果在代码里面直接加入断点,那么vim和被调试程序将会断开,依然在原程序中出现pdb调试.

# 3 以上没有提到的

[pyclewn doc](http://pyclewn.sourceforge.net/index.html)的文档比较详细,以上只是根据我实际体验,写出我踩到的坑,在其他应用中,按照文档里面写的用就好了.其实以上内用文档里面也有介绍,可能并不是每个人都能注意到吧......

----------

以上
