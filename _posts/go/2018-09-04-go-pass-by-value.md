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


下面一段代码
``` golang
package main

import (
	"unsafe"
	"fmt"
)
var arr1 = []int{1,2}
func main()  {
	arr := []int{1,2,3}
	fmt.Println(unsafe.Pointer(&arr))
	test(arr)
	test4(&arr)
	fmt.Println(arr)
	// 由于arr是slice，参数传递的是指针，对arr进行test操作后，应该是[2,3],但实际还是[1,2,3]
	// 理解：slice里面具体里面的内容是指针，slice还是值传递, test函数里面的arr是个指拷贝，截取操作改变的是拷贝，对原来的slcie不影响
	// 但是对slice里面原始的影响会传递给上层的arr

	test2(arr)
	test(arr)
	fmt.Println(arr)
	// 对slice里面的具体指还是影响到了，所以是[0,2,3]

	test3(arr)
	fmt.Println(arr)
	fmt.Println(arr1)
}
func test(arr []int)  {
	fmt.Println(unsafe.Pointer(&arr))
	arr = arr[1:]
	fmt.Println(arr)
}

func test2(arr []int)  {
	arr[0] = 0
}

func test3(arr []int)  {
	copy(arr1, arr[1:])
}
func test4(arr *[]int)  {
	fmt.Println(unsafe.Pointer(arr))
}
```
```
```

参考：

[httprouter框架 (Gin使用的路由框架)](http://www.okyes.me/2016/05/08/httprouter.html)

[Go的函数参数总是传值](https://colobu.com/2016/10/28/When-are-function-parameters-passed-by-value/)