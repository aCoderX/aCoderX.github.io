---
title: requireJS
date: 2016-11-21 21:04:11
tags: javascript
---
### JavaScript的模块化编程
> 随着网页项目规模的增大，JavaScript模块化编程已经成为了迫切的需求。

#### 原始写法
```javascript
function f1(){}
function f2(){}
function f3(){}
```
几个函数组成一个模块

* 缺点：污染全局变量，代码多的时候会变的难以阅读，难以维护
 <!--more-->
#### 对象写法
```javascript
var model = new Object({
    f1:function(){
    },
    f2:function(){
    },
    name:"test"
})
```
* 缺点：暴露的模块中的所有属性，外部可以任意修改内部属性
```javascript
model.name="newName";
```

#### 立即执行函数写法
```javascript
var model = (function(){
    var name="test";
    f1:function(){
    },
    f2:function(){
    }
    return {
        "f1":f1,
        "f2":f2
    }
})();
```

### 模块化规范
#### CommonJS
> 服务端（nodeJs）的模块系统

由于nodejs的出现，javascript也能开始编写服务端代码，而服务端代码一定要有模块，于是参照CommonJS规范实现的Nodejs的模块系统

* 缺点：在服务端，由于所有模块都存放在本地磁盘，所以可以同步完成，只需要等待硬盘读取文件的时间。但是，对于浏览器来说，模块都在服务端，需要通过网络加载到本地，如果使用CommonJS规范，可能需要等待很长时间，这段时间浏览器处于假死状态。

#### AMD(Asynchronous Module Definition)
基于回调函数
```javascript
define(['jquery'],function($){
});
```
库：requireJS

第一个参数是要依赖的js模块，第二个参数是加载完第一个参数指向的模块后执行的回调函数。

#### CMD(Common Module Definition)
依赖就近
```javascript
define(function(require, exports, module) {
var a = require('./a')
})
```
库：seaJS

### requireJS
#### 入口
```html
<script src="../js/lib/requirejs/require.js" data-main="../js/init.js"></script>
```
data-main 属性指向主模块。

#### 配置
```javascript
require.config({
	paths:{
		"jquery":"lib/jquery/dist/jquery.min",
		"test":"app/test"
	}
});
配置模块

```
在主模块中定义

#### 模块的定义
定义一个不依赖其他模块的模块
```javascript
//test模块
define(function(){
	var friendInfo={"test":"testname"};
	
	return {
		getAllInfo:function(){
			return friendInfo;
		}
	};
})
```
定义一个有依赖的模块
```javascript
define(['jquery'],function($){
	console.log($("#test").html());
})
```

#### 引用模块
```javascript
requirejs(['jquery'],function($) {
    console.log($("#test").html());
});
```
引用上面的定义的模块
```javascript
requirejs(['test'],function(test) {
    console.log(test.getAllInfo());
});
```