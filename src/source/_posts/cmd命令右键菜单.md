---
title: cmd命令右键菜单
toc: true
tags: 编程
categories:
  -  编程
  - 其它
abbrlink: 63799
date: 2017-11-26 18:40:07
---

## 使用方法

新建一个xx.reg，将下面的命令粘贴进去，直接双击运行即可

## 添加cmd
```
Windows Registry Editor Version 5.00

[HKEY_CLASSES_ROOT\*\shell\mycmd]
@="在此处打开CMD"

[HKEY_CLASSES_ROOT\*\shell\mycmd\command]
@="cmd.exe /k pushd \"%L\\..\""[HKEY_CLASSES_ROOT\Folder\shell\mycmd]
@="在此处打开CMD"

[HKEY_CLASSES_ROOT\Folder\shell\mycmd\command]
@="cmd.exe /k pushd \"%L\""[HKEY_CLASSES_ROOT\DesktopBackground\shell\mycmd]
@="在此处打开CMD"

[HKEY_CLASSES_ROOT\DesktopBackground\shell\mycmd\command]
@="cmd.exe /k pushd \"%V\""[HKEY_CLASSES_ROOT\Directory\Background\shell\mycmd]
@="在此处打开CMD"

[HKEY_CLASSES_ROOT\Directory\Background\shell\mycmd\command]
@="cmd.exe /k pushd \"%V\""
```
## 删除cmd
```
Windows Registry Editor Version 5.00

[-HKEY_CLASSES_ROOT\*\shell\mycmd]
[-HKEY_CLASSES_ROOT\Folder\shell\mycmd]
[-HKEY_CLASSES_ROOT\DesktopBackground\shell\mycmd]
[-HKEY_CLASSES_ROOT\Directory\Background\shell\mycmd]
```