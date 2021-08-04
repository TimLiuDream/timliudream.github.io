---
title: "golang文件相对路径问题"
date: 2020-09-26T17:34:48+08:00
draft: false
---

**目录结构：**

```go
--simple
    --data
          --data.json
    --search
          --feed.go
```

具体代码：

```go
const dataFile = "../data/data.json"
_, filename, _, _ := runtime.Caller(1)
datapath := path.Join(path.Dir(filename), dataFile)
fmt.Println(datapath)
//打开文件
file, err := os.Open(datapath)
if err != nil {
   return nil, err
}
//当函数return时，关闭文件
defer file.Close()
```

如果不要中间获取源文件的路径：

```go
_, filename, _, _ := runtime.Caller(1)
```

直接打开文件的话，是会报错的，会报找不到的错误， 所以只能先获取当前源文件的路径再join数据文件