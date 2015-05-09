#thrift "hello world"篇

为了对thrift有一个初步的认识，这里从thrift官方tutorial入手，运行thrift的php版本的client和server。我们将用thrift（命令行工具）生成thrift文件对应的客户端和服务器端的lib库，通过python运行一个简单的web server（调用php服务），php（php-cli 命令行）来执行对应的php客户端。

####1. git clone https://github.com/apache/thrift  
>通过git或者其他方式下载thrift源码，其中包行官方的tutorial

####2. thrift -r -gen php:server tutorial.thrift
>进入tutorial目录执行该命令，如果报错例如
No generator named 'haxe' could be found 
，可以先将对应的 tutorial.thrift share.thrift 中的 对应的 namespace haxe tutorial注释掉，
执行成功后会在tutorial目录下面生成gen-php目录以及对应的文件

####3.  python runserver.py
>通过Python启动一个简易的webServer

####4.  php PhpClient.php --http
>通过phpClient.php 调用 phpServer.php

###更换nginx方式运行
#####1. 将phpserver.php放置到nginx可访问的目录
####2.去掉 phpserver.php的第一行的Shebang line
####2.修改phpclent.php访问的phpserver.php的路径以及对应的http服务的host, port,uri