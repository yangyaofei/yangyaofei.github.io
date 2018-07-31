---
layout	: post
title	: mysql上关于自增键的一些东西
date	: 2016-05-13
author	: yangyaofei
tags	:
    - mysql
    - database
---
> 下面的东西都不是原创,只是正好需要,搜到之后使用没有问题,或者自己修正过的


### 1 自增乱掉重排

Mysql自增键ID号乱了有时候会因为锁键等等的问题,导致不连续,所以需要重排

原理：删除原有的自增ID，重新建立新的自增ID。

#### 1，删除原有主键：

	ALTER TABLE `table_name` DROP `id`;

#### 2，添加新主键字段：

	ALTER TABLE `table_name` ADD `id` int NOT NULL FIRST;

#### 3，设置新主键：

	ALTER TABLE `table_name` MODIFY COLUMN `id` int NOT NULL AUTO_INCREMENT,ADD PRIMARY KEY(id);

### 2设定自定开始的数字
有时候需要自增设置为0活着我别的数字,设置的值大于现在自增的初始值的话可以自动对其重排,如果小于的话,只能利用第一项里面的方法重置了

	ALTER TABLE table_name AUTO_INCREMENT = 1;

	
