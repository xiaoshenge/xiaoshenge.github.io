---
layout: post
title: 利用diff，patch制作补丁文件
category : skill
tags : [linux]
---

####单个文件
diff -u file1 file2 > f1f2.patch

将补丁应用于file1: patch file1 < f1f2.patch

恢复file1之前版本：patch file1 -R < f1f2.patch

####patch文件格式（diff中-u使得差异输出中带有上下文 ）：
diff -u a1/a1.1 a2/a1.1（差异比较的命令）

--- a1/a1.1	2012-04-22 23:32:19.000000000 +0800（-代表文件）

+++ a2/a1.1	2012-04-22 22:48:53.000000000 +0800（+代表副本）

@@ -1 +1,2 @@（@差异标识符，-1 +1,2表示文件的第1行到第2行，副本的第1行到第2行）

 this a1.1（没有差异地方）
 
+add in a2（副本中有的）

####目录（多个文件）
diff -ur dir1 dir2 > d1d2.patch

将补丁应用于dir1：patch -p0 < d1d2.patch

恢复dir1之前的版本： patch -R dir1 < d1d2.patch

patch -p数字：跟d1d2.patch 里面的文件名有关（就patch文件第一行）0代表于要更新的文件在同一目录下：
例如diff -uNr a1/a1.1 a2/a1.1 p0就是要打补丁的文件是a1/a1.1，p1代表要更新的文件时a1.1（即p代表目录层次）


