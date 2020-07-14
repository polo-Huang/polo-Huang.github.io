---
layout:     post
title:      "Centos Nginx PHP Mysql php web 环境搭建"
subtitle:   "\"记录零基础部署步骤\""
date:       2020-07-10 11:12:00
author:     "Polo"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - centos
    - nginx
    - php
    - mysql
    - web
---  

> 小记 

## Centos  
Centos是Linux发行版之一  
#### Centos与Ubuntu  
[参考文章](https://www.idcspy.com/centos-ubuntu.html)  
根据使用习惯，所以以下使用的操作系统为`Centos`

## Nginx
Nginx (engine x) 是一个高性能的HTTP和反向代理web服务器，同时也提供了IMAP/POP3/SMTP服务。  
#### 安装
通过[`yum`](https://baike.baidu.com/item/yum/2835771?fr=aladdin)安装方式简单便捷
```
# yum install nginx
```  
就安装成功了，接下来可以通过 `ip` 访问到nginx，看到`Welcome to xxx`就代表nginx已经安装并启动了  
> xxx 拒绝了我们的连接请求。  
出现以上问题应该是nginx没有启动  
```
# nginx
```  
直接在命令行中输入以上命令就可以了，重新访问就会发现可以看到nginx的欢迎页面  

如何使用nginx呢？  
最简单的方式就是，可以在nginx访问目录下创建一个文件，通过 http://`ip`/`文件名` 的方式直接访问到文件。  
举个栗子  
先进入nginx访问目录 在Ubuntu下的默认路径是 `/usr/share/nginx/html`  
创建一个测试用的`html`文件按`i`输入任意文本按`Esc`进入命令模式再输入`:wq`保存并退出  
以下使用[`vim`](https://www.cnblogs.com/greatLong/p/12188185.html)编辑文件  
```
# cd /usr/share/nginx/html
# vim test.html

hello nginx!

:wq
```  
然后我们继续访问 http://`ip`/test.html 就可以看到我们编辑的文本了。  
使用以上方式在访问目录下放置打包好的`vue`项目就可以通过`ip`访问到我们的纯静态的`vue`项目了。  
*注意vue下的入口文件一定是要在 `/usr/share/nginx/html` 里面，不能在其他目录层级。当然如果要自定义目录路径也是可以的，可以通过修改`nginx`配置文件实现。*  
#### 配置nginx配置文件  
nginx的配置文件默认的路径在 `/etc/nginx/`  
进入目录我们可以使用来`ll`查看一下目录下都有哪些文件  
```
# cd /etc/nginx/
# ll
drwxr-xr-x 2 root root 4096 10月  3 2019 conf.d
drwxr-xr-x 2 root root 4096 10月  3 2019 default.d
-rw-r--r-- 1 root root 1077 10月  3 2019 fastcgi.conf
-rw-r--r-- 1 root root 1077 10月  3 2019 fastcgi.conf.default
-rw-r--r-- 1 root root 1007 10月  3 2019 fastcgi_params
-rw-r--r-- 1 root root 1007 10月  3 2019 fastcgi_params.default
-rw-r--r-- 1 root root 2837 10月  3 2019 koi-utf
-rw-r--r-- 1 root root 2223 10月  3 2019 koi-win
-rw-r--r-- 1 root root 5231 10月  3 2019 mime.types
-rw-r--r-- 1 root root 5231 10月  3 2019 mime.types.default
-rw-r--r-- 1 root root 2471 10月  3 2019 nginx.conf
-rw-r--r-- 1 root root 2656 10月  3 2019 nginx.conf.default
-rw-r--r-- 1 root root  636 10月  3 2019 scgi_params
-rw-r--r-- 1 root root  636 10月  3 2019 scgi_params.default
-rw-r--r-- 1 root root  664 10月  3 2019 uwsgi_params
-rw-r--r-- 1 root root  664 10月  3 2019 uwsgi_params.default
-rw-r--r-- 1 root root 3610 10月  3 2019 win-utf
# cat nginx.conf
```  
以上输出就是目录下的原始文件  
首先 `nginx.conf` 就是配置文件的入口文件  
我们可以使用 `cat` 命令查看文件内容  
可以深入研究一下[nginx配置详解](https://www.runoob.com/w3cnote/nginx-setup-intro.html)  
下面我们可以做一些简单的配置  
*在不熟悉的情况下建议先将`nginx.conf`备份一份修改文件名为`nginx.conf.bak`进行备份，如果后面不知道自己不小心修改到了那些配置导致无法运行等问题时可以直接替换为默认配置*  
```
# cp nginx.conf nginx.conf.bak
# vim nginx.conf
...
    server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  _;
        root         /usr/share/nginx/html;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        location / {
        }

        error_page 404 /404.html;
            location = /40x.html {
        }

        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }
...
```  
进入配置文件之后可以看到`server块`  
> server块：配置虚拟主机的相关参数，一个http中可以有多个server。  
可以修改`server块`中的`root`改变`nginx`访问入口文件目录，修改成我们想要的路径，按照自己的习惯来维护项目的文件目录  
例如：  
```
...
    server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  _;
        root         /data/html;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        location / {
        }

        error_page 404 /404.html;
            location = /40x.html {
        }

        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }
...

:wq
# nginx -s reload
```  
修改保存并推出后记得重启nginx，这样新的配置设置才能生效  
#### 配置nginx过程中一些常见问题  

## PHP  
PHP即“超文本预处理器”，是一种通用开源脚本语言。  

接下来就是项目的后端的搭建  
#### 安装  
```
# apt-get install php
# php -v
```  
看到PHP版本号就代表安装成功了  
安装[php-fpm](https://baike.baidu.com/item/php-fpm/10256166?fr=aladdin)  
```
# apt-get install php-fpm
# netstat -ap | grep php-fpm
```  
看到php-fpm进程运行中的端口号   