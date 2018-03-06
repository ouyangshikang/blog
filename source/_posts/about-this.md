---
title: 搞懂this及相关问题
tags:
  - JavaScript
  - 面试题
date: 2017-05-12 22:49:39
---

### this的使用场合
1. 全局环境下使用，指的是顶层对象 ``window``
2. 构造函数中的this，指的是实例对象
3. 对象的方法 当A对象的方法赋予给另一个B对象，该方法中的this指向就变成B

### 照常举个栗子:
``` javascript
let obj = {
    foo() {
        console.log(this);
    }
}
let bar = obj.foo;
obj.foo() // 打印出的 this 是 obj
bar() // 打印出的 this 是 window
```
为什么呢？       
解决这类问题需要理解函数的调用问题，js里有三种函数的调用方式：    
- func(a,b);
- obj.child.method(a,b);
- func.call(context,a,b);    
func.apply(context,[a,b]); //context为函数执行环境的上下文    

但其实前两种都可以转化为第三种(以call的形式)：

- func.call(undefine, a,b);
- obj.child.method.call(obj.child,a,b);

所以，栗子可以吃了:       
```javascript
obj.foo(); ----->   obj.foo.call(obj);
bar();     ----->  bar.call(undefined);
```
> 在浏览器中，如果context传入的值为undefined，null, 还有空值 ，那么默认的就是window

- 当然，如果你想让bar()的context指向obj,那就直接bar.call(obj),指定当前context(函数内部的this)绑定到obj上

> 总结一句话：函数在哪里被调用，this就指向哪里

---
### 绑定this的方法(call,apply,bind的区别)

1. call与apply两者对于参数的接受方式不同,call接受的是参数列表，apply接受的是一个包含多个参数的数组
- apply的语法： ``fun.apply(thisArg[, argsArray])``   
如xxx.apply(undefined, [1,2,3])
- call的语法：``fun.call(thisArg[, arg1[, arg2[, ...])``   
如xxx.call(undefined, 1,2,3])

2. ``bind``和call,apply的作用一样，动态指定this,不同在于返回值,bind可以强制指定this到某个对象，还可以绑定原函数的参数,然后返回一个新函数，之后不会修改，即预设了对象的this和参数
3. 看下面bind的demo
```javascript
var counter = {
  count: 0,
  inc: function () {
    this.count++;
  }};
counter.count // 0
counter.inc()counter.count // 1

/**
 * counter.inc内部的this，默认指向counter对象。
 * 如果将这个方法赋值给另一个变量，就会出错,如下：
 */
 
var counter = {
  count: 0,
  inc: function () {
    this.count++;
  }};
var func = counter.inc;
func();
counter.count // 0
count // NaN

/** 函数func是在全局环境中运行的，这时inc内部的this指向顶层对象window，
 *  所以counter.count是不会变的，反而创建了一个全局变量count。
 *  因为window.count原来等于undefined，进行递增运算后undefined++就等于NaN。
 */

// 可以使用bind方法，将inc内部的this绑定到counter对象
var func = counter.inc.bind(counter);
func();
counter.count // 1
```
绑定原函数的参数这里不再赘述。

---

###  关于this的题目：

``` javascript
var john = {
     firstName: "John"
 };
function func() {
     alert(this.firstName + ": hi!")
};
 john.sayHi = func;
 john.sayHi();  //john.sayHi()的this指向john因此输出"John:hi!"
```
```javascript
document.addEventListener('click', function(e){
    console.log(this);// this指向document
    setTimeout(function(){
        console.log(this);//this指向window
    }, 200);
}, false);
//分别输出document和window
```
```javascript
var john = {
  firstName: "John";
};
function func() {
  alert( this.firstName )
};
func.call(john);
//输出John,因为func.call(john)将func的this指向了john对象
```
```javascript
var module= {
  bind: function(){
    var _this = this   //将当前this保存
    $btn.on('click', function(){
      console.log(_this) //this指向$btn
      _this.showMsg();//$btn没有showMsg()方法
    })
  },
  showMsg: function(){
    console.log('nice');
  }
}
```