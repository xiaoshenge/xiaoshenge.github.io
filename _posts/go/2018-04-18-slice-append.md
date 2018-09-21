---
layout: post
title: slice append
category : skill
tags : [go]
---

首先我们看一段代码：
```
package main

import "fmt"

func create(iterations int) []int {
	a := make([]int, 0)
	for i := 0; i < iterations; i++ {
		a = append(a, i)
	}
	return a
}

func main() {
	sliceFromLoop()
	sliceFromLiteral()

}

func sliceFromLoop() {
	fmt.Printf("** NOT working as expected: **\n\n")
	i := create(11)
	fmt.Println("initial slice: ", i)
	j := append(i, 100)
	g := append(i, 101)
	h := append(i, 102)
	fmt.Printf("i: %v\nj: %v\ng: %v\nh:%v\n", i, j, g, h)
}

func sliceFromLiteral() {
	fmt.Printf("\n\n** working as expected: **\n")
	i := []int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10}
	fmt.Println("initial slice: ", i)
	j := append(i, 100)
	g := append(i, 101)
	h := append(i, 102)
	fmt.Printf("i: %v\nj: %v\ng: %v\nh:%v\n", i, j, g, h)
}
```
运行结果如下：
```
initial slice:  [0 1 2 3 4 5 6 7 8 9 10]
i: [0 1 2 3 4 5 6 7 8 9 10]
j: [0 1 2 3 4 5 6 7 8 9 10 102]
g: [0 1 2 3 4 5 6 7 8 9 10 102]
h:[0 1 2 3 4 5 6 7 8 9 10 102]


** working as expected: **
initial slice:  [0 1 2 3 4 5 6 7 8 9 10]
i: [0 1 2 3 4 5 6 7 8 9 10]
j: [0 1 2 3 4 5 6 7 8 9 10 100]
g: [0 1 2 3 4 5 6 7 8 9 10 101]
h:[0 1 2 3 4 5 6 7 8 9 10 102]
```
作为初学者我们都大致知道go的数组和切片都是类似的概念，append(a, i)会扩充切片a的容量，并且把i追加到切片的最后，但是为什么上面的代码jgh相同的操作得到结果不一样呢？
我们把代码加上调试信息：
```
package main

import "fmt"

func create(iterations int) []int {
	a := make([]int, 0)
	for i := 0; i < iterations; i++ {
		a = append(a, i)
		//fmt.Println("cap(a) = ", cap(a), "ptr(i) = ", &a[0])
	}
	return a
}

func main() {
	sliceFromLoop()
	sliceFromLiteral()
}

func sliceFromLoop() {
	fmt.Printf("** NOT working as expected: **\n\n")
	a := create(11)
	fmt.Println("initial slice: ", a)
	j := append(a, 100)
	g := append(a, 101)
	h := append(a, 102)
	fmt.Printf("a: %v\nj: %v\ng: %v\nh:%v\n", a, j, g, h)
	fmt.Println("cap(a) = ", cap(a), "ptr(i) = ", &a[0])
	fmt.Println("cap(j) = ", cap(j), "ptr(j) = ", &j[0])
	fmt.Println("cap(g) = ", cap(g), "ptr(g) = ", &g[0])
	fmt.Println("cap(h) = ", cap(h), "ptr(h) = ", &h[0])
}

func sliceFromLiteral() {
	fmt.Printf("\n\n** working as expected: **\n")
	i := []int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10}
	fmt.Println("initial slice: ", i)
	j := append(i, 100)
	g := append(i, 101)
	h := append(i, 102)
	fmt.Printf("i: %v\nj: %v\ng: %v\nh:%v\n", i, j, g, h)

	fmt.Println("cap(i) = ", cap(i), "ptr(i) = ", &i[0])
	fmt.Println("cap(j) = ", cap(j), "ptr(j) = ", &j[0])
	fmt.Println("cap(g) = ", cap(g), "ptr(g) = ", &g[0])
	fmt.Println("cap(h) = ", cap(h), "ptr(h) = ", &h[0])
}
```
运行结果如下：
```
** NOT working as expected: **

cap(a) =  1 ptr(i) =  0xc42001c060
cap(a) =  2 ptr(i) =  0xc42001c070
cap(a) =  4 ptr(i) =  0xc420016120
cap(a) =  4 ptr(i) =  0xc420016120
cap(a) =  8 ptr(i) =  0xc420018100
cap(a) =  8 ptr(i) =  0xc420018100
cap(a) =  8 ptr(i) =  0xc420018100
cap(a) =  8 ptr(i) =  0xc420018100
cap(a) =  16 ptr(i) =  0xc42008c000
cap(a) =  16 ptr(i) =  0xc42008c000
cap(a) =  16 ptr(i) =  0xc42008c000
initial slice:  [0 1 2 3 4 5 6 7 8 9 10]
a: [0 1 2 3 4 5 6 7 8 9 10]
j: [0 1 2 3 4 5 6 7 8 9 10 102]
g: [0 1 2 3 4 5 6 7 8 9 10 102]
h:[0 1 2 3 4 5 6 7 8 9 10 102]
cap(a) =  16 ptr(i) =  0xc42008c000
cap(j) =  16 ptr(j) =  0xc42008c000
cap(g) =  16 ptr(g) =  0xc42008c000
cap(h) =  16 ptr(h) =  0xc42008c000


** working as expected: **
initial slice:  [0 1 2 3 4 5 6 7 8 9 10]
i: [0 1 2 3 4 5 6 7 8 9 10]
j: [0 1 2 3 4 5 6 7 8 9 10 100]
g: [0 1 2 3 4 5 6 7 8 9 10 101]
h:[0 1 2 3 4 5 6 7 8 9 10 102]
cap(i) =  11 ptr(i) =  0xc420078060
cap(j) =  22 ptr(j) =  0xc42008e000
cap(g) =  22 ptr(g) =  0xc42008e0b0
cap(h) =  22 ptr(h) =  0xc42008e160
```

1. 首先我要注意到2种初始化切片的方式导致a的容量是16， i的容量是11
2. 切片内部是由:指针(指向首地址)，长度，容量3个元素组成
3. sliceFromLoop 中 a的长度是11，容量是16，所以向a中append的时候不会扩容, jgh的操作后得到切片还是只向a的内存块，但是a的长度没变，所以jgh都是覆盖a的第12个元素
4. sliceFromLiteral 由于 i的长度是11，容量是11，所以在jhg的appen操作要扩容是都会产生新的内存块，相当于jgh都是指向不同的内存块


```golang
func main() {
	var s []int
	for i := 1; i <= 3; i++ {
		s = append(s, i)
	}
	j := append(s, 100)
	m := append(s, 101)

	fmt.Println(j)
	fmt.Println(m)
}

[1 2 3 101]
[1 2 3 101]
```
这里s的cap=4，len=3, 当我们2次操作append的时候发现s都需要扩容，所以都还是执向s，所以j的值就被m覆盖了。

参考资料：

[Golang slice append gotcha](https://medium.com/@Jarema./golang-slice-append-gotcha-e9020ff37374)

[Golang中Slice的append详解](https://segmentfault.com/a/1190000011016431)

[Go 切片：用法和本质](https://blog.go-zh.org/go-slices-usage-and-internals)

[Why are slices sometimes altered when passed by value in Go?](https://www.calhoun.io/why-are-slices-sometimes-altered-when-passed-by-value-in-go/)