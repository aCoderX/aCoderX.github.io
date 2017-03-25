---
title: jsonp
date: 2016-10-08 21:57:02
tags: [javascript,跨域]
---
### jsonp客户端
```javascript
//通过script实现jsonp
function CreateScript(src) {
	$("<script><//script>").attr("src",src).appendTo("body")
}
//回调函数
function JSONcallback(data) {
	console.log(data);
}
//点击按钮触发
$("#getOtherDomainThings").click(function() {
	CreateScript("http://localhost:8080/JSONP/getJSON?callback=JSONcallback");
});
```
 <!--more-->
### jsonp服务端
```java
String cb = request.getParameter("callback");
String json="{'id':'1','name':'wang'}";
Writer out=response.getWriter();
out.write(cb+"(");
out.write(json);
out.write(");");
```
***
1. 只能处理get请求
2. 需要服务端改动
3. 只能用于js
