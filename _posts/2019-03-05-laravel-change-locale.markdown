---
layout:     post
title:      "Laravel 切换语言"
subtitle:   "\"在larval框架使用session的方式切换语言功能\""
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


laravel支持多语言功能，看了官网的文档一脸懵，测试了一下发现只能设置单个URL的语言设置。之前看过一些项目使用地址栏的参数来判断当前语言，不过感觉会比较麻烦。这里分享一个用session判断当前语言的方法。


## 模版代码  
1\. 在模板中设置切换语言的<a>

<a href="{{ url('/changeLocale/en') }}">en</a><a href="{{ url('/changeLocale/zh') }}">zh</a>

## 路由  
2\. 在routes/web.php中创建一个get路由

    //修改语言
    Route::get('/changeLocale/{locale}', 'HomeController@changeLocale');

## 控制器  
3\. 在app/Http/HomeController.php中创建changeLocale方法

	public function changeLocale($locale){
	    if (in_array($locale, ['en', 'zh'])) {
	        session()->put('locale', $locale);
	    }
	    return redirect()
	    ->back()
	    ->withInput();
	}

这里通过session将当前的语言存起来

## 中间件  
4\. 创建中间件执行一下命令

    php artisan make:middleware SetLocale

打开app/Http/Middleware/SetLocale.php修改handle方法

    public function handle($request, Closure $next) {
        if (Session::has('locale') && in_array(Session::get('locale'), ['en', 'zh'])) {
            App::setLocale(Session::get('locale'));
        } else {
        	App::setLocale('en');
       	}
       	return $next($request);
    }

再在app/Http/Kernel.php

修改以下

    protected $routeMiddleware = [...'setLocale' => \App\Http\Middleware\SetLocale::class,...];

最后将中间件setLocale加入其他路由中

    //根据session值设置语言
    Route::group(['middleware' => ['setLocale']], function() {    Route::get('/', 'HomeController@index');});

## 完成  
点击模板中的`<a>`就可以了