---
layout:     post
title:      "Nginx 小记"
subtitle:   "\"记录Nginx小知识点\""
date:       2020-07-16 11:37:00
author:     "Polo"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - nginx
    - 配置
---

> 小记 

## nginx.conf  
#### 变量  
假设客户端请求的url为`http://localhost/example`
+ `$uri`: `/example`
+ `$root`: 是项目代码安装目录
+ `$query_string`: 

#### 路由  
如何理解以下配置  

```
location / {
    try_files $uri $uri/ /index.php?$query_string;
}
```

+ `location /`: 客户端所有请求都会通过`/xxx`的方式请求到服务器，也就是说以上配置可以匹配到所有的请求  
+ `try_files`: 会按照顺序在硬盘里尝试空格后所匹配到的文件  
   1. 如果存在名为 `/$root/example`（其中 $root 是项目代码安装目录）的文件，就直接把这个文件的内容发送给用户。  
   1. 如果存在`$root/example/`的目录则返回目录默认入口文件如`index.php`  
   1. 最后就会 fall back 到 try_files 的最后一个选项 `/index.php`，发起一个内部 “子请求”，也就是相当于 nginx 发起一个 HTTP 请求到 `http://localhost/index.php`。
