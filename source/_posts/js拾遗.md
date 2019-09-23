---
title: js拾遗
date: 2019-08-30 00:28:02
tags: javascript
---
## 对象深拷贝（详尽的可以参考[lodash baseClone的实现](https://github.com/lodash/lodash/blob/master/.internal/baseClone.js)）

需要考虑的点：

- 循环引用
- 特殊值的处理（null、undefined等等）

``` javascript
function isObject(v) {
    return Object.prototype.toString.call(v) === '[object Object]';
}

function isArray(v) {
    return Array.isArray(v);
}

function clone(value, srcStack, dstStack) {
    var newValue = isObject(value)
        ? Object.create(Object.getPrototypeOf(value))
        : isArray(value)
            ? []
            : undefined;
    if (newValue === undefined) {
        return value; // number boolean或者string直接返回value
    }
    var keys = Object.keys(value);
    var index = -1; // 用于搜索srcStack
    var prop = undefined;

    // srcStack和dstStack用于复制循环引用对象(a.a.a === a.a === a)
    srcStack = srcStack || [];
    dstStack = dstStack || [];
    srcStack.push(value);
    dstStack.push(newValue);

    for (var i = 0; i < keys.length; i++) {
        prop = value[keys[i]];
        if (prop === null || prop === undefined) {
            newValue[keys[i]] = prop;
        }
        else if (isObject(prop)) {
            index = srcStack.lastIndexOf(prop);
            if (index >= 0) {
                newValue[keys[i]] = dstStack[index];
                continue; // 跳过后续递归
            }
        }
        newValue[keys[i]] = clone(prop, srcStack, dstStack);
    }
    return newValue;
}
```

## Object.toString和Object.prototype.toString

- Object.toString是Object函数继承自Function的方法，返回`function Object() {[native code]}`
- Object.prototype.toString是Object的原型方法，返回`[object ToStringTag]`(可以通过Symbol.toStringTag修改)

## `==`、`===`和`Object.is`

### `==`

参见[MDN非严格相等](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Equality_comparisons_and_sameness#%E9%9D%9E%E4%B8%A5%E6%A0%BC%E7%9B%B8%E7%AD%89)

### `===`和`Object.is`

===和Object.is有两处不同：

``` javascript
//+0和-0
+0 === -0; // true
Object.is(+0, -0); // false

// NaN
NaN === NaN; // false
Object.is(NaN, NaN); // true
```

## `new.target`

new.target是用来判断函数是否是通过new关键字调用的。
new.target指向被调用的函数。new.target整体可以视为一个关键字，`new.`提供了一个“虚拟上下文”。
在箭头函数中，new.target指向最近的外层函数的new.target。

## Array.prototype

Array.prototype是一个**数组**，length为0，Array的原型方法均为该数组的方法，但该数组的__proto__仍然指向Object.prototype。Array.isArray()可以使用Object.prototype.toString.call()实现polyfill。
