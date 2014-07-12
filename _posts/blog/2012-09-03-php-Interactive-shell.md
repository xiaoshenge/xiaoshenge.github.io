---
layout: post
title: PHP Interactive shell
category : skill
tags : [PHP]
---

无意中发现在cli模式下php -a可以进入交互脚本，从此成了我调试php的小工具（不必为了调试而写一个文件）。

然而在自己编译安装PHP后发现输入php -a是Interactive mode enabled，没有之前那么方便了，于是对于有强迫症的人要折腾成以前那样。网上搜了一圈没有发现解决方案。

文档中发现了As of PHP 5.1.0, the CLI SAPI provides an interactive shell using the -a option if PHP is compiled with the --with-readline option.

[php interactive shell](http://www.php.net/manual/en/features.commandline.interactive.php)


./configure --prefix=/usr/local/php --enable-fpm --with-curl --with-gd --with-mysql --with-mysqli --with-readline
