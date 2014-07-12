---
layout: post
title: CentOS5 yum error
category : skill
tags : [linux, yum]
---

在CentOS下要使用yum，发现报错：There was a problem importing one of the Python modules required to run yum.

错误原因：错误信息描述为 yum 所依赖的python 不相符，请安装相对应的python即可。

查看python版本：whereis python，执行python：查看使用的python版本，发现两处的版本不一致，而yum调用了高版本的python而环境变量时低版本的python

将/usr/bin/yum中的python改为低版本的：#!/usr/bin/python ===》 #!/usr/bin/python2.4

然后yum可以运行了，接着安装软件的时候，yum找不到版本库。

将/etc/yum.repos.d/CentOS-Base.repo里面的内容注释掉（因为出错的版本库在这里），然后执行yum update。