---
title: instanceof
date: 2019-08-31 04:31:40
tags: javascript
---
## instanceof操作符

### 描述

instanceof运算符用于测试构造函数的prototype属性是否出现在**对象**的原型链中的**任何**位置

### 语法

语法：*object* instanceof *constructor*

### 代码示例

``` javascript
// 定义构造函数
function C(){};
function D(){};

var o = new C();


o instanceof C; // true，因为 Object.getPrototypeOf(o) === C.prototype


o instanceof D; // false，因为 D.prototype不在o的原型链上

o instanceof Object; // true,因为Object.prototype.isPrototypeOf(o)返回true
C.prototype instanceof Object // true,同上

C.prototype = {};
var o2 = new C();

o2 instanceof C; // true

o instanceof C; // false,C.prototype指向了一个空对象,这个空对象不在o的原型链上.

D.prototype = new C(); // 继承
var o3 = new D();
o3 instanceof D; // true
o3 instanceof C; // true 因为C.prototype现在在o3的原型链上
```
