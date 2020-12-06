---
title: 入门(二)Windows环境下redis的安装
toc: true
tags: 编程
categories:
  - 编程
  - Redis
abbrlink: 12975
date: 2014-06-07 19:05:10
---

## 说明

之前介绍了Linux环境下Redis的安装，这次介绍一下Windows环境下Redis的安装，首先要讲的是，Redis官方只支持Linux，还好 Microsoft Open Tech group 开发了windows版本的Redis，github地址： https://github.com/MicrosoftArchive/redis

已经发布了很多个release版本，截至目前已经发布到了 v-3.2.100 。

官网截图：

![官网](http://raw.github.com/eastFu/docs/master/blog/redis-win/redis-win.png)

github截图：

![github](http://raw.github.com/eastFu/docs/master/blog/redis-win/redis-win-github.png)

## 下载与安装
截至到目前最新版本是3.2.100 ，这里就使用这个版本来进行安装，下载 Redis-x64-3.2.100.msi ，双击开始安装：

![步骤1](http://raw.github.com/eastFu/docs/master/blog/redis-win/redis-win-1.png)

![步骤2](http://raw.github.com/eastFu/docs/master/blog/redis-win/redis-win-2.png)

![步骤3](http://raw.github.com/eastFu/docs/master/blog/redis-win/redis-win-3.png)

![步骤4](http://raw.github.com/eastFu/docs/master/blog/redis-win/redis-win-4.png)

![步骤5](http://raw.github.com/eastFu/docs/master/blog/redis-win/redis-win-5.png)

![步骤6](http://raw.github.com/eastFu/docs/master/blog/redis-win/redis-win-6.png)

![步骤7](http://raw.github.com/eastFu/docs/master/blog/redis-win/redis-win-7.png)

![步骤8](http://raw.github.com/eastFu/docs/master/blog/redis-win/redis-win-8.png)

安装完毕，我们看一下目录文件：

![目录](http://raw.github.com/eastFu/docs/master/blog/redis-win/redis-win-9.png)

## redis启动
```
redis-server  redis.windows.conf
```

![启动效果图](http://raw.github.com/eastFu/docs/master/blog/redis-win/redis-win-10.png)

## redis-cli的使用

双击打开 redis-cli.exe，我们可以在命令行使用redis命令来进行操作：

![命令](http://raw.github.com/eastFu/docs/master/blog/redis-win/redis-win-11.png)