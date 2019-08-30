---
title: typeof
date: 2019-08-31 04:31:50
tags: javascript
---
## typeof操作符

### 描述

typeof 操作符返回一个字符串，表示未经计算的操作数的类型

### 语法

typeof *operand* 或者 *typeof(operand)*

### 概括

类型|结果
--|--
Undefined|    "undefined"
Null|   "object"
Boolean|    "boolean"
Number| "number"
BigInt| "bigint"
String| "string"
Symbol (ECMAScript 2015 新增)|  "symbol"
宿主对象（由 JS 环境提供）| 取决于具体实现
Function 对象 (按照 ECMA-262 规范实现 [[Call]])|    "function"
其他任何对象|   "object"

### 代码示例

``` javascript
// 数值
typeof 37 === 'number';
typeof 3.14 === 'number';
typeof(42) === 'number';
typeof Math.LN2 === 'number';
typeof Infinity === 'number';
typeof NaN === 'number'; // 尽管它是 "Not-A-Number" (非数值) 的缩写
typeof Number(1) === 'number'; // Number 会尝试把参数解析成数值

typeof 42n === 'bigint';


// 字符串
typeof '' === 'string';
typeof 'bla' === 'string';
typeof `template literal` === 'string';
typeof '1' === 'string'; // 注意内容为数字的字符串仍是字符串
typeof (typeof 1) === 'string'; // typeof 总是返回一个字符串
typeof String(1) === 'string'; // String 将任意值转换为字符串，比 toString 更安全


// 布尔值
typeof true === 'boolean';
typeof false === 'boolean';
typeof Boolean(1) === 'boolean'; // Boolean() 会基于参数是真值还是虚值进行转换
typeof !!(1) === 'boolean'; // 两次调用 ! (逻辑非) 操作符相当于 Boolean()


// Symbols
typeof Symbol() === 'symbol';
typeof Symbol('foo') === 'symbol';
typeof Symbol.iterator === 'symbol';


// Undefined
typeof undefined === 'undefined';
typeof declaredButUndefinedVariable === 'undefined';
typeof undeclaredVariable === 'undefined';


// 对象
typeof {a: 1} === 'object';

// 使用 Array.isArray 或者 Object.prototype.toString.call
// 区分数组和普通对象
typeof [1, 2, 4] === 'object';

typeof new Date() === 'object';
typeof /regex/ === 'object'; // 历史结果请参阅正则表达式部分


// 下面的例子令人迷惑，非常危险，没有用处。避免使用它们。
typeof new Boolean(true) === 'object';
typeof new Number(1) === 'object';
typeof new String('abc') === 'object';

// 函数
typeof function() {} === 'function';
typeof class C {} === 'function';
typeof Math.sin === 'function';

// null JavaScript 诞生以来便如此
typeof null === 'object';

// 当前所有的浏览器都暴露了一个类型为 undefined 的非标准宿主对象 document.all
typeof document.all === 'undefined';

// 在es6引入let和const之后 typeof也会报错
typeof undeclaredVariable === 'undefined';
typeof newLetVariable; // ReferenceError
typeof newConstVariable; // ReferenceError
typeof newClass; // ReferenceError
var undeclaredVariable;
let newLetVariable;
const newConstVariable = 'hello';
class newClass{};
```
