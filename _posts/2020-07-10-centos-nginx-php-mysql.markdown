---
layout:     post
title:      "Centos Nginx PHP MySQL php web 环境搭建"
subtitle:   "\"记录零基础部署步骤\""
date:       2020-07-10 11:12:00
author:     "Polo"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - linux
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
# nginx -v
nginx version: nginx/1.16.1
```  
  
看到以下输出就表示安装成功了，接下来可以通过 `ip` 访问到nginx，看到`Welcome to xxx`就代表nginx已经安装并启动了  
假如出现  
> xxx 拒绝了我们的连接请求。  

出现以上问题应该是nginx没有启动  
  
```
# nginx
```  
  
直接在命令行中输入以上命令就可以了，重新访问就会发现可以看到nginx的欢迎页面  

如何使用nginx呢？  
最简单的方式就是，可以在nginx访问目录下创建一个文件，通过 http://`ip`/`文件名` 的方式直接访问到文件。  
  
**举个栗子**  
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
可以深入研究一下[nginx配置详解](https://www.runoob.com/w3cnote/nginx-setup-intro.html)，或者了解以下[nginx小记](/2020/07/16/nginx-notes/)  
  
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
**例如：**  
  
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
如果新配置的目录找不到到的话就会有一下错误  
> 404 Not Found
> nginx/1.16.1  
  
```
# mkdir /data
# mkdir /data/html
# cd /data/html
# vim index.html

hello nginx!

:wq
```  
  
继续访问`ip`就可以看到文件内容啦  
如果在主目录下创建的文件名为`index.html`，则访问时不需要加上`/index.html`。因为`index.html`就是默认的入口文件  
#### 配置nginx过程中一些常见问题  
学习过程中一边学习一边使用的学习效果会比较好，而入门之后的边用边学也是提升自身能力的必经过程,那么在使用过程中自然会遇到各式各样的问题,学习很多技能都是一样，授人鱼不如授人以渔，只有掌握了自己解决问题的能力才是关键。  
当然，在摸爬滚打的过程中适时的被拉一把会跑的更快，[下面](https://blog.csdn.net/qq13650793239/article/details/81185293?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.nonecase&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.nonecase)让我们看一下一些常见的问题  

## PHP  
PHP即“超文本预处理器”，是一种通用开源脚本语言。  

接下来就是项目的后端的搭建  
#### 安装  
下面是指定7.2版本安装php的安装步骤  
  
```
# yum install -y epel-release
# wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo
# rpm -ivh http://rpms.famillecollet.com/enterprise/remi-release-7.rpm
# yum install --enablerepo=remi --enablerepo=remi-php72 php
# php -v
PHP 7.2.32 (cli) (built: Jul  8 2020 07:33:50) ( NTS )
Copyright (c) 1997-2018 The PHP Group
Zend Engine v3.2.0, Copyright (c) 1998-2018 Zend Technologies
```  
  
看到PHP版本号就代表安装成功了  
安装[php-fpm](https://baike.baidu.com/item/php-fpm/10256166?fr=aladdin)  
  
```
# yum install --enablerepo=remi --enablerepo=remi-php72 php-fpm
# php-fpm -v
PHP 7.2.32 (fpm-fcgi) (built: Jul  8 2020 07:35:05)
Copyright (c) 1997-2018 The PHP Group
Zend Engine v3.2.0, Copyright (c) 1998-2018 Zend Technologies
# systemctl start php-fpm
# netstat -tunlp | grep 9000
tcp        0      0 127.0.0.1:9000          0.0.0.0:*               LISTEN      2462/php-fpm: maste
```  
  
使用`systemctl`启动php-fpm并查看9000是否已被php-fpm监听，  
#### 访问PHP文件  
接下来就是通过nginx访问到php文件了  

```
# cd /data/html
# vim test.php

<?php
    phpinfo();

?>

