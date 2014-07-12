---
layout: post
title: git常用命令
category : skill
tags : [git]
---

[git常用备忘录](http://robbinfan.com/blog/34/git-common-command)

######查看提交历史######
	git log
	git log <file> #查看该文件提交历史记录
	git log -p <file> #查看每次详细修改内容的diff
	git log -p <file> -2 #查看最近2次详细修改内容的diff
	git log --stat #查看提交统计信息

######恢复已删除文件######
	git checkout file #直接rm的文件还没有commit
	git checkout HEAD~1 --file #从上一次提交中恢复file