---
layout: post
title: crontab里php使用相对路径的方法
subtitle: 
author: pizida
date: 2016-05-19 14:23:03 +0800
categories: 
tag: PH杂项
---
  
问题：

1. 在crontab里执行php脚本，在php文件里，引入别的文件，并不是以这个php文件自身所在的路径作为相对路径的。 而是以安装的php 的那个可执行文件所在路径作为相对路径的。所以在php脚本中引用相对路径文件都会报文件不存在等问题。    

2. 因为crontab默认在/root下执行，所以php脚本引入相对路径时，会按照crontab执行路径（/root）去include文件，所以就找不到要include的文件了。

解决方案：   

### php脚本中引用的文件都是绝对路径  
因为__DIR__ 和 dirname(__FILE__)是等价的，所以，上面的语句和下面这条语句是一样的：

```
chdir(dirname(__FILE__));   //cd 到php脚本所在的目录  
include ('../inc/common.inc.php'); 

include (__DIR__.'/../inc/common.inc.php');
```

---------------------------------------
### 在crontab中先切换到执行脚本的路径，再执行脚本  
借助shell(假设我的php脚本（my_script.php）在/var/www/my_project 目录下)：

```
#!/bin/bash  
cd /var/www/my_project && php my_script.php >>  
/var/log/my_script.log 2>&1 
```
