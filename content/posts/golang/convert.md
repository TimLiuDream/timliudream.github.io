---
title: "golang的一些基础数据类型转换"
date: 2020-09-24T21:34:55+08:00
draft: false
---

1. int -- string

```go
//string TO int
value_int,err:=strconv.Atoi(string)
//int TO string
str:=strconv.Itoa(value_int)
```

2. int64 -- string

```go
//string TO int64
value_int64, err := strconv.ParseInt(string, 10, 64)
//int64 TO string,需注意下面转换规定
//FormatInt returns the string representation of i in the given base, for 2 <= base <= 36.
//The result uses the lower-case letters 'a' to 'z' for digit values >= 10
str:=strconv.FormatInt(value_int64,10)//FormatInt第二个参数表示进制，10表示十进制。
```

3. float -- string

```go
//float TO string
v := 3.1415926535
s1 := strconv.FormatFloat(v, 'E', -1, 32)//float32s2 := strconv.FormatFloat(v, 'E', -1, 64)//float64
//第二个参数可选'f'/'e'/'E'等，含义如下：
// 'b' (-ddddp±ddd，二进制指数)
// 'e' (-d.dddde±dd，十进制指数)
// 'E' (-d.ddddE±dd，十进制指数)
// 'f' (-ddd.dddd，没有指数)
// 'g' ('e':大指数，'f':其它情况)
// 'G' ('E':大指数，'f':其它情况)
 
//string TO float
s := "3.1415926535"
v1, err := strconv.ParseFloat(v, 32)
v2, err := strconv.ParseFloat(v, 64)
```

4. float -- int

```go
//这个就很简单了
var a int64
a = 1
var b float64
b = 2.000
 
//a -- float64
c := float64(a)
 
//b -- int64
d := int64(b)
```