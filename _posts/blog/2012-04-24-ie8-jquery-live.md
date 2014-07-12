---
layout: post
title: jquery.1.4.2的live在IE8下的一个bug
category : JavaScript
tags : [jquery, live]
---

在IE8下，如果给页面有内容是动态加载的，通过live绑定事件，在页面上面的内容要后绑定（如果发现有功能不能工作，就按动态加载后的DOM，顶部的DOM后绑定live事件），不过这个bug在更高版本的jquery下修复了。