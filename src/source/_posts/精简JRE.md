---
title: 精简JRE
toc: true
tags: 编程
categories:
  - 编程
  - Java
abbrlink: 55345
date: 2016-07-18 20:34:23
---
## 需求场景

最近手头上的有个项目发布是以jar包来发布运行的，由于部署的服务器很多，不能保证每台机器上都安装同一版本的Java环境。 需要 发布包中内置jre，但是原始的jre太大，所以进行了手动的精简（这个精简包有可能会不能通用你们的项目，原因是不同的项目使用到jre中的class不同 ，精简包主要是将一些没有用到的class删除）。

下面我们看一下jre的目录结构：
```
jre7
 ├─bin
 │  ├─dtplugin
 │  ├─plugin2
 │  └─server
 └─lib
     ├─amd64
     ├─applet
     ├─cmm
     ├─deploy
     ├─ext
     ├─fonts
     ├─images
     │  └─cursors
     ├─management
     ├─security
     ├─servicetag
     └─zi
         ├─Africa
         ├─America
         │  ├─Argentina
         │  ├─Indiana
         │  ├─Kentucky
         │  └─North_Dakota
         ├─Antarctica
         ├─Asia
         ├─Atlantic
         ├─Australia
         ├─Etc
         ├─Europe
         ├─Indian
         ├─Pacific
         └─SystemV
```

## 瘦身
jre bin目录下 我们要保留的 是 jvm 虚拟机的配置文件。

之后就是精简lib包， lib包中我们仅仅需要三个jar包：charsets.jar，resources.jar，rt.jar （32位的系统会多几个jar包）
我们使用最核心的jar包是 rt.jar ，需要根据自己的需求删除其中没用的class文件。

最后附上，jre7 Linux和Windows ，32和 64 位 的精简包，希望可以解决你们的问题。

地址：[https://pan.baidu.com/s/1c1RfxxY](https://pan.baidu.com/s/1c1RfxxY)

