---
title: "golang中数组与切片的区别"
date: 2020-09-24T21:39:11+08:00
draft: false
---

**初始化：数组需要指定大小，不指定也会根据初始化的自动推算出大小，不可改变**

数组：

```go
a := [...]int{1,2,3}   
a := [3]int{1,2,3}
```

切片：

```go
a:= []int{1,2,3}  
a := make([]int, 5) 
a := make([]int, 5, 10)
```

**slice的数据结构：**

go源码slice的数据结构定义：

```go
type slice struct {
    array unsafe.Pointer
    len   int
    cap   int
}
```

一个指向真实 array 地址的指针 ptr ，slice 的长度 len 和容量 cap


**函数传递：数组需要明确指定大小，切片不需要。数组是值传递，切片是地址传递**

```go
numbers2 := [...]int{1, 2, 3, 4, 5, 6}
maxIndex2 := len(numbers2) - 1
for i, e := range numbers2 {
   if i == maxIndex2 {
      numbers2[0] += e
   } else {
      numbers2[i+1] += e
   }
}
fmt.Println(numbers2)

numbers3 := []int{1, 2, 3, 4, 5, 6}
maxIndex3 := len(numbers3) - 1
for i, e := range numbers3 {
   if i == maxIndex3 {
      numbers3[0] += e
   } else {
      numbers3[i+1] += e
   }
}
fmt.Println(numbers3)
```

输出：

```go
[7 3 5 7 9 11]
[22 3 6 10 15 21]
```

**观察slice append的时候内存地址会不会改变：**

通过一个例子：

```go
package main

import (
   "fmt"
   "unsafe"
)

func main() {
   //说先定义一个切片，只限定长度为1
   s := make([]int, 1)

   //打印出slice的长度，容量以及内存地址
   fmt.Printf("len :%d cap:%d array ptr :%v \n", len(s), cap(s), *(*unsafe.Pointer)(unsafe.Pointer(&s)))

   for i := 0; i < 5; i++ {
      s = append(s, i)
      fmt.Printf("len :%d cap:%d array ptr :%v \n", len(s), cap(s), *(*unsafe.Pointer)(unsafe.Pointer(&s)))
   }
   //打印出slice
   fmt.Println("array:", s)
}
```

输出：

```go
len :1 cap:1 array ptr :0xc042062080 
len :2 cap:2 array ptr :0xc0420620c0 
len :3 cap:4 array ptr :0xc0420600e0 
len :4 cap:4 array ptr :0xc0420600e0 
len :5 cap:8 array ptr :0xc0420880c0 
len :6 cap:8 array ptr :0xc0420880c0 
array: [0 0 1 2 3 4]
```

可以看出来在append的过程中，内存地址有些是一样的，有些是不一样的，容量也是如此

看出来了吧，每次cap改变的时候指向array内存的指针都在变化。当在使用 append 的时候，如果 cap==len 了这个时候就会新开辟一块更大内存，然后把之前的数据复制过去。

1. 而cap是以乘以2的速度扩展的。这里是不是真的就是乘以2的速度呢？？
2. 最后一句输出slice元素的时候为什么会多了一个0呢？？

对于问题2：是因为make初始化时，都会初始化成对应数据类型的原值

我们再来测试一下问题1：

```go
package main

import (
    "fmt"
    "unsafe"
)

func main() {
    //说先定义一个切片，只限定长度为1
    s := make([]int, 1)

    //打印出slice的长度，容量以及内存地址
    fmt.Printf("len :%d cap:%d array ptr :%v \n", len(s), cap(s), *(*unsafe.Pointer)(unsafe.Pointer(&s)))

    for i := 1; i < 1024*2; i++ {
        s = append(s, i)
        fmt.Printf("len :%d cap:%d array ptr :%v \n", len(s), cap(s), *(*unsafe.Pointer)(unsafe.Pointer(&s)))
    }
    //打印出slice
    fmt.Println("array:", s)
}
```

输出太多了，就截一部分：

![1455942-20181107201151047-786508773.png](https://ae01.alicdn.com/kf/H53e3e5ba2ef04593ab81f1461086a13bf.png)

如果按照上面得出的结论，那cap为1024的下一个应该是2048，但是却是1280

实际go在append的时候放大cap是有规律的。在 cap 小于1024的情况下是每次扩大到 2 * cap ，当大于1024之后就每次扩大到 1.25 * cap 。