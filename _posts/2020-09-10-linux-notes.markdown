---
layout:     post
title:      "Linux 小记"
subtitle:   "\"记录Linux小知识点\""
date:       2020-09-10 16:05:00
author:     "Polo"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - linux
---

> 小记 

## 命令  
#### grep  
```
grep 'GET /15/qd.html' access.log | cut -d' ' -f 1 | sort | uniq | wc -l  
```

从 `access.log` 查询 `'GET /15/qd.html'`  

+ `zgrep` 搜索压缩文件内容
+ `grep` 搜索文件内容
   1. `-n` 显示行号
   1. `-v` 取反
   1. `-i` 不区分大小写
   1. `-c` 带关键字的行数
   1. `-q` 静默执行，即不在屏幕显示匹配到的内容
   1. `-A` 连带显示关键字之后的几行
   1. `-B` 连带显示关键字之前的几行
   1. `--color` 带颜色显示关键字
+ `|` 符号为添加条件管道  
+ `cut` 提取内容
   1. `-b 1-6` 取第一到第六个字节内容
   1. `-d' '` 根据空格切割内容
   1. `-c 1-3` 按照第一到第三个字符内容
   1. `-f` 指定分割后的哪一列  
+ `sort` 根据内容排序  
   1. `-n`：（numeric-sort 数值排序）根据数值比较，默认单个字符比较
   1. `-r`：reverse（颠倒） 反序输出结果
   1. `-t: -k 3`：以冒号为分隔符输出第三列（separator分离器）
   1. `-k`：排序建（key，排序的列字段）
   1. `-u`：去掉重复行（uniq 唯一的）
+ `uniq` 合并连续重复的行内容
   1. `-u`：只显示没有连续重复的行
   1. `-c`：统计连续重复行的次数
   1. `-d`：只显示一次连续重复的行
   1. `-i`：忽略大小写
+ `wc` 统计行数、单词数、字节数
   1. `-l` 统计行数
   1. `-c` 统计字节数
   1. `-w` 统计单词数
   1. `-m` 统计字符数  

#### rsync  
```
rsync -avz /data/game-backend/index/ root@192.168.100.220:/opt/sdios
```
`rsynz` 同步文件或目录  

+ `-a --archive`：归档模式，表示递归传输并保持文件属性。等同于"-rtopgDl"。
+ `-v`：显示rsync过程中详细信息。可以使用"-vvvv"获取更详细信息。
+ `-z`：传输时进行压缩提高效率。。