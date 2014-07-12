---
layout: post
title: linux selinux
category : skill
tags : [linux]
---

在centos下使用samb一直都提示位置不对，但是ssh可以连接上就没怀疑是本地限制问题，一直以为是网络问题，导致纠结了老久。
先记录一下selinx：
临时关闭：setenforce 0
永久关闭：/etc/selinux/config SELINUX=disabled
[]()

