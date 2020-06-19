---
layout:     post
title:      "mysql 索引 小记"
subtitle:   "\"慢慢摸索吧...\""
date:       2020-06-19 16:47:00
author:     "Polo"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - mysql
    - 索引
---  

> 小记 

## 复合索引  
优点：复合索引在数据库操作期间所需的开销更小,可以代替多个单一索引;  
对一张表来说,如果有一个复合索引 `on(col1,col2)`,就没有必要同时建立一个单索引 `on col1`； 
例如：
有一个复合索引为`(col1,col2,col3)`
那么  
1. 单独查询`col1=xxx`  
1. 查询`col1=xxx and col2=xxx`  
1. 查询`col1=xxx and col2=xxx and col3=xxx`  

都能使用到改复合索引  
但是
1. 单独查询`col2=xxx`  
1. 单独查询`col3=xxx`  
1. 单独查询`col2=xxx and col3=xxx`  

改复合索引都不起作用  
因此复合索引的顺序也很关键  

# 还有啥索引等待更新...  