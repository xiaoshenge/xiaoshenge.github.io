---
layout: post
title: 使用vim作为php编码工具
category : PHP
tags : [PHP, vim]
---

####vim配置（.vimrc）

	set nu #显示行数

	set colorscheme desert #配色方案：desert（你可以选择你喜欢的方案）

	set sw=4 #自动缩进的时候， 缩进尺寸为 4 个空格。

	set ts=4 #Tab 宽度为 4 个字符。

	set et #编辑时将所有 Tab 替换为空格

	set fileencoding=utf-8 #字符编码

	set nobackup #没有备份

	set nobomb #BOM是“Byte Order Mark”的缩写，用于标记文件的编码。并不是所有的文本编辑工具都能识别BOM标记

	set tags=~/.vim/tags/phptags,~/.vim/tags/cimvc #ctags用于函数间跳转，使用 Ctrl+] 就可以自动跳转至定义，Ctrl+t 可以返回上一次查看位置。


####生成项目tags：
1.安装exuberant-ctags

2.执行脚本(http://mwop.net/blog/134-exuberant-ctags-with-PHP-in-Vim.html)

	!/bin/bash

	cd /path/to/framework/library

	exec ctags-exuberant -f ~/.vim/mytags/framework \

	-h \".php\" -R \

	--exclude=\"\.svn\" \

	--totals=yes \

	--tag-relative=yes \

	--PHP-kinds=+cf \

	--regex-PHP='/abstract class ([^ ]*)/\1/c/' \

	--regex-PHP='/interface ([^ ]*)/\1/c/' \

	--regex-PHP='/(public |static |abstract |protected |private )+function ([^ (]*)/\2/f/'
	
##[语法检查](http://www.ooso.net/archives/197)##	
php的语法检查功能：

	map <F5> :!/usr/bin/php -l %<CR> f5帮助你检查php的语法错误
	
vim的语法美化功能也很方便,只要在程序的第一行,command模式下输入=:1,$,就可以把当前代码排列的井然有序.

##TagList WinManager##
	"add TagList
	let Tlist_Show_One_File=1
	let Tlist_OnlyWindow=1
	let Tlist_Use_Right_Window=0
	let Tlist_Sort_Type='name'
	let Tlist_Exit_OnlyWindow=1
	let Tlist_Show_Menu=1
	let Tlist_Max_Submenu_Items=10
	let Tlist_Max_Tag_length=20
	let Tlist_Use_SingleClick=0
	let Tlist_Auto_Open=0
	let Tlist_Close_On_Select=0
	let Tlist_File_Fold_Auto_Close=1
	let Tlist_GainFocus_On_ToggleOpen=0
	let Tlist_Process_File_Always=1
	let Tlist_WinHeight=10
	let Tlist_WinWidth=18
	let Tlist_Use_Horiz_Window=0
	filetype plugin on
	let g:winManagerWindowLayout='FileExplorer|TagList'
	nmap wm :WMToggle<cr>
##迁移到mac下报错

>Taglist: Failed to generate tags for /my/path/to/file
>ctags: illegal option -- -^@usage: ctags [-BFadtuwvx] [-f tagsfile] file ...

在talglist的[faq](http://vim-taglist.sourceforge.net/faq.html)上找到了答案,原来taglist只支持exuberant ctags tool,不支持GNU　ctags或UNIX ctags,mac下自带的不是exuberant　ctags,所以就会有问题了,解决办法也很简单,下载[exuberant ctags tool](http://ctags.sourceforge.net/),然后装在一个与系统自带的ctags不冲突的路径下,然后在.vimrc里加一行let Tlist_Ctags_Cmd = '/path/to/ctags'就可以了.

##自动补全
1.下载词典[http://svn.php.net/viewvc/phpdoc/doc-base/trunk/funclist.txt](http://svn.php.net/viewvc/phpdoc/doc-base/trunk/funclist.txt)

2.设置vimrc

	”只有在是PHP文件时，才启用PHP补全(ctrl+p)
	au FileType php call AddPHPFuncList()
	function AddPHPFuncList()
    	set dictionary-=~/.vim/php_funclist.txt dictionary+=~/.vim/php_funclist.txt
    	set complete-=k complete+=k
	endfunction



##备注：
[php syntax](http://www.vim.org/scripts/script.php?script_id=2874)

[将PHP Manual融入(g)Vim](http://www.laruence.com/2010/08/18/1718.html)

[VIM for (PHP) Programmers](http://www.slideshare.net/ZendCon/vim-for-php-programmers-presentation)
[php自动补全](http://randomclan.blog.163.com/blog/static/1453009820121015111325147/)

[简明 Vim 练级攻略](http://coolshell.cn/articles/5426.html)

