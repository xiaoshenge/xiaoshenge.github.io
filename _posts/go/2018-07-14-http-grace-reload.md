---
layout: post
title: go http grace restart
category : skill
tags : [go]
---

HTTP 服务器替换方案 想无缝重启、停机吗? 以下有几种方式：

我们可以使用 fvbock/endless 来替换默认的 ListenAndServe。
```
router := gin.Default()
router.GET("/", handler)
// [...]
endless.ListenAndServe(":4242", router)
```
Go 1.8的发布终于提供了优雅关闭http服务

文档中是这样介绍的：

func (srv *Server) Shutdown(ctx context.Context) error
Shutdown 将无中断的关闭正在活跃的连接，然后平滑的停止服务。处理流程如下：

* 首先关闭所有的监听

* 然后关闭所有的空闲连接

* 然后无限期等待连接处理完毕转为空闲，并关闭

* 如果提供了 带有超时的Context，将在服务关闭前返回 Context的超时错误

需要注意的是，Shutdown 并不尝试关闭或者等待 hijacked连接，如 WebSockets。如果需要的话调用者需要分别处理诸如长连接类型的等待和关闭。

存在一个问题，有了shutdown函数我们还需要处理关闭进程的信号量么？
```
package main

import (
	"net/http"
	"fmt"
	"time"

	"os"
	"context"
	"log"
	"os/signal"
	"syscall"
)
func main() {
	writLog("init")
	http.HandleFunc("/config", configFunc)

	h := &http.Server{
		Addr: ":8122",
		Handler: http.DefaultServeMux,
		ReadTimeout: 3 * time.Second,
		WriteTimeout: 3 * time.Second,
		MaxHeaderBytes: 1 << 20,
	}

	go func() {
		writLog("server start")
		log.Println(h.ListenAndServe())
		writLog("server shutdown")
	}()
	sigChan := make(chan os.Signal)
	signal.Notify(sigChan, syscall.SIGINT, syscall.SIGTERM, syscall.SIGUSR2,syscall.SIGKILL)
	log.Println(<-sigChan)
	shutDown(context.Background(), h)
	writLog("main end")
}

func shutDown(ctx context.Context, h * http.Server)  {
	writLog("shutdown func call")
	h.Shutdown(ctx)
}

func writLog(string string)  {
	f, err := os.OpenFile("/tmp/go.redpack.log", os.O_APPEND | os.O_WRONLY, 0644)
	if err != nil {
		panic(err)
	}
	defer f.Close()

	if _, err = f.WriteString(fmt.Sprintf("%s\n",string)); err != nil {
		panic(err)
	}
	f.Sync()
}

func configFunc(w http.ResponseWriter, req * http.Request)  {
	pid := os.Getpid()
	writLog(fmt.Sprintf("pid: %d", pid))

	fmt.Fprintf(w, "ok")
	writLog("resp: ok")
}

```
执行结果如下：
```
curl http://127.0.0.1:8122/config
ok 
kill -15 57696

pid: 57696
resp: ok
shutdown func call
main end
server shutdown
```

```go
package main

import (
	"net/http"
	"fmt"
	"time"

	"os"
	"context"
)
func main() {
	writLog("init")
	http.HandleFunc("/config", configFunc)

	h := &http.Server{
		Addr: ":8122",
		Handler: http.DefaultServeMux,
		ReadTimeout: 3 * time.Second,
		WriteTimeout: 3 * time.Second,
		MaxHeaderBytes: 1 << 20,
	}
	h.ListenAndServe()
	defer shutDown(context.Background(), h)

	writLog("main end")
}

func shutDown(ctx context.Context, h * http.Server)  {
	writLog("shutdown func call")
	h.Shutdown(ctx)
}

func writLog(string string)  {
	f, err := os.OpenFile("/tmp/go.redpack.log", os.O_APPEND | os.O_WRONLY, 0644)
	if err != nil {
		panic(err)
	}
	defer f.Close()

	if _, err = f.WriteString(fmt.Sprintf("%s\n",string)); err != nil {
		panic(err)
	}
	f.Sync()
}

func configFunc(w http.ResponseWriter, req * http.Request)  {
	pid := os.Getpid()
	writLog(fmt.Sprintf("pid: %d", pid))

	fmt.Fprintf(w, "ok")
	writLog("resp: ok")
}
```
执行结果如下：
```go
curl http://127.0.0.1:8122/config
ok
kill -15 57784

init
pid: 57784
resp: ok
```

这里可以看到第二种写法没有处理信号量，然后调用defer shutdown, 好像shutdown里面函数没有执行。不知道是不是写法有问题，后续在跟进吧。

参考资料

[Go 1.8 http graceful 体验](https://segmentfault.com/a/1190000008383272)

[Golang开发支持平滑升级（优雅重启）的HTTP服务](http://tabalt.net/blog/graceful-http-server-for-golang/)

[go 语言框架 gin 的中文文档](https://github.com/skybebe/gin-doc-cn#install)

