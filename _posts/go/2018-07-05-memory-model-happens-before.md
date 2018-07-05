---
layout: post
title: memory model happens before
category : skill
tags : [go]
---
```
var a, b int

func f() {
	a = 1
	b = 2
}

func g() {
	print(b)
	print(a)
}

func main() {
	go f()
	g()
}
```
it can happen that g prints 2 and then 0.

参考：https://golang.google.cn/ref/mem#tmp_10

上面的代码一开始我认为的可能结果是：0,0 0,1 2,1，但是怎么也不会是2,0

go的内存操作顺序是Happens Before。如果事件e1在e2之前发生，那么e2在e1之后发生。如果e1没有在e2之前发生，也没有在e2之后发生，那我们说e1和e2同时发生。

happens before 主要是用来保证内存的可见性，原因是没有限制的情况下，编译器和CPU使用的各种优化，会对此造成影响，具体的来说就是操作重排序和CPU CacheLine缓存同步：
* 操作重排序。现代CPU通常是流水线架构，且具有多个核心，这样多条指令就可以同时执行。然而有时候出现一条指令需要等待之前指令的结果，或是其他造成指令执行需要延迟的情况。这个时候可以先执行下一条已经准备好的指令，以尽可能高效的利用CPU。操作重排序可以在两个阶段出现：

1. 编译器指令重排序
2.  CPU乱序执行
* CPU 多核心间独立Cache Line的同步问题。多核CPU通常有自己的一级缓存和二级缓存，访问缓存的数据很快。但是如果缓存没有同步到主存和其他核心的缓存，其他核心读取缓存就会读到过期的数据。

通俗点理解：
```
A
B
C
```
如果A happens before C，B happens before C, 编译器优化的时候，只会保证着这个顺序，但是A,B的顺序就不一定了，可能是ABC,也可能是BAC。所以这个如果是单个gorountine的时候，这个顺序跟代码逻辑一致能保证AB在C之前执行，但是如果多个gorountine情况下，AB的可见性就保证不了，就会出现上面的情况2,0

### Go 中定义的Happens Before保证
#### 单线程
* 在单线程环境下，所有的表达式，按照代码中的先后顺序，具有Happens Before关系

#### Init 函数
* 如果包P1中导入了包P2，则P2中的init函数Happens Before 所有P1中的操作 main函数Happens After 所有的init函数

#### Goroutine
* Goroutine的创建Happens Before所有此Goroutine中的操作
* Goroutine的销毁Happens After所有此Goroutine中的操作

#### Channel
* 对一个元素的send操作Happens Before对应的receive 完成操作
* 对channel的close操作Happens Before receive 端的收到关闭通知操作
* 对于Unbuffered Channel，对一个元素的receive 操作Happens Before对应的send完成操作
* 对于Buffered Channel，假设Channel 的buffer 大小为C，那么对第k个元素的receive操作，Happens Before第k+C个send完成操作。可以看出上一条Unbuffered Channel规则就是这条规则C=0时的特例

#### Lock
* 对于一个Mutex/RWMutex，设n < m，则第n个Unlock操作Happens Before第m个Lock操作。
* 对于一个RWMutex，存在数值n，RLock操作Happens After 第n个UnLock，其对应的RUnLockHappens Before 第n+1个Lock操作。


#### Once
* once.Do中执行的操作，Happens Before 任何一个once.Do调用的返回。

#### happens before是可以传递的
由a happens before b和b happens before c可以得出 a happens before
```
var a string

func f() {
	print(a)//E2
}

func hello() {
	a = "hello, world"//E1
	go f()//G1
}
```
E1 happens before G1, G1 happens before E2, 所以 E1 happens before E2, a的值在E2中是可见的
```
var a string

func hello() {
	go func() {//G1
	    a = "hello"//E1
	}()
	print(a)//E2
}
```
G1 happens before E1, G1 happens before E2, 但是E1,E2的顺序没法保证，所以E2中a的值不一定可见

##
感觉多个gorountine访问全局变量，有点类似多线程访问共享内存，需要通过显示同步手段来保证可见性，所以go推荐channel来通讯，而不是共享内存来通讯。

##参考资料

[The Go Memory Model 中文翻译](https://legacy.gitbook.com/book/damoye/the-go-memory-model/details)

[The Go Memory Model](https://golang.google.cn/ref/mem)

[Go 内存模型和Happens Before关系](https://zhuanlan.zhihu.com/p/29108170)

[Happens before](http://ifeve.com/happens-before/)

[happens-before俗解](http://ifeve.com/easy-happens-before/)