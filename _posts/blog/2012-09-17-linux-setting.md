---
layout: post
title: 常用linux设置
category : skill
tags : [linux]
---
####linux密码####
passwd：修改密码
####设置语系####
centos：echo -e "LANG='zh_CN.UTF-8'\nSYSFONT='latarcyrheb-sun16'" > /etc/sysconfig/i18n
####修改环境变量####
/etc/profile里面添加 export PATH="$PATH:/usr/local/git/bin:/usr/local/php/bin"
####linux挂载samba####
mount -t cifs //hostname/Public /var/www/html/1/NAS -o username=xxxx,password=xxxx
[]()

