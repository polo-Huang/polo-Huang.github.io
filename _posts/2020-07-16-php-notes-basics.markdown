---
layout:     post
title:      "PHP 小记 | 基础篇"
subtitle:   "\"记录PHP基础知识点\""
date:       2020-07-16 11:05:00
author:     "Polo"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - php
    - 基础
---

> 小记 

## 变量  
#### 超级变量  
PHP中预定义了几个超级全局变量（superglobals） ，这意味着它们在一个脚本的全部作用域中都可用。 你不需要特别说明，就可以在函数及类中使用。  
PHP 超级全局变量列表:  

+ $GLOBALS
+ $_SERVER
+ $_REQUEST
+ $_POST
+ $_GET
+ $_FILES
+ $_ENV
+ $_COOKIE
+ $_SESSION

使用`$_SESSION`可能会有以下报错  
> Undefined variable: _SESSION  

错误的原因是php session没有开启造成的结果。在使用变量之前使用`session_start()`函数开启session就可以了,或者在`php.ini`里把`session.auto_start = 0`设置为`session.auto_start = 1`。

