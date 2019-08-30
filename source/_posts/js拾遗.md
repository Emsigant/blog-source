---
title: js拾遗
date: 2019-08-30 00:28:02
tags: javascript
---
## 对象深拷贝（详尽的可以参考[lodash baseClone的实现](https://github.com/lodash/lodash/blob/master/.internal/baseClone.js)）

需要考虑的点：

- 是否有循环引用
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
                continue;
            }
        }
        newValue[keys[i]] = clone(prop, srcStack, dstStack);
    }
    return newValue;
}
```
