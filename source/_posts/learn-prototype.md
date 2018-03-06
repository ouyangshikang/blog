---
title: 原型链的理解
tags:
  - JavaScript
  - 面试题
date: 2017-05-10 23:26:27
---
### 先给出个栗子吃
``` javascript
function Person(name){
    this.name = name;
}
Person.prototype.sayName = function(){
    console.log('My name is :' + this.name);
}
var p = new Person("KK")
p.sayName();
```
#### 我们解释下 Person、p、prototype、proto、constructor之间的关系哦
1. ``Person``是构造函数，``p``是``Person``的一个实例
2. 所有的对象都有``__proto__``属性,只有函数有``prototype``
3. 每个函数都有自己的原形对象 ``prototype``， 而``prototype``中有``__proto__``和``constructor``, ``constructor``顾名思义就是构造函数,``__proto__``就是是实例的原型链
4. 在实例中，``__proto__``可看做是key,``prototype``可看做是value。xxx.proto === yyy.prototype

#### 那么，在这个栗子中,可以得出这么些结论:
``` javascript
Person.prototype.constructor == Person //true
p.__proto__ === Person.prototype //true
Person.prototype.__proto__ === Object.prototype //true
Object.prototype.constructor == Object,
Object.prototype.__proto__ == null
```

### 原型链的概念理解：
> 使用一个构造函数创造一个实例对象，在此对象上调用相应的属性和方法时，JS存在一个搜索机制，首先查找它本身有没有，如果没有，则顺着``__ proto``__这个指针去找它的构造函数的原型上有没有，如果没有，再顺着原型的``__ proto__``向上去找，也就是说，只要存在``__ proto__``这个指针，在没有找到对应的属性与方法时，查找不会停下，直到没有``__ proto__``为止，这样的一种形式可行的结构基础就叫 原型链 。

#### 再给张图就更完美了：
![](http://ovc5dgvpb.bkt.clouddn.com/QQ%E6%88%AA%E5%9B%BE20170512113830.png)

### 关于原型的其他知识点
#### instanceOf
作用：用来检测一个对象的类型     
- ``instanceof``运算符用来测试一个对象在其原型链中是否存在一个构造函数的``prototype``属性    
**A instanceof B**
> 表示A的的原型链中是否存在 B.prototype ： 有就return true,反之return false

``` javascript
function Car(make, model, year) {
  this.make = make;
  this.model = model;
  this.year = year;
}
var mycar = new Car("Honda", "Accord", 1998);
var a = mycar instanceof Car;   
// 返回 true  var b = mycar instanceof Object; // 返回 true
var simpleStr = "This is a simple string";
var myString  = new String();
simpleStr instanceof String; 
// returns false, 检查原型链会找到 undefined，simpleStr不是对象
myString  instanceof String; 
// returns true
```
#### Object.create()
- ``Object.create()`` 方法使用指定的原型对象和其属性创建了一个新的对象    
语法：``Object.create(proto, [ propertiesObject ])``  
// 第一个参数为一个对象，应该是新创建的对象的原型。   
作用： 创建一个新的对象,第一层原型链指向对应的参数 
``` javascript
var a = {'age': 20}
var b = {'name':'Jack'}
b = Object.create(a)
b.__proto__ === a //true
```

