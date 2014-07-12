---
layout: post
title: PHP常用header
category : skill
tags : [PHP]
---
PHP is not limited to outputting html. PHP can output images, pdf, javascript files, etc. Browsers determine what type of content is by analyzing the headers sent. In this tutorial I will present you with some examples of how to send headers. To send PHP header use the function header(). You must call this function before any output. Use the function headers_sent() to check whether the headers have been sent and output started. 

301 moved permanently (redirect):
 
    <?php 
    header('HTTP/1.1 301 Moved Permanently');
    header('Location: http://www.example.com');
    die();
    ?> 

302 moved temporarily(redirect):
 
    <?php 
    header('Location: http://www.example.com');
    die();
    ?> 

404 Page Not Found: 

    <?php 
    header('HTTP/1.1 404 Not Found');
    ?> 

Service not avaliable: 

    <?php 
    header('HTTP/1.1 503 Service Temporarily Unavailable');
    header('Status: 503 Service Temporarily Unavailable');
    header('Retry-After: 60');
    ?> 

CSS: 

    <?php
    header('Content-Type: text/css');
    ?> 

Javascript header: 

    <?php 
    header('Content-Type: application/javascript');
    ?> 

Images:

    For JPEG(jpg): 
    <?php 
    header('Content-Type: image/jpeg');
    ?> 
    For PNG: 
    <?php 
    header('Content-Type: image/png');
    ?> 
    For BMP: 
    <?php 
    header('Content-Type: image/bmp');
    ?> 

PDF (output pdf with php): 

    <?php 
    header('Content-Type: application/pdf');
    echo file_get_contents('filename.pdf');
    ?> 

Cache (force browsers not to cache files): 

    <?php 
    header('Expires: Sat, 26 Jul 1997 05:00:00 GMT');
    header('Cache-Control: no-store, no-cache, must-revalidate');
    header('Cache-Control: pre-check=0, post-check=0, max-age=0');
    header ('Pragma: no-cache'); 
    ?> 

Download dialog: 

    <?php 
    header('Content-Disposition: attachment; filename=' . urlencode($f));   
    header('Content-Type: application/force-download');
    header('Content-Type: application/octet-stream');
    header('Content-Type: application/download');
    header('Content-Description: File Transfer');            
    header('Content-Length: ' . filesize($f));
    echo file_get_contents($f);
    ?> 

Authentication (force the browser to pop up a Username/Password input window) - only available when PHP is running as an Apache module: 

    <?php
    if (!isset($_SERVER['PHP_AUTH_USER'])) {
        header('WWW-Authenticate: Basic realm="The Realm"');
        header('HTTP/1.0 401 Unauthorized');
        echo 'If cancel is pressed this text shows';
        die();
    } else {
    //always escape your data//
    $user='user';
    $pass='pass';
       if($_SERVER['PHP_AUTH_USER']==$user && $_SERVER['PHP_AUTH_PW']==$pass){
        echo 'Authorized';
    }
    }
    ?>
 
php header编码
 
header("Content-type: text/html; charset=utf-8"); 

[参考](http://phpweby.com/tutorials/php/35)

