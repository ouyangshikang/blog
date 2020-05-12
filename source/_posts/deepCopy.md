---
title: javascript中的深拷贝和浅拷贝
tags:
  - javascript

date: 2017-05-21 23:12:06
---
### 区别
#### 浅拷贝
- 对于字符串类型，浅拷贝是对值的复制;
- 对于对象或数组来说，浅拷贝是对``对象地址的复制``，并没有开辟新的栈。就是将一个对象的内存地址的“编号”拷贝给另一个对象。``只复制一层对象的属性``.

#### 深拷贝
- 实现原理: 先新建一个空对象，``内存中新开辟一块地址``，把被复制对象的``所有可枚举的(注意可枚举的对象)属性方法``一一复制过来，注意要用递归来复制子对象里面的所有属性和方法，直到子子…..属性为基本数据类型。

#### 总结
> 深拷贝理解两点:1. 新开辟内存地址，2,递归来刨根复制。

### 实现
#### 浅拷贝
在Javascript 中，将一个对象赋值给一个变量其实只是将这个对象的引用拷贝了一份
假设这么一段代码
``` javascript
var man = {
     name: 'aepkill',
     sex: 'male'
};
var superMan = man;
```
![](https://st-gdx.dancf.com/gaodingx-dev/73047376/video-cliper/20200426-173241-ba43.png)
> 并没有创建一个新的对象实体，而是仅仅拷贝了这种引用关系。
所以假设执行 man.name = ‘aotuman’ ，那么superMan.name的值也会变成 ‘aotuman’，因为其实他们引用的是同一个对象实体。

很多场景下并不希望出现像上面中那样， 我们希望能使两个变量相互独立，不再引用同一个对象
这很容易实现，只需要申明一个新的对象，然后将这个对象的所有字段原封不动拷贝过去即可：

``` javascript
function copy(obj){
     //创建一个新对象
     let newObj = {};
     //返回keys数组，包含obj所有可枚举的自身属性名
     let keys = Object.keys(obj),key=null;
     for(let i =0;i<keys.length;i++){
          key = keys[i];
          //将obj所有属性复制到新对象
          newObj[key] = obj[key];
     }
     return newObj;
}
var people = {
     name:'java',
     age:22
}
var hero = copy(people);
people.name = 'javascript';
console.log(people.name); //javascript
console.log(hero.name); // java
```
两个变量已经不在引用同一个对象，二者的修改是相互独立的


#### 深拷贝
上面已经完成了基本的拷贝，但是只拷贝了第一层的关系，如果对象不止一层的话，上面的函数就不适用了：

``` javascript
var people = {
     name: 'java',
     age: 22,
     concat:{
          tel:'1568552256',
          qq:'54666651'
     }
}
var hero = copy(people);
people.concat.tel = '1111111111';
console.log(people.concat.tel); //1111111111
console.log(hero.concat.tel); // 1111111111
```
因为只拷贝了一层，所以people 与 hero 中的concat 字段还是引用同一个对象，所以当执行 people.concat.tel = ‘1111111111’ 后，hero .concat.tel 也变成了 ‘1111111111 ‘ ，该如何解决这个问题呢？

> 此时，需要对这个问题进行梳理并抽象：定义一个函数deepCopy，该函数遍历传入的对象，如果该字段的值不是一个对象则可直接赋值给新对象，否则对该字段用函数deepCopy进行递归操作。

``` javascript
function deepCopy(obj) {
     let newObj = {}
     let keys = Object.keys(obj),
                    key = null,
                    temp = null;
     for (let i = 0; i < keys.length; i++) {
          key = keys[i];
          temp = obj[key];
          // 如果字段的值也是一个对象则递归操作
          if (temp && typeof temp === 'object') {
               newObj[key] = deepCopy(temp);
          } else {
               // 否则直接赋值给新对象
               newObj[key] = temp;
          }
     }
     return newObj;
}
var people = {
     name: 'java',
     age: 22,
     concat: {
          tel: '1568552256',
          qq: '54666651'
     }
}
var hero = deepCopy(people);
people.concat.tel = '1111111111';
console.log(people.concat.tel);//1111111111
console.log(hero.concat.tel);// 1568552256
```
OK,deepCopy 函数可以拷贝任意深度的对象了


