---
layout:     post
title:      "NodeJs Express Mysql - MVC API"
subtitle:   "\"基于nodeJs-express + mysql以mvc文件结构的API开发demo\""
date:       2019-03-02 11:40:00
author:     "Polo"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - program
    - nodeJs
    - express
    - mysql
    - mvc
    - api
---

> Node Js

## 初学NodeJs   
最近开始入坑小程序，而独立服务端开发我选择用`nodeJs`。  
长时间使用世界上最优雅😇的框架`laravel`让我忘了怎么链接数据库了。查了很多文档很多博客，终于搭建了一个`mvc`结构的`nodeJs+express+mysql`的demo。  

## 搭建本地环境
以mac安装为例:
首先进入你的工作目录
```
cd ~/workspace
```
安装NodeJs
```
node -v // 可以先判断是否已经安装了nodejs
// 没有安装的话可以自行百度安装node
```
下面需要`npm`来执行安装操作，如果安装太慢可以使用`cnpm`
```
sudo npm install -g cnpm --registry=https://registry.npm.taobao.org
```

先安装express-generator
```
sudo npm install -g express-generator
```
安装express
```
sudo npm install -g express
```
查看是否安装成功
```
express --version
```
创建Express项目
```
express test
npm install // 安装依赖
```
这样就搭建好了express项目了 输入运行
```
npm start
```

## 基本目录结构  

| 目录名        |           |
| ------------ | ------------ |
| node_modules | node 依赖  |
| routes       | 路由       |
| app.js       | 核心入口文件 |

## 创建配置目录
在根目录创建`configs`目录，创建app.config.js文件
```
/**
 * Created by jun on 2019/3/2.
 * 配置文件
 */
module.exports = {
    mysql: {
        host: '127.0.0.1',
		user: 'user',
		password: 'password',
		database: 'database',
    }
}
```
配置数据库信息

## mvc文件结构搭建
- 数据库层  
创建models目录，创建model.js文件  

```
const express = require('express');
const mysql = require('mysql');
const appConfig = require("../configs/app.config");

const query = function(sql, data, callback) {
	// 创建连接池
	mysql.createPool(appConfig.mysql).getConnection(function(err, connection) {
		connection.query(sql, data, function(err, rows, fields) {
			var result = {
				err: err, 
				rows: rows,
				fields: fields
			}
			callback(result);
		});
	});
}

module.exports = {
	// 封装查询语句
	selectQuery: function(selectFields, fromTable, hasWhere, whereData, callback) {
		var sql, data;
		if (hasWhere) {
			sql = 'SELECT ' + selectFields + ' FROM ' + fromTable + ' WHERE ?';
		} else {
			sql = 'SELECT ' + selectFields + ' FROM ' + fromTable;
		}
		query(sql, whereData, function(result) {
			callback(result);
		});
	}
}

```
创建user模型，创建user.model.js  
```
const model = require('../models/model');
// 定义表名
const tableName = 'users';

module.exports = {
	// 继承model.js进行数据库交互
	getAll: function(data, callback) {
		var selectFields = '*';
		var	fromTable = tableName;
		var hasWhere = false;
		model.selectQuery(selectFields, fromTable, hasWhere, null, function(result) {
			callback(result);
		})
	},
}
```
- 逻辑层  
创建controllers目录，创建user.controller.js文件  

```
const express = require('express');
// 引用user模型
const userModel = require('../models/user.model');

module.exports = {
	// 处理数据，返回结果
	getList: function(req, callback) {
		queryData = JSON.stringify(req.query);
		console.log(queryData);
		var data = null;
		userModel.getAll(data, function(result) {
			var list = result.rows;
			callback(list);
		})
	},
}
```
- 路由(因为是API的开发，所以没有模版层，直接通过路由输出json)  
在routes/users.js中添加一下路由  

```
// 引用userController
const userController = require('../controllers/user.controller');

// 获取用户列表数据
router.get('/getList', function(req, res, next) {
  	userController.getList(req, (result) => {
		res.json(result);
	});
});
```
这样就搭建成MVC的基本文件结构了。  


## 源码  
👉[源代码](https://github.com/polo-Huang/nodeJs-express-mysql-mvc)，欢迎提出问题与建议。🙏点击`star` 请多指教!