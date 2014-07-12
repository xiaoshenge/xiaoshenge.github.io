---
layout: post
title: 谁动了我的内存：php内存泄露，系统缓存消耗？
category : skill
tags : [PHP]
---


#####情景：线上图片服务压缩的图片品质（100），缩略图品质（100）占用了很多空间，导致后来又55个文件了（占用空间160G）才发现这个问题。现在需要解决的是把这部分压缩个低品质的缩略图节省空间
（当然在这个硬盘白菜价的时代搞这样的问题没这个必要，我这里讨论的不是节省空间是想找出内存消耗问题）。我用php脚本重新生成缩略图的时候，通过top发现内存消耗一直增加导致后来脚本报错内存不够了，到底谁动了我的内存？

处理代码版本一(php)：

	set_time_limit(0);

	function thumbnailimage($img,$width,$height,$savefile){

		$new_img = imagecreatetruecolor ( $width, $height );
		imagedestroy($new_img);
		
	}
	foreache($list as $v) {

		$img = imagecreatefromjpeg($v);
		thumbnailimage($img,480,300,$savepath);
		imagedestroy($img);
		
	}



这个脚本处理了5千多个的时候，由于内存不够用挂了，然后我改了php.ini里面的memory_limit改成了5G，但是随着脚本的执行，内存也会被消耗殆尽。
于是我就以为是php的内存泄露了，然后就想用其他方案解决，在老王的技术手册里面看到GraphicsMagick这个工具，然后写了个脚本去处理，结果发现top看到的内存消耗还是一直增加，然后经人提示这个应该是系统操作文件（写文件）文件被缓存了消耗了内存，
调整bash脚本，处理一张图片后手动释放一下内存（sync && echo 3 > /proc/sys/vm/drop_caches），然后top看到的内存消耗就正常了。
由于这个bash脚本处理的速度还不如php的gd库处理，然后就换成php处理。
验证php脚本内存消耗的原因：
处理代码版本二（php）：

	set_time_limit(0);

	function thumbnailimage ($img,$width,$height,$savefile) {

		$new_img = imagecreatetruecolor ( $width, $height );
		imagedestroy($new_img);
		
	}
	foreache($list as $v) {

		$memory1=memory_get_usage();
		file_put_contents('memory','memory1:'.$memory1."\n",FILE_APPEND);
		
		$img = imagecreatefromjpeg($v);
		thumbnailimage($img,480,300,$savepath);
		imagedestroy($img);
		
		$memory1=memory_get_usage();
		file_put_contents('memory','memory1:'.$memory1."\n",FILE_APPEND);
		
		system('sync && echo 3 > /proc/sys/vm/drop_caches');
	}



通这个版本处理内存消耗就正常了，当然php进程也要消耗内存，php.ini的memory_limit稍微改大一下。
从这里看来消耗内存的是系统操作文件消耗的，不是php，由于我的无知一开始错怪了PHP。
###出现问题没有仔细分析，只是用道听途说的php内存泄露来解释（简单的测试一下就知道不是PHP内存泄露的问题）。