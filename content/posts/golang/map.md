---
title: "map的使用注意事项"
date: 2020-09-26T17:45:03+08:00
draft: false
---

- map是无序的，每次打印出来的map都会不一样，它不能通过index获取，而必须通过key获取
- map的长度是不固定的，也就是和slice一样，也是一种引用类型
- 内置的len函数同样适用于map，返回map拥有的key的数量
- map的值可以很方便的修改，通过numbers["one"]=11可以很容易的把key为one的字典值改为11
- map和其他基本型别不同，它不是thread-safe，在多个go-routine存取时，必须使用mutex lock机制

最后一点需要注意： 字典不是线程安全的，在多个线程操作字典的时候，需要使用到互斥锁

**判断map中是否含有某个key**

```go
v,ok:=map[key]
if ok{
    ...
}
```

**map的查询效率(时间复杂度)（需要很懂数据结构，暂不深究）**

golang中的map底层是hashMap数据结构，那么：

- 插入:O(1)，最坏情况O(N)。
- 查看:O(1)，最坏情况O(N)。
- 删除:O(1)，最坏情况O(N)。

C++中的map采用的是红黑树实现，红黑树是平衡二叉树的一种。不同操作的时间复杂度近似为:

- 插入: O(logN)
- 查看:O(logN)
- 删除:O(logN)

**map与slice的速度比较**

map的时间复杂度是O(1)，slice的时间复杂度是O(n) 是不是map就一定比slice快呢？

```go
var testTimeSlice=[]string{"aa","bb","cc","dd","ee","aa","zz"}

var testTimeMap = map[string]bool{"aa": true, "bb": true, "cc": true, "dd": true, "ee": true, "ff": true, "zz": true}

//以上为第一组查询测试数据


var testTimeSlice2=[] string{"aa","bb","cc","dd","ee","aa","aa","bb","cc","dd","ee","aa","aa","bb","cc","dd","ee","aa","aa","bb","cc","dd","ee","aa","i","j", "l", "m", "n", "o", "p", "q", "k", "x", "y", "z",
    "1", "2", "3", "4", "5", "6", "7", "8", "9", "10","zz"}

var testTimeMap2 = map[string]bool{"aa": true, "bb": true, "cc": true, "dd": true, "ee": true, "ff": true, "qq": true,"ww": true, "rr": true, "tt": true, "zz": true, "uu": true, "ii": true,"oo": true, "pp": true, "lk": true, "kl": true, "jk": true, "kj": true,"hl": true, "lh": true, "fg": true, "gfdd": true, "df": true, "fd": true,
    "i": true, "j": true, "l": true, "m": true, "n": true, "o": true, "p": true, "q": true, "k": true, "x": true, "y": true, "z": true,
    "1": true, "2": true, "3": true, "4": true, "5": true, "6": true, "7": true, "8": true, "9": true, "10": true}

//以上为第二组查询测试数据


func testSlice(a []string)  {
    now:=time.Now()

    for j:=0; j < 100000; j++{
        for _,v:=range a{
            if v=="zz"{
                break
            }
        }
    }
    finish:=time.Since(now)
    fmt.Println(finish1)
}


func testMap(a map[string]bool) {
    now:=time.Now()
    for j:=0; j < 100000; j++{
        if _, ok := a["zz"]; ok {
            continue
        }
    }
    finish2:=time.Since(now)
    fmt.Println("!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!")
    fmt.Println(finish2)
    fmt.Println("!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!")
}
```

当用第一组测试数据时，结果如下：

```go
1.0011ms   //Slice查询耗时
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
6.0038ms   //Map查询耗时
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
```

用第二组测试数据时，结果如下：

```go
8.0038ms    //Slice查询耗时
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
2.0019ms    //Map查询耗时
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
```

现在可以得出结论了，当数据量小的时候，slice的查询速度是比map快的，为什么呢，因为golang的map底层是用hash实现的，既然有hash，那就要做映射，那就有hash函数，这个hash函数的开销千万不要忘记，不要一看到map就只记着O(1)