---
layout: post
title: Heartbeat nginx
category : skill
tags : [heartbeat]
---

##安装##

系统：Red Hat Enterprise Linux Server release 6.4 (Santiago)
http://hg.linux-ha.org/

1.添加账号

	groupadd -g 5000 haclient
	useradd -u 5000 -g haclient hacluster


2.[glue](http://hg.linux-ha.org/glue/archive/tip.tar.gz) 

	./autogen.sh 
	./configure --prefix=/usr
	make
	make install
	
遇到的问题：
指定prefix到/usr ，如果不指定后面安装其他的组件的时候导致和其他组件目录冲突（导致组件安装不到同一个目录，比如/usr/lib64/heartbeat, /usr/local/heartbeat）, 建议prefix是/usr,这样方便后面安装heartbeat不需要指定头文件。

安装过程会遇到依赖问题：缺少asciidoc,下载安装即可(不需指定prefix)。缺少libtool-ltdl，下载libtool安装后还是报错缺少libtool，通过查资料猜测可能是版本问题。然后通过rpm查找系统默认安装的libtool，rpm -qa|grep libtool 发现默认安装的是libtool-2.2.6，然后下载对应的libtool安装后解决。


3.[Cluster Resource Agents](https://github.com/ClusterLabs/resource-agents)
	./autogen.sh 
	./configure --prefix=/usr
	make
	make install
	
4.[Heartbeat 3.0](http://hg.linux-ha.org/heartbeat-STABLE_3_0/archive/tip.tar.gz)

	./bootstrap 
	./configure --prefix=/usr
	make
	make install
	
5. [pacemaker-1.0](https://github.com/ClusterLabs/pacemaker-1.0)
	./autogen.sh 
	./configure --prefix=/usr
	make
	make install
	
遇到的问题：pacemaker依赖：[libqb](https://github.com/ClusterLabs/libqb), [corosync](https://github.com/corosync/corosync)
安装1.0版本原因：github有2个版本，安装pacemake的时候编译不通过，需要删掉pkg-config检查libqb版本的方法才能编译通过，所以就安装了pacemaker1.0版本。

##配置##

/usr/etc/ha.d/ha.cf

	debugfile /var/log/ha-debug  
	logfile /var/log/ha-log   
	logfacility     local0
	crm on  #是否开启Cluster Resource Manager（集群资源管理）功能。  
	bcast eth0 #指明心跳使用以太网广播方式，并且是在eth1接口上进行广播。  
	keepalive 2 #指定心跳间隔时间为2秒（即每两秒钟在eth1上发送一次广播）。  
	deadtime 30 #指定备用节点在30秒内没有收到主节点的心跳信号后，则立即接管主节点的服务资源。  
	warntime 10 #指定心跳延迟的时间为十秒。当10秒钟内备份节点不能接收到主节点的心跳信号时，就会往日志中写入一个警告日志，但此时不会切换服务。  
	initdead 120 #在某些系统上，系统启动或重启之后需要经过一段时间网络才能正常工作，该选项用于解决这种情况产生的时间间隔。取值至少为deadtime的两倍。   
	udpport 694 #设置广播通信使用的端口，694为默认使用的端口号。(如果开启了iptables，对应机器要打开UDP的694端口)  
	baud 19200 #设置串行通信的波特率。  
	ucast eth0 192.168.1.2 #采用网卡eth0的udp单播来组织心跳，后面跟的IP地址应为双机对方的IP地址。  (主节点就写从节点的ip， 从节点就写主节点的ip)
	auto_failback on #用来定义当主节点恢复后，是否将服务自动切回，heartbeat的两台主机分别为主节点和备份节点。主节点在正常情况下占用资源并运行所有的服务，遇到故障时把资源交给备份节点并由备份节点运行服务。在该选项设为on的情况下，一旦主节点恢复运行，则自动获取资源并取代备份节点，如果该选项设置为off，那么当主节点恢复后，将变为备份节点，而原来的备份节点成为主节点。  
	node node1 #主节点主机名，可以通过命令“uanme –n”查看。  
	node node2 #备用节点主机名。  
	ping 192.168.12.1 #选择ping的节点，ping 节点选择的越好，HA集群就越强壮，可以选择固定的路由器作为ping节点，但是最好不要选择集群中的成员作为ping节点，ping节点仅仅用来测试网络连接。  
	respawn hacluster /usr/lib64/heartbeat/ipfail
	respawn hacluster /usr/lib64/heartbeat/cibmon -d
	apiauth ipfail gid=haclient uid=hacluster
	apiauth cibmon gid=haclient uid=hacluster

haresource
	#pacemaker cms 需要将其转化为xml /usr/lib64/heartbeat/haresources2cib.py /usr/etc/ha.d/haresources 生成的文件在/usr/var/lib/heartbeat/crm/cib.xml
	redhat6.4_3     10.21.192.238 nginx #主节点名 vip 服务（/etc/init.d/里面要有对应的控制脚本start|stop|status）
authkeys
	#authkeys 权限是600
	auth 1
	1 crc
	
###crm 配置及监控##

监控：crm_mon -Afr

配置: node1,node2的heartbeat启动后：默认配置是node1的nginx挂掉后，crm会自动重启nginx不会迁移到node2，需配置

	/*
	默认：migration-threshold=1000000	fail-count=1
	通过nginx stop， crm_mon --failcounts可以查看
	执行crm configure报错ERROR: You may try the upgrade command， 执行cibadmin --upgrade --force即可解决
	*/
	crm configure rsc_defaults migration-threshold=1
	crm configure rsc_defaults failure-timeout=10s
	
	
##参考##
[linux-ha](http://hg.linux-ha.org/)

[The Linux-HA User’s Guide](http://www.linux-ha.org/doc/users-guide/users-guide.html)

[High Availability 指南](http://www.novell.com/zh-cn/documentation/sle_ha/book_sleha/data/book_sleha.html)

[Heartbeat实现Nginx高可用性(style 1.x) ](http://club.topsage.com/thread-530648-1-1.html)

[Heartbeat实现Nginx高可用性(style 2.x)](http://club.topsage.com/thread-530655-1-1.html)

[Heartbeat3.x应用全攻略之：概念组成及工作原理](http://ixdba.blog.51cto.com/2895551/745228)

[Heartbeat3.x应用全攻略之：安装、配置、维护](http://ixdba.blog.51cto.com/2895551/746271)

[heartbeat + pacemaker实现pg流复制自动切换](http://my.oschina.net/lianshunke/blog/200411)

[Heartbeat3.0.5+pacemaker](http://my.oschina.net/guol/blog/90128)