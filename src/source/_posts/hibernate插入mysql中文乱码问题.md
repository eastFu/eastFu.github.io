---
title: hibernate插入mysql中文乱码问题
toc: true
tags: 编程
categories:
  - 编程
  - Java
abbrlink: 47378
date: 2014-08-26 09:41:23
---

## mysql配置问题
首先需要修改mysql数据库的配置文件my.ini，此文件放在mysql根目录下。在此文件下查找default-character-set属性，并将其值更改为utf8（注意：不是utf-8，也要注意大小写），这里需要将default-character-set属性全部属性的值修改为utf8。示例：
```
default-character-set= utf8
```
提示：default-character-set属性有两个，一个在[mysql]下面，另外一个在[mysqld]下面。

## sql问题
同时创建hibernate数据库时需要显示设置数据库的编码方式为utf8。示例：
```
create database daycode default charset=utf8;
```
## hibernate配置问题

做完这两步还是不行，需要修改hibernate的配置文件hibernate.cfg.xml，在配置文件配置hibernate.connection.url属性。示例：
```
<property name="hibernate.connection.url">
		<![CDATA[jdbc:mysql://localhost:3306/daycode?useUnicode=true&characterEncoding=utf8]]>
</property>
设置这些之后乱码问题就解决了。
```
