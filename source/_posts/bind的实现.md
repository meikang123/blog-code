---
title: bind的实现
date: 2021-02-28 13:03:53
categories:
- JS代码实现
tags:
---

### call、apply、bind的区别和用法
都是来改变函数执行上下文，其实就是改变函数运行时的this

#### 1、用法
```js
const obj = {name: 'mk'}
const obj2 = {
    name: 'mk2',
    fun: function (a, b) {
        console.log(this.name)
        return a + b
    }
}

obj2.fun(1, 2)// mk2 return 3
obj2.fun.call(obj, 1, 2) // mk return 3
obj2.fun.apply(obj, [1, 2]) // mk return 3
obj2.fun.bind(obj, 1)(2) // mk return 3
```

#### 2、区别
- call、apply与bind的差别
> call和apply改变了函数的this上下文后便执行了该函数，而bind则返回改变上下文this的一个函数
- call、apply的区别
> 他们的差别在于参数的区别，
> call和apply第一个参数都是改变函数上下文对象的，
> 而call从第二个参数开始以列表的形式展示，
> apply参数是放在一个数组里面的

### 3、应用
- 将伪数组转为数组
```js
// call
function fun() {
    const arr = Array.prototype.slice.call(arguments)
    console.log(arr)
}

fun(1, 2, 3, 4)

// apply
const arr1 = [1, 2, 3]
const arr2 = [4, 5, 6]
Array.prototype.push.apply(arr1, arr2)
console.log(arr1) // [1, 2, 3, 4, 5, 6]

// 类型判断
function isType(type) {
    return function(obj) {
        return Object.prototype.toString.call(obj) === `[object ${type}]`
    }
}

const isNumber = isType('Number')
console.log(isNumber(2)) // true
```

### 4、bind的实现方式
绑定过后的函数被new实例化之后，需要继承原函数的原型链方法，且绑定过程中提供的this被忽略（继承原函数的this对象），但是参数还是会使用。
```js
if(Function.prototype.bind) {
    Function.prototype.bind = function(toThis) {
        const tThis = this;
        const Fun = function() {}
        const arr = Array.prototype.slice.call(arguments, 1)
        const bFun = function () {
            return tThis.apply(
                this instanceof Fun && toThis ? this : toThis || window, 
                arr.concat(Array.prototype.slice.call(arguments))
            )
        }
        Fun.prototype = tThis.prototype;
        bFun.prototype = new Fun();
        return bFun
    }
}
```