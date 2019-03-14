---
layout:     post
title:      "PHP 小记 | 内置函数篇"
subtitle:   "\"记录PHP使用时常见的却又常忘记的语法\""
date:       2019-03-14 14:40:00
author:     "Polo"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 程序
    - php
    - 语法
    - 内置函数
    - 小记
---

> 小记 

## 判断函数  
`isset()`与`!empty()`的区别

    $a = 0;
    isset($a) // 输出 true
    !empty($a) // 输出 flase

## 转换函数  


## 处理函数
`preg_match_all()`  
正则获取匹配的字符返回`$matches`数组结果

    $str = '<img src="123.png">';
    $isMatched = preg_match_all('/<img\ssrc=\".\">/', $str, $matches);
    var_dump($isMatched, $matches);


