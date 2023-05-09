---
title: Windows下编辑shell脚本编码问题
date: 2019-04-06
tags:
 - 问题
categories:
 - 2019
 - 技术
---


::: warning Windows下编辑shell脚本编码问题
在windows下编辑的文件远程传送到linux下的时候每行末尾都会出现^M，这将导致shell脚本运行错误，主要是因为dos下的编辑器和linux下的编辑器对文件末行的回车符处理不一致导致。
:::

### 解决方案

**第一种**

 `sed -i 's/\r//' filename.sh`

**第二种**

`vim filename.sh`

命令模式下 `set ff`

如果输出 fileformat=dos

在命令模式下 `set ff=unix`