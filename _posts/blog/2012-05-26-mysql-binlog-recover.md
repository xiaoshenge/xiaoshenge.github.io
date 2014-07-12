---
layout: post
title: 通过mysql的binlog恢复误删数据库
category : skill
tags : mysql
---

我承认周六来赶项目有点不爽（领导是给新人锻炼的机会，我信了...），但是误删数据库确实是我不小心的。

由于测试服务器上的mysql数据库没有备份，查了资料还原数据库需要binlog,万幸的是测试机上的binlog是开启的[show variables like 'log_bin'; show binary logs;].

通过mysqlbinlog [mysql-bin.0001],我们可以看到数据的操作,通过此命令导出sql【mysqlbinlog --no-defaults --database="dbname" --stop-datetime="2012-05-26 00:00:00"  /usr/local/mysql/var/mysql-bin.000008 > /home/mysql-restore20120526081.sql】。

然后把导出的sql恢复到数据库。

好吧，保持好心情，去吃个饭回来继续赶项目。