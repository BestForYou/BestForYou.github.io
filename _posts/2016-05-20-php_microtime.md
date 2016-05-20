---
layout: post
title: 程序执行时间
subtitle: 
author: pizida
date: 2016-05-20 16:07:26 +0800
categories: 
tag: PH杂项
---
#### php记录代码执行时间
```
$t1 = microtime(true);
// ... 执行代码 ...
$t2 = microtime(true);
echo '耗时'.round($t2-$t1,3).'秒';
```

简单说一下. microtime() 如果带个 true 参数, 返回的将是一个浮点类型. 这样 t1 和 t2 得到的就是两个浮点数, 相减之后得到之间的差. 由于浮点的位数很长, 或者说不确定, 所以再用个 round() 取出小数点后 3 位. 这样我们的目的就达到了~