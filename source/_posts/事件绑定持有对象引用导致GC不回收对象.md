---
title: 事件绑定持有对象引用导致GC不回收对象
date: 2016-12-29 12:58:20
tags: javascript
---

### 现象
封装了一个部门选择框对象，在第一次创建选择框的时候是正确的，但是在关闭之后再次创建，发现点击事件被调用两次，于是console.log()，发现第一次创建的选择框的数据也被打印了一次，执行两次分别是执行了第一次本该销毁掉的选择框对象的方法和刚创建的选择框对象的方法。

<!--more-->
### 解决
想了想，会不会是事件绑定持有了对象的引用导致了对象不被GC。
于是动手：
```html
<button id="test">test</button>
<button id="bind">bind</button>
```
```javascript
function A(num){
	this.test=function(){
		console.log(num);
	}
}
$("#test").click(function(event) {
	var a = new A(Math.random());
	$("#bind").click(function(event) {
		a.test();
	});
});
```

点击两次test，然后点击bind，果然打印了两次,而且num不一样。

想了一想，只要在第二次绑定之前把之前的事件解绑了就行了。于是在绑定之前加了：
```javascript
$("#bind").off("click");
```
果然OK了，但是虽然现象是解决的，不知道这个对象是不是真的被销毁了，于是用chrome的profiles看了下。

### 验证

[使用Chrome Profiles](http://www.programering.com/a/MzM0cjMwATQ.html)

> F12开发者选项，选择Profiles选项卡，选择Take Heap Snapshot，点击Take Snapshot即可拍下当前JS的heap快照，每次执行之前都会强制执行一次GC。

#### 未设置off

1. 点击test，创建一个A的对象，并绑定bind的点击事件，拍下快照
![image](http://oe9o9e68g.bkt.clouddn.com/before_off_1.png)

2. 再次点击test，拍下快照
![image](http://oe9o9e68g.bkt.clouddn.com/before_off_2.png)
发现之前的对象没有销毁，并且都有一个引用被事件持有。

3. 查看bind按钮的绑定事件
![image](http://oe9o9e68g.bkt.clouddn.com/before_off_event.png)
可见bind按钮的绑定事件有两个。

#### 设置off

1. 点击test，拍下快照
![image](http://oe9o9e68g.bkt.clouddn.com/after_off_1.png)

2. 再次点击test，拍下快照
![image](http://oe9o9e68g.bkt.clouddn.com/after_off_1.png)

3. 查看bind按钮的绑定事件
![image](http://oe9o9e68g.bkt.clouddn.com/after_off_event.png)

发现之前的对象已经销毁 >.<。

### 结语
JS的灵活性既是优点也是缺点，平时写代码时要注意内存泄漏的问题。当代码量非常庞大的时候，就不能仅靠复查代码来排查问题，必须要有一些监控对比工具来协助排查。
