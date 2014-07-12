---
layout: post
title: mysqldump出现的小问题
category : skill
tags : [mysql]
---

mysqldump的sql文件在导入的时候出现： unkonwn commond "\n", unkonwn commond '',一开始以为是linux和windown的换行符导致的（导出是在linux，导入是在window下）

还是搜索给力，关键词“MySQL导入时出现unknown command的解决方案”，导入的时候加上--default-character-set=utf8

这与mysql的配置有关：可以通过 show variables like 'char%'

