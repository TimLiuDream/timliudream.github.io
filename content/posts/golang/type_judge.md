---
title: "golang类型判断"
date: 2020-09-26T17:39:49+08:00
draft: false
---

```go
_.ok:=interface{}(a).(B)
```

此语句用于判断对象a是否是B类型

也可以判断对象a是否实现了B接口

```go
package main

import "fmt"

type Pet interface {
   SetName(name string)
   Name()string
   Category()string
}

type Dog struct {
   name string
}

func (dog *Dog) SetName(name string) {
   dog.name = name
}

func(dog Dog) Name()string{
   return dog.name
}

func (dog Dog)Category()string{
   return "dog"
}

func main() {
   dog:=Dog{"little pig"}

   _,ok:=interface{}(dog).(Pet)
   fmt.Printf("Dog implements interface Pet: %v\n", ok)
   _, ok = interface{}(&dog).(Pet)
   fmt.Printf("*Dog implements interface Pet: %v\n", ok)
   fmt.Println()

   var pet Pet = &dog
   fmt.Printf("This pet is a %s, the name is %q.\n",
      pet.Category(), pet.Name())
}

```

输出为：

```go
Dog implements interface Pet: false
*Dog implements interface Pet: true

This pet is a dog, the name is "little pig".
```

一个指针类型实现了某某接口类型，但它的基本类型却不一定能够作为该接口的实现类型。

怎样判定一个数据类型的某一个方法实现的就是某个接口类型中的某个方法呢？
这有两个充分必要条件，一个是“两个方法的签名需要完全一致”，另一个是“两个方法的名称要一模一样”。显然，这比判断一个函数是否实现了某个函数类型要更加严格一些。

这就意味着，Dog类型本身的方法集合中只包含了 2 个方法， 也就是所有的值方法。而它的指针类型*Dog方法集合却包含了 3 个方法