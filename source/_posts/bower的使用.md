---
title: bower的使用
date: 2016-10-25 18:04:39
tags: 前端
---
### bower介绍
> Bower是一个客户端技术的软件包管理器，它可用于搜索、安装和卸载如JavaScript、HTML、CSS之类的网络资源。其他一些建立在Bower基础之上的开发工具，如YeoMan和Grunt。

了解java开发的一定知道Maven，Bower就是类似于Maven的包管理器，可以轻松的管理js等文件，也可管理不同库之间的依赖。
 <!--more-->
### 前提准备
为了安装bower，首先需要安装：
* Node
* NPM：node的程序包管理器。捆绑在nodejs安装程序上。
* Git：bower会通过git从远程仓库获取包。

### 安装bower
通过node的npm来安装bower
```
//-g为全局安装
npm install -g bower
```

### 使用bower
先来看看bower的所有命令。输入**bower help**查看
```
cache:bower缓存管理
help:显示Bower命令的帮助信息
home:通过浏览器打开一个包的github发布页
info:查看包的信息
init:创建bower.json文件
install:安装包到项目
link:在本地bower库建立一个项目链接
list:列出项目已安装的包
lookup:根据包名查询包的URL
prune:删除项目无关的包
register:注册一个包
search:搜索包
update:更新项目的包
uninstall:删除项目的包
```
#### 生成bower.json文件（可跳过）
输入**bower init**，之后一直回车，会发现生成了bower.json文件

#### 包的安装
Bower是一个软件包管理器，所以你可以在应用程序中用它来安装新的软件包。举例来看一下来如何使用Bower安装JQuery，在你想要安装该包的地方创建一个新的文件夹，键入如下命令：
```
//--save表示写入bower.json，可不写
bower install jquery --save
```
之后会发现所在文件夹生成了**bower_components**的文件夹

#### 包的使用
之后就可以在html文件之中引用jquery了
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Bower</title>
</head>
<body>
<div class="test"></div>
<script src="../bower_components/jquery/dist/jquery.min.js"></script>
<script>
    $('.test').html("hahah");
</script>
</body>
</html>

```


#### 修改默认生成目录
刚刚生成了默认的bower_components文件夹，如果你对这个默认的名字无法忍受，可以在项目根目录新建.bowerrc文件，编辑内容：
```
{
  "directory":"js/lib"
}
```
更多配置可查看 https://github.com/bower/spec/blob/master/config.md


还有一些list、search命令可自行发挥、也可去[Bower官网](https://bower.io/)查看