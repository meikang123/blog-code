---
title: 'new运算符实现'
date: 2021-02-28 14:24:37
categories:
- JS代码实现
tags:
---

#### 1、原理
- 原型（prototype）:一个简单的对象，用于实现对象的属性继承。可以简单理解成对象的父亲。在火狐和谷歌中，每个js对象中都包含一个__proto__(非标准)的属性指向它爹（该对象原型），可obj.__proto__进行访问。
- 构造函数：可以  通过new来新建一个对象  的函数
- 实例：通过 构造函数 和 new 创建出来的 对象，就是实例。实例通过__proto__指向原型，通过constructor指向构造函数。
```js
 function Father() {
    let colors = [1,2,3];
    return colors;
 }
const instance = new Father();
// 此时，instance为实例，构造函数为Father，构造函数拥有一个prototype的属性指向原型
```

```text
实例.__proto__===原型
原型.constructor === 构造函数
构造函数.prototype === 原型
实例.constructor === 构造函数 
```

#### 2、过程
- 新生成一个对象
- 链接到原型
- 绑定this
- 返回新对象（如果构造函数有自己 return 时，则返回该值）

#### 3、实现
```js
function newFun(fn) {
    const obj = Object.create(fn.prototype);
    const arg = Array.prototype.slice.call(arguments, 1);
    const result = fn.apply(obj, arg)
    return typeof result === 'object' ? result : obj
}

function Fun(name) {
    this.name = name;
}
function Fun2(name) {
    this.name = name;
    return {
        name: 'mk2'
    }
}

const o1 = new Fun('mk');
const o2 = new Fun2('mk');
const o3 = newFun(Fun, 'mk');
const o4 = newFun(Fun2, 'mk');
console.log(o1, o2, o3, o4); // {name: mk}, {name: mk2}, {name: mk}, {name: mk2}
```
