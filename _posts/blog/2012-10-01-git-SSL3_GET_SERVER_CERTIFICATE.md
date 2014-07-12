---
layout: post
title: HOW TO FIX A GIT SSL3_GET_SERVER_CERTIFICATE ERROR
category : skill
tags : [git]
---

git clone https://github.com/xurenlu/acfilter.git

Cloning into 'acfilter'...

error: SSL certificate problem, verify that the CA cert is OK. Details:

error:14090086:SSL routines:SSL3_GET_SERVER_CERTIFICATE:certificate verify failed while accessing https://github.com/xurenlu/acfilter.git/info/refs

fatal: HTTP request failed

a workaround is to tell git to avoid checking SSL certificates:

export GIT_SSL_NO_VERIFY=true

[reference](http://www.uwosh.edu/ploneprojects/docs/how-tos/how-to-fix-a-git-ssl3-get-server-certificate-error)

