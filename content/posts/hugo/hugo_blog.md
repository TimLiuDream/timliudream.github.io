---
title: "在Windows10系统下通过hugo搭建个人博客"
date: 2020-09-20T18:43:30+08:00
draft: false
---

1. 在[hugo](https://github.com/gohugoio/hugo/releases)官网上下载安装包，选择windows版本

2. 将hugo的安装包解压后，将解压后的路径加入到系统的环境变量PATH中便可运行hugo

3. 打开命令提示符窗口，进入hugo安装文件夹，然后通过hugo version便可查看安装的hugo版本，即hugo安装成功

![微信截图_20200920182233.png](https://ae05.alicdn.com/kf/H3a9de6c626624e9f830742b918c4f188i.png)

4. 通过hugo新建一个文件夹来存放我们之后要放的博客资料，此处在E盘的hugo文件中来新建myblog文件来存放博客资料;进入E:\hugo目录，键入hugo new site myblog来创建myblog文件，点击回车

![微信截图_20200920182429.png](https://ae01.alicdn.com/kf/Hb2711508adc34072bd34a49c4cec4991j.png)

5. 下载一个[博客主题](https://hugothemesfree.com/)，选择一个自己喜欢的主题点击进入，然后找到如下代码部分

6. 假设我选择了[noteworthy](https://github.com/kimcc/hugo-theme-noteworthy)这个主题，只需要在myblog下执行以下命令即可
```
git clone https://github.com/kimcc/hugo-theme-noteworthy.git themes/noteworthy
```

7. 在本地启动个人博客：在myblog根目录下键入hugo server -t noteworthy --buildDrafts：(其中noteworthy为下载的主题名)

```
hugo server -t noteworthy --buildDrafts
```

![微信截图_20200920182828.png](https://ae05.alicdn.com/kf/Hbbe67332b63f412782efdefd07653912y.png)

当出现以上提示，则证明本地启动成功。可以在浏览器中查看http://localhost:1313/，看站点是否起来

![微信截图_20200920182940.png](https://ae04.alicdn.com/kf/H54dcb54d0ed04d779c9a12541ce3e0abA.png)

