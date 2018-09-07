---
layout: post
title: Parameters in Go are always passed by value
category : skill
tags : [go]
---

go的参数传递都是值传递，比如参数是指针，传递的是指针的拷贝，基础类型是值得拷贝（int，string，bool，array），指针类型（slice, map, chan）
```golang
package main

import "fmt"

type Node struct {
	name string
	children []*Node
}

func (n *Node)InitChildren(name string)  {
	n.name = name
	child := &Node{}
	n.children = []*Node{child}
	n = child
	n.name = "child"
}

func main() {
	root := &Node{}
	root.InitChildren("root")
	fmt.Println(root)
	for _,v := range root.children{
		fmt.Println(v)
	}
}

```
```
&{root [0xc42007e1b0]}
&{child []}
```
InitChildren 函数的参数n，在函数执行的时候，函数内的n是参数指针的一份拷贝，当改指针执向的值时候，原数据也会变更。但是这时候把n的指针改掉，不会影响原数据。参考： httprouter 的insertChild实现

参考：

[httprouter框架 (Gin使用的路由框架)](http://www.okyes.me/2016/05/08/httprouter.html)

[Go的函数参数总是传值](https://colobu.com/2016/10/28/When-are-function-parameters-passed-by-value/)