---
title: js数值精度问题及es6数值的拓展
tags:
  - javascript

date: 2018-03-1 22:20:35
---
***
### js的数值精度问题
> 在js内部，整数和浮点数采用同样的存储方法(64位双精度浮点数)，所以有``66 === 66.00``。

在涉及小数的运算时要注意数值精度的问题,举个人尽皆知的栗子🌰
``` javascript
0.1 + 0.2 === 0.3 // false
0.1 + 0.2  // 0.30000000000000004
```
JavaScript采用国际标准 *IEEE 754*，JavaScript浮点数的64个二进制位，从最左边开始，是这样组成的:
- 第1位：符号位，0表示正数，1表示负数
- 第2位到第12位(11)：指数部分
- 第13位到第64位(52)：小数部分（即有效数字）

![](https://st-gdx.dancf.com/gaodingx/73047376/design/20200426-173521-8918.png)

- **``符号位``决定了一个数的正负，``指数部分``决定了数值的大小，``小数部分``决定了数值的精度**

> 国际标准IEEE 754 规定，有效数字第一位默认总是1，不保存在64位浮点数之中。也就是说，有效数字总是``1.xx...xx``的形式，其中xx..xx的部分保存在64位浮点数之中，最长可能为52位。因此，JavaScript 提供的有效数字最长为``53``个二进制位。

  所以，一个数在 JavaScript 内部实际的表示形式为:
![](https://st-gdx.dancf.com/gaodingx/73047376/design/20200426-174023-4f23.png)

- 精度最多只能到53个二进制位，这意味着，绝对值小于2的53次方的整数，即**-(2^53-1) ~  2^53-1**，都可以精确表示。
``` javascript
Math.pow(2, 53); // 9007199254740992
Math.pow(2, 53) + 1; //9007199254740992
Math.pow(2, 53) + 2;  // 9007199254740994
9007199254740992222 // 9007199254740992000
```
  大于2的53次方后，计算结果就开始混乱了，无法保持精度,并且多出的三个有效位无法保存，变成0。

***
### ES6中2进制和8进制的表示
- 二进制数值：前缀``0b/0B``
- 八进制数值：前缀``0o/0O``
``` javascript
console.log('二进制表示法:', 0b00010 === 2); // true
console.log('八进制表示法:', 0o0340 === 224); // true
```
> 将各进制转为10进制用Number()/ parseInt()方法,将10进制转化为各进制可以用toString(n)方法

***
### Number对象拓展
es6在Number对象上，新增了2个方法。
- **Number.isFinite()**   检查数值是否是有限的，如果不是数值，返回false
``` javascript
console.log(Number.isFinite(15)); // true
console.log(Number.isFinite(true)); // false
console.log(Number.isFinite(0.8)); // true
console.log(Number.isFinite(NaN)); // false
console.log(Number.isFinite('string')); // false
console.log(Number.isFinite(Infinity)); // false
```

- **Number.isNaN()**   检测数值是否为NaN， 如果不是数值，也返回false
``` javascript
console.log(Number.isNaN(15)); // false
console.log(Number.isNaN(true)); // false
console.log(Number.isNaN('NaN')); // false
console.log(Number.isNaN(NaN)); // true
console.log(Number.isNaN(0.4)); // false
```
> 它们与传统的全局方法***isFinite()***和***isNaN()***的区别在于，传统方法先调用Number()将非数值的值转为数值，再进行判断。而这两个新方法``只对数值有效``。 先进行是否是数值的判断，是数值再进行具体判断。

-  **ES6将全局方法parseInt()和parseFloat()移植到Number对象上，其他都保持不变**

- **Number.isInteger()** 判断一个数值是否为整数
``` javascript
console.log(Number.isInteger(66)); // true
console.log(Number.isInteger(66.0)); // true
console.log(Number.isInteger(66.6)); // false
console.log(Number.isInteger(null)); // false
console.log(Number.isInteger('nice')); // false
```
- **Number.EPSILON**  它表示 1 与大于 1 的最小浮点数之间的差即2的-52次方,这是``JavaScript能够表示的最小精度``
``` javascript
console.log(Number.EPSILON === Math.pow(2, -52)); // true
```