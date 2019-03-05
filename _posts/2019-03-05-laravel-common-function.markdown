---
layout:     post
title:      "Laravel 全局函数"
subtitle:   "\"在laravel框架使用全局变量的方法，前端模版后台控制器都可以直接使用\""
date:       2019-03-05 11:40:00
author:     "Polo"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - program
    - laravel
    - php
---

> Laravel  

在使用laravel框架的时候框架本身的很多功能可以满足基本的一些需求，但是有时候在项目中的很多地方都需要用到同样的程序时我们会想创建一个公用的全局函数。在这里我分享一下我的做法。

首先在App/下创建一个Helpers文件夹（可以自己命名）并新建一个放置函数的文件

![](https://img-blog.csdn.net/20180609175243671?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNjgyNTk4Mg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

common.php    下面举个栗子

    <?php
    //引入栗子
    use App\Chestnut;
    /*
    *  获取栗子ID放回栗子实例
    */
    function getChestnut($chestnut_id)
    {
    	$chestnut = Chestnut::find($chestnut_id);
    	return = $chestnut;
    }
    ?>

接着在composer.json中的files中输入

    "autoload": {
    	...
    	"files": [
    	...
    	"app/Helpers/common.php",
    	...
    	]
    },

然后执行一下命令

    composer dump-auto

最后我们就可以了试着在HomeController使用这个函数了。

    class HomeController extends Controller{
    	/**
    	* Show the application dashboard.
    	*
    	* @return \Illuminate\Http\Response
    	*/
    	public function index()
    	{
    		dd(getChestnut(1));
    		...
    	}