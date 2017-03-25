---
title: js面向对象
date: 2016-10-08 10:11:29
tags: javascript
---
### 字面式 (literal notation) 对象声明

```javascript
var person = {
    name: "张三",
    age: 26,
    gender: "男",
    eat: function(stuff) {
        alert("我在吃" + stuff);
    }
};
person.height = 176;
delete person["age"];
```
 <!--more-->
### 使用构造器（构造函数）(constructor) 创建对象
(创建高级对象构造有两种方式：使用“this”关键字构造、使用原型prototype构造。)

PS：当访问对象的属性或者方法是，将按照搜索原型链prototype chain的规则进行。首先查找自身的静态属性、方法，继而查找构造上下文的可访问属性、方法，最后查找构造的原型链

“this”与“prototype”定义的另一个不同点是属性的占用空间不同。使用“this”关键字，实例初始化时为每个实例开辟构造方法所包含的所有属性、方法所需的空间，而使用“prototype”定义，由于“prototype”实际上是指向父级的一种引用，仅仅是个数据的副本，因此在初始化及存储上都比“this”节约资源。
```javascript
//使用this关键字定义构造的上下文属性 
function Girl() 
{ 
this.name = "big pig"; 
this.age = 20; 
this.standing; 
this.bust; 
this.waist; 
this.hip; 
} 

//使用prototype 
function Girl(){} 
Girl.prototype.name = "big pig"; 
Girl.prototype.age = 20; 
Girl.prototype.standing; 
Girl.prototype.bust; 
Girl.prototype.waist; 
Girl.prototype.hip; 
alert(new Girl().name); 

function Person() {}
Person.prototype = {
    name: “张三”,
    age: 26,
    gender: “男”,
    eat: function(stuff) {
        alert(“我在吃” + stuff);
    }
}
var p = new Person();


推荐使用的创建对象方式
  混合的构造函数
function Parent() {
    this.name = "李小龙";
    this.age = 32;
};
Parent.prototype.lev = function() {
    return this.name;
};
var x = new Parent();
alert(x.lev());
```
1. 该模式是指混合搭配使用构造函数方式和原型方式
2. 将所有属性不是方法的属性定义在函数中（构造函数方式）将所有属性值为方法的属性利用prototype在函数之外定义（原型方式）




### 对象的 __proto__属性和隐式引用
```javascript
function Person(name) {
    this.name = name;
}
var p = new Person();
console.log(p.__proto__ === Person.prototype);  （true）
console.log(Person.prototype.__proto__ === Object.prototype);  （true）
console.log(Person.__proto__ === Function.prototype);  （true）
```

### 利用原型链Horse->Mammal->Animal 实现继承
```javascript
function Animal() { } 
Animal.prototype = { 
name: animal",
weight: 0,
eat: function() {
    alert("Animal is eating!");
}
}
Mammal() {
    this.name = "mammal";
} 
Mammal.prototype = new Animal(); 
function Horse( height,weight) {
    this.name = "horse";
    this.height = height;
    this.weight = weight;
} 
Horse.prototype = new Mammal();
Horse.prototype.eat = function() {
    alert("Horse is
        eating grass!");
}
 var horse = new Horse( 100, 300 ); 
console.log(horse.__proto__ === Horse.prototype);
console.log(Horse.prototype.__proto__ === Mammal.prototype);
console.log(Mammal.prototype.__proto__ === Animal.prototype);
```

### 使用闭包实现信息隐藏
（匿名函数本身也是一个闭包，可以在函数外部对函数内部的局部变量进行操作。）

```javascript
function User( pwd ) { 
var password = pwd;
 function getPassword() { 
return password;
 }
 this.passwordService = function() {
 return getPassword();
        } 
}
User.prototype.checkPassword = function( pwd ) { 
return this.passwordService()=== pwd;
 };
var u = new User( "123456" ); 
// 打印 true 
console.log( u.checkPassword("123456" ) ); 
// 打印 undefined 
console.log( u.password ); 
// 打印 true 
console.log( typeof u.getPassword === "undefined" );
```