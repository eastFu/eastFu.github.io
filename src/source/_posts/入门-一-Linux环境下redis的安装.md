---
title: 入门(一)Linux环境下redis的安装
toc: true
tags: 编程
categories:
  - 编程
  - Redis
abbrlink: 31027
date: 2014-06-06 16:52:03
---
## 关于redis
>Redis最为一款开源的key-value存储系统，自推出到现在一直受到编程人员的喜爱。它支持存储多种value类型，String 、List 、Set 、Zset 、Hash。这些数据类型都支持push/pop、add/remove 及取交集、并集、差集等操作，这些操作都是原子性的，而且Redis还有各种不同的排序方式。

Redis 的数据都是缓存在内存中，这样做是为了保证效率，而且也支持数据的磁盘持久化，目前Redis 不仅可以部署Linux 而且Windows 同样可以进行部署。

## 下载与安装
Redis的下载地址：http://download.redis.io/releases/ 版本很多，请自己下载想要安装的版本，这里 介绍 3.2.8的安装过程：
```
$ wget http://download.redis.io/releases/redis-3.2.8.tar.gz
$ tar xzf redis-3.2.8.tar.gz
$ cd redis-3.2.8
$ make
$ make install
```
## 启动
安装完成之后，进入src文件夹，你会发现脚本文件很多，为了操作方便我们将我们一会要使用到的脚本文件拷贝到一个目录同一进行管理：
```
# 1. 创建两个文件夹 bin 、etc

$ mkdir -p /usr/local/redis/bin
$ mkdir -p /usr/local/redis/ect

# 2. copy 脚本和配置文件

$ mv /lamp/redis-3.2.8/redis.conf /usr/local/redis/etc
$ cd /lamp/redis-3.2.8/src
$ mv mkreleasehdr.sh redis-benchmark redis-check-aof redis-check-dump redis-cli redis-server /usr/local/redis/bin

#3. 启动Redis

$ cd /usr/local/redis/bin
$ ./redis-server
```
下面是启动后的截图（redis 默认端口 6379）：
![启动效果图](http://raw.github.com/eastFu/docs/master/blog/redis-linux/redis-linux.png)

## 问题分析
如上图所示，redis已经成功启动，但是这里有三个WARNING 警告项，请不要忽略，下面介绍一下解决方法：
* 第一个错误大概是说somaxconn的值128设置过小，从/proc/sys/net/core/somaxconn这个路径也可大概知道这个值的设置是关于网络连接中某个最大值的限定设置，此值表示网络连接的队列大小，在配置文件redis.conf中的“tcp-backlog 511”就配置在高并发环境下的最大队列大小，此值受限于系统的somaxconn与tcp_max_syn_backlog这两个值，所以应该把这两个内核参数值调大，具体解决方法如下：

```
$ vim /etc/sysctl.conf
$ net.core.somaxconn = 20480  #最大队列长度，应付突发的大并发连接请求，默认为128
$ net.ipv4.tcp_max_syn_backlog = 20480  #半连接队列长度，此值受限于内存大小，默认为1024
$ sysctl -p  #使参数生效
```
* 报错解释

```
警告：过量使用内存设置为0！在低内存环境下，后台保存可能失败。为了修正这个问题，
请在/etc/sysctl.conf 添加一项 'vm.overcommit_memory = 1' ，
然后重启（或者运行命令'sysctl vm.overcommit_memory=1' ）使其生效。

vm.overcommit_memory不同的值说明：

0 表示检查是否有足够的内存可用，如果是，允许分配；如果内存不够，拒绝该请求，并返回一个错误给应用程序。
1 允许分配超出物理内存加上交换内存的请求
2 内核总是返回true
redis的数据回写机制分为两种

同步回写即SAVE命令。redis主进程直接写数据到磁盘。当数据量大时，这个命令将阻塞，响应时间长
异步回写即BGSAVE命令。redis 主进程fork一个子进程，复制主进程的内存并通过子进程回写数据到磁盘。
由于RDB文件写的时候fork一个子进程。相当于复制了一个内存镜像。当时系统的内存是4G，而redis占用了
近3G的内存，因此肯定会报内存无法分配。如果 「vm.overcommit_memory」设置为0，在可用内存不足的情况
下，就无法分配新的内存。如果 「vm.overcommit_memory」设置为1。 那么redis将使用交换内存。
```

解决方法:

```
$ vim /etc/sysctl.conf
$ vm.overcommit_memory = 1  #末尾追加
$ sysctl -p  #参数生效
注：使用交换内存并不是一个完美的方案。最好的办法是扩大物理内存。
```

* 关闭THP透明内存

```
`Transparent Huge Pages (THP)`告警，这是一个关于透明内存巨页的话题。简单来说内存可管理的最小
单位是page，一个page通常是4kb，那1M内存就会有256个page，CPU通过内置的内存管理单元管理page表
记录。Huge Pages就是表示page的大小已超过4kb了，一般是2M到1G，它的出现主要是为了管理超大内存。
个人理解上TB的内存。而THP就是管理Huge Pages的一个抽象层次，根据一些资料显示THP会导致内存锁
影响性能，所以一般建议关闭此功能。

"/sys/kernel/mm/transparent_hugepage/enabled”有三个值，如下：
$ cat /sys/kernel/mm/transparent_hugepage/enabled

always [madvise] never
####
# always 尽量使用透明内存，扫描内存，有512个 4k页面可以整合，就整合成一个2M的页面
# never 关闭，不使用透明内存
# madvise 避免改变内存占用
```

解决方法：

```
# 临时解决
$ echo never > /sys/kernel/mm/transparent_hugepage/enabled

# 永久解决
$ vim /etc/rc.local
$ echo never > /sys/kernel/mm/transparent_hugepage/enabled  #在开机脚本里追加此命令
```

## redis的启动和暂停
刚刚我们使用 ./redis-server 来启动了Redis ，但是Redis默认是前台启动的，后台启动Redis设置：
```
# 修改配置文件，将 daemonize 设置为 yes

$ cd /usr/local/redis/etc
$ vim redis.conf
$ daemonize yes

# 调用启动脚本

$ cd /usr/local/redis/bin
$ ./redis-server /usr/local/redis/etc/redis.conf
复制代码
停止Redis 两种方式：

$ cd /usr/local/redis/bin
$ ./redis-cli shutdown

# 或者关闭端口为 6379 的 redis-server
$ ./redis-cli -p 6379 shutdown
```
到此我们对Redis有了初步的了解，后面我们将继续了解Redis的其他内容。
