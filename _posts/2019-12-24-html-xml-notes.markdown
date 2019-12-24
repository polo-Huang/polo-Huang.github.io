---
layout:     post
title:      "HTML-XML 小记"
subtitle:   "\"有种会忘记的感觉\""
date:       2019-12-24 18:00:00
author:     "Polo"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 语法
    - html
    - xml
    - 转译
---  

> 小记 

# XML  

## 转译  
    <string>https://www.test.cn?key1=value1&key2=value2</string>
报错
    <string>https://www.test.cn?key1=value1&amp;key2=value2</string>
因为&在XML中会被转译必须用`&amp;`表示


# HTML  