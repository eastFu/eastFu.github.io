---
title: '踩坑:python base64'
toc: true
tags: 编程
categories:
  - 编程
  - Python
abbrlink: 13474
date: 2018-04-11 21:46:08
---
## 需求
读取文本内容，对字符串进行base64加密
```
>>> str = 'aaaaaaaaaaaaaaaaaaa\nbbbbbbbbbbbbbbbbbbbbbbbbbbb\ncccccccccccccccccccccccccc'
>>> encodeStr = base64.encodestring(str)
>>> encodeStr
 'YWFhYWFhYWFhYWFhYWFhYWFhYQpiYmJiYmJiYmJiYmJiYmJiYmJiYmJiYmJiYmIKY2NjY2NjY2Nj\nY2NjY2NjY2NjY2NjY2NjY2M=\n'
```
使用到了 base64 模块的 base64.encodestring()

## 采坑
返回的字符串默认结尾带”\n”，而且产生的base64编码字符串每76个字符就会用”\n”隔开

## 解决

使用replace() 替换吊所有的\n

base64.ecodestring每76字符就换行，这个是mime协议的规定，用于email发送，感兴趣的话自己去了解一下mime协议
