---
title: storm 安装部署
toc: true
tags: 编程
categories:
  - storm
abbrlink: 5363
date: 2019-02-26 18:24:57
---

# storm 安装部署

## 1.关于Storm


> Storm是Twitter开源的分布式实时大数据处理框架，被业界称为实时版Hadoop。随着越来越多的场景对Hadoop的MapReduce高延迟无法容忍，比如网站统计、推荐系统、预警系统、金融系统(高频交易、股票)等等，大数据实时处理解决方案（流计算）的应用日趋广泛，目前已是分布式技术领域最新爆发点，而Storm更是流计算技术中的佼佼者和主流。

> 按照storm作者的说法，Storm对于实时计算的意义类似于Hadoop对于批处理的意义。Hadoop提供了map、reduce原语，使我们的批处理程序变得简单和高效。同样，Storm也为实时计算提供了一些简单高效的原语，而且Storm的Trident是基于Storm原语更高级的抽象框架，类似于基于Hadoop的Pig框架，让开发更加便利和高效。

> Storm 目前版本更新到2.x，这里我们选用经典版本1.1.0进行安装部署，storm+kafka+zookeeper 实现流式计算。


官方下载地址：
```
 https://archive.apache.org/dist/storm/
```


## 2.单节点安装部署

### 修改配置文件conf/storm.yaml
```
storm.zookeeper.servers:
     - "zk001.test.com"
nimbus.seeds: ["storm001.test.com"]
storm.log.dir: "/data/storm/logs"
storm.local.dir: "/data/storm/db"
storm.zookeeper.root: "/storm-1.1.0"
ui.port: 18888
supervisor.slots.ports:
    - 19527
    - 19528
    - 19529
```

### 启动storm

```
nohup bin/storm nimbus  >/data/storm/logs/storm_start.log 2>&1 &
nohup bin/storm supervisor  >/data/storm/logs/storm_supervisor.log 2>&1 &
nohup bin/storm ui  >/data/storm/logs/storm_ui.log 2>&1 &
```

### 访问UI web

```
http://ip:18888

```


## 3.集群部署


## 4.应用程序编写和打包发布









