---
layout: post
title: linux下ext3格式恢复误删文件
category : skill
tags : linux
---

这次不是我的删掉的文件，但是我却搞丢了innode（别人让我帮忙看一下，结果我在那个目录下面写文件了），导致文件恢复不了。

参考：http://blog.s135.com/linux_ext3_undelete/


      http://easwy.com/blog/archives/undelete-directories-files-on-ext3-filesystem-via-ext3grep/

	  
1.安装ext3grep软件
2.umount 分区（df）
3.查询所有Inode（ ext3grep /dev/sdb1 --ls --inode 2），（通过逐级查找Inode我们可以看到文件操作历史）
4.ext3grep /dev/your-device --restore-file path/to/your/file/filename 恢复文件

如果innode丢失了，就恢复不了（比如对那个目录在删除文件后执行了写操作）
