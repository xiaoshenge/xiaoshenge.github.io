---
layout: post
title: html doctype
category : skill
tags : [html]
---

之前有遇到没有写doctype发现写的样式在浏览器下面表示不同，今天看到有同学写的blog提到的<!DOCTYPE html>简写模式，一起注意过豆瓣的没有仔细研究，以后用简写模式吧。

[有关Doctype](http://hi.baidu.com/cuttinger/blog/item/3173c89525c9b46ad1135e88.html)

[HTML5标准学习 – DOCTYPE](http://www.cnblogs.com/GrayZhang/archive/2011/03/31/learning-html5-doctype.html)


1）在任何html文档中，文档的首部都增加doctype声明，否则，因为浏览器的不同，样式的行为是很难预期的；

2）建议使用标准模式的doctype声明， 

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

或者

<!DOCTYPE html>

后者是前者的简写形式。