:wq
```

在目录下创建`test.php`文件，内容为打印出php配置信息。然后尝试访问一下`/test.php`  
这时候会发现test.php会被直接下载下来  
这是因为nginx无法自己解析php文件，除了txt, html, pdf等等文件其他文件在没有其他解释器的情况下访问时就会被直接下载下来，所以我们需要在nginx配置文件中配置php的解析代理  

```
# vim /etc/nginx/nginx.conf

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

    location ~ \.php$  {
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;

        fastcgi_param SCRIPT_FILENAME           $document_root$fastcgi_script_name;
        include        fastcgi_params;
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

在nginx配置文件中添加以上配置，将php文件后缀的通过9000代理解析。重启nginx，重新访问`/test.php`。发现我们可以成功访问到`phpinfo`了  
配置好php代理之后，我们就可以开始开发PHP项目了  
#### 编写第一个PHP程序  
```
# vim /data/html/test.php

<?php
    // phpinfo(); 注释PHP配置打印
    echo "我的第一个PHP程序";

?>

:wq
```

通过`echo`语法输出文案  
进而学习[PHP语法](https://www.runoob.com/php/php-syntax.html)编写更加复杂的程序  

## MySQL  
MySQL是一种开放源代码的关系型数据库管理系统（RDBMS）使用最常用的数据库管理语言--结构化查询语言（SQL）进行数据库管理。  
*在使用PHP编写程序的过程中一般都会配合mysql进行数据管理*  
#### 安装  

```
# rpm -ivh https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
# yum -y install mysql-community-server
# mysql -V
# systemctl enable mysqld
# systemctl start mysqld.service
mysql  Ver 14.14 Distrib 5.7.31, for Linux (x86_64) using  EditLine wrapper
# mysql
ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: NO)
# sudo cat /var/log/mysqld.log
2020-07-15T02:39:22.082574Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
2020-07-15T02:39:23.099838Z 0 [Warning] InnoDB: New log files created, LSN=45790
2020-07-15T02:39:23.238996Z 0 [Warning] InnoDB: Creating foreign key constraint system tables.
2020-07-15T02:39:23.309898Z 0 [Warning] No existing UUID has been found, so we assume that this is the first time that this server has been started. Generating a new UUID: 646c0852-c644-11ea-86bb-00163e0a15a1.
2020-07-15T02:39:23.313649Z 0 [Warning] Gtid table is not ready to be used. Table 'mysql.gtid_executed' cannot be opened.
2020-07-15T02:39:24.314597Z 0 [Warning] CA certificate ca.pem is self signed.
2020-07-15T02:39:24.533356Z 1 [Note] A temporary password is generated for root@localhost: xxxxxxxxxxx
...
# mysql -uroot -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 7
Server version: 5.7.31

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> set password for root@localhost=password('新密码');
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql> quit
Bye
# mysql -uroot -p
Enter password:
...
mysql> 
```

通过rpm安装`mysql5.7`再使用`mysql -V`命令查看mysql版本看到以上输出证明已经安装完成了，接下来设置自启动并启动mysql  
然后使用`mysql`命令时会输出以上报错，需要密码登录。这时候我们可以以root用户查看`/var/log/mysqld.log`获取`root@localhost: `后面的初始密码进行登录并修改密码。修改后退出使用新密码重新进入则代表修改密码成功  
#### 使用SQL命令创建第一个数据库  
可以使用以下命令先看一下mysql中几个默认的数据库，使用`use 数据库名`可以使用指定数据库并用`show tables;`命令查看其所有数据表  

```
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.00 sec)

mysql> use mysql
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
+---------------------------+
| Tables_in_mysql           |
+---------------------------+
| columns_priv              |
| db                        |
| engine_cost               |
| event                     |
| func                      |
| general_log               |
| gtid_executed             |
| help_category             |
| help_keyword              |
| help_relation             |
| help_topic                |
| innodb_index_stats        |
| innodb_table_stats        |
| ndb_binlog_index          |
| plugin                    |
| proc                      |
| procs_priv                |
| proxies_priv              |
| server_cost               |
| servers                   |
| slave_master_info         |
| slave_relay_log_info      |
| slave_worker_info         |
| slow_log                  |
| tables_priv               |
| time_zone                 |
| time_zone_leap_second     |
| time_zone_name            |
| time_zone_transition      |
| time_zone_transition_type |
| user                      |
+---------------------------+
31 rows in set (0.00 sec)

mysql>
``` 

其中自带的`mysql`数据库存放着mysql的相关配置数据，比如用户信息和权限、时区、服务等设置  
**下面我们来创建第一个数据库**  
深入学习[SQL语法](https://www.runoob.com/mysql/mysql-create-database.html)可以更加熟悉的操作数据库  
下面创建数据库的同时指定编码编码格式一般我们使用的是`utf8_general_ci`格式
> 如果没有指定编码格式的话可能会导致了使用utf-8格式的project訪问数据库的过程中产生中文乱码的问题。  

```
mysql> CREATE DATABASE `test` DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;
Query OK, 1 row affected (0.00 sec)
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| test               |
| sys                |
+--------------------+
5 rows in set (0.00 sec)

Database changed
mysql> show tables;
Empty set (0.00 sec)

mysql>

```

创建好之后查看数据表，现在还是空的，下面继续创建数据表  

```
mysql> CREATE TABLE IF NOT EXISTS `users`(
    ->    `id` int(11) AUTO_INCREMENT,
    ->    `account` VARCHAR(255) NULL,
    ->    `nickname` VARCHAR(255) NULL,
    ->    `phone` VARCHAR(11) NULL,
    ->    `sex` tinyint(1) DEFAULT '0' COMMENT '0: 未设置; 1: 男; 2: 女',
    ->    PRIMARY KEY ( `id` )
    -> )ENGINE=InnoDB DEFAULT CHARSET=utf8;
Query OK, 0 rows affected (0.02 sec)

mysql> show tables;
+----------------+
| Tables_in_test |
+----------------+
| users          |
+----------------+
1 row in set (0.00 sec)

```

插入一条数据并查询`users`表  

```
mysql> insert into users (account,nickname,phone,sex) values ('account1','nickname1','12345678910','1');
Query OK, 1 row affected (0.01 sec)

mysql> select*from users;
+----+----------+-----------+-------------+------+
| id | account  | nickname  | phone       | sex  |
+----+----------+-----------+-------------+------+
|  1 | account1 | nickname1 | 12345678910 |    1 |
+----+----------+-----------+-------------+------+
1 row in set (0.00 sec)

```

可以看到现在`users`中已经有一条数据了，尝试修改数据内容  

```
mysql> update users set sex=2 where account='account1';
Query OK, 1 row affected (0.01 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> select*from users;
+----+----------+-----------+-------------+------+
| id | account  | nickname  | phone       | sex  |
+----+----------+-----------+-------------+------+
|  1 | account1 | nickname1 | 12345678910 |    2 |
+----+----------+-----------+-------------+------+
1 row in set (0.00 sec)

```

修改成功，以上就是mysql的基础操作了，这里没有演示删除操作（*注意删除数据需谨慎*），可以继续了解[sql语法](https://www.runoob.com/mysql/mysql-delete-query.html)尝试更多数据库操作  

## PHP+MySQL开发  
学习了PHP与MySQL，那么现在可以使用PHP操作MySQL进行项目开发了  
#### 使用PHP链接MySQL  
###### 链接方式  
[如何使用PHP链接MySQL？](https://www.runoob.com/php/php-mysql-connect.html)
###### 首先安装PHP的mysql扩展  
> 如何使用PHP操作MySQL呢？  
> 我们需要通过安装PHP的[数据库扩展](https://www.php.net/manual/zh/refs.database.php)。  

通过以上所述链接方式的选择来决定安装PHP所需的那些扩展，下面以`PDO`连接方式为例  

```
# yum install --enablerepo=remi --enablerepo=remi-php72 php-pdo
# yum install --enablerepo=remi --enablerepo=remi-php72 php-mysql
```

###### 尝试链接数据库  
安装完成后，创建`index.php`文件编写以下代码

```
# cd /data/html
# vim index.php

<?php

try {
    $conn = new PDO("mysql:host=localhost;dbname=test;charset=utf8", "root", "密码");
    echo "连接成功";
}
catch(PDOException $e)
{
    echo "连接失败：".$e->getMessage()."\n";
    die();
}

$conn = null;
echo "关闭连接";

?>

:wq
```

访问`http://xxx/index.php`看到连接成功后关闭连接的输出就代表我们可以通过PHP成功连接到MySQL了  
#### 开发PHP项目  
###### 搭建项目目录结构  
下面简单封装一下PHP项目的目录结构  

+ configs `配置文件目录`
   + config.php `基础配置文件`
+ core `核心文件目录`
   + db.php `数据库类`
   + init.php `初始化文件`
+ index.php `入口文件: 路由管理时可通过入口文件转发请求`
+ users.php `未封装路由时可以通过目录层级直接访问到指定文件`

编写以上文件代码  
**config.php**

```
<?php
/**
 * 定义常量
 */
define('WEB_TITLE', 'PHP+MYSQL 测试项目');


/* 定义全局变量
    */
$GLOBALS = [];
global $GLOBALS;

// 数据库配置变量
$GLOBALS['config']['db'] = [
    'type'    => "mysql",
    'host'    => "localhost",
    'user'    => "root",
    'pass'    => "sqlPolo@07",
    'name'    => "test",
    'charset' => "utf8",
];


```

**db.php**

```
<?php
if( !defined('CORE') ) exit('Request Error!');
/**
 * 数据库操作类
 */
class db
{
    /* 配置数据库
     */
    private static $conn;

    private static function connect()
    {
        $db_config = $GLOBALS['config']['db'];
        $dsn = "{$db_config['type']}:host={$db_config['host']};dbname={$db_config['name']};charset={$db_config['charset']}";
        $user = $db_config['user'];
        $pass = $db_config['pass'];
        try {
            self::$conn = new PDO($dsn, $user, $pass);
            self::$conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
        }
        catch(PDOException $e)
        {
            echo "链接失败：".$e->getMessage()."\n";
            die();
        }
    }
    
    private static function close()
    {
        self::$conn = null;
    }

    // 操作函数
    public static function select($sql, $params = [])
    {
        // 打开链接
        self::connect();

        $stmt = self::$conn->prepare($sql);
        $stmt->execute();
        $result = $stmt->fetchAll();

        // 关闭链接
        self::close();
        return $result;
    }
}


```

**init.php**

```
<?php
/**
 * 框架核心入口文件
 *
 * 环境检查，核心文件加载
 *
 */

//核心库目录
define('CORE', dirname(__FILE__));

//系统配置
require CORE.'/../configs/config.php';

//加载核心类库
require CORE.'/db.php';


```

**index.php**

```
<?php
header('Content-Type: text/html; charset=utf-8');
require './core/init.php';

$html = "
<html>
<head>
    <title>".WEB_TITLE."</title>
</head>
<body>
    <p>这是我的第一个PHP程序</p>
    <a href='/users.php'>用户列表</a>
</body>
</html>
";

echo $html;


```

###### 编写业务逻辑  
前面我们创建了`users`数据表，下面就来通过数据库类进行数据操作输出用户列表  
**users.php**

```
<?php
header('Content-Type: text/html; charset=utf-8');
require './core/init.php';

$users = db::select("select * from users");

$tbody = "";
foreach ($users as $user) {
    $tbody .= "
    <tr>
        <td>{$user['id']}</td>
        <td>{$user['account']}</td>
        <td>{$user['nickname']}</td>
        <td>{$user['phone']}</td>
        <td>{$user['sex']}</td>
    </tr>
    ";
}

$table = "
<table>
    <thead>
        <tr>
            <th>ID</th>
            <th>账号</th>
            <th>昵称</th>
            <th>手机</th>
            <th>性别</th>
        </tr>
    </thead>
    <tbody>
        {$tbody}
    </tbody>
</table>
";
$html = "
<html>
<head>
    <title>".WEB_TITLE."</title>
</head>
<body>
    <h4>用户列表</h4>
    <br>
    <a href='/'>返回首页</a>
    {$table}
</body>
</html>
";
echo $html;


```

增删改查是数据管理的基本操作，剩余增删改以及更多查询功能待完善  

**以上所有就是PHPweb程序基础学习开发的个人总结基本内容，更多细节有待探讨...**  