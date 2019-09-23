---
title: Promise
date: 2019-09-13 11:12:18
tags:
---
## 语法

``` javascript
new Promise(function/* executor */(resolve, reject) {/*...*/});
```

Promise构造函数在执行时**立即调用executor**(executor函数在Promise构造函数返回所建promise实例对象前被调用)，executor的返回值会被忽略。

## 描述

一个Promise对象，其状态*只能*是pending、resolved或者rejected。当Promise的状态发生变化时，then绑定的handler就会被调用(then方法接受两个参数：onfulfilled和onrejected，但通常情况下，只传onfulfilled)。

![Promise示意图](https://mdn.mozillademos.org/files/8633/promises.png "Promise示意图")

## 属性

- Promise.length: Promise构造函数的参数个数，总是为1。
- Promise.prototype: Promise构造函数的原型对象。

## 方法

- Promise.all(iterable): 返回一个新的Promise对象，只有iterable里**所有**的promise成功时才会成功，任何一个promise失败都会触失败。成功时会返回一个数组，每一项对应iterable里的promise的成功返回值。失败时会取第一个失败的promise的错误信息。*注意*，iterable不仅仅只能是promise，也可以是任意其他值，比如：

  ``` javascript
  Promise.all([Promise.resolve(1), 2, 3]).then(resArr => console.log(resArr)); // [1,2,3]
  ```
  
  对于所有非thenable的值，都会直接将其resolve；thenable的值，则会根据以运行结果判断。当Promise.all的参数为[]或者空字符串时(或者定义了空的Symbol.iterator的任意值)时，返回一个*同步的*resolved的promise。如果iterable不包含任何thenable对象，则返回一个*异步的*resolved的promise(Chrome是例外，会返回一个同步的promise)。

- Promise.race(iterable): 返回一个新的Promise对象，iterable中**任意**一个*成功或者失败*都会调用其对应回调。
- Promise.reject(reason): 返回一个失败状态的Promise对象，并将reason传递给对应处理方法。
- Promise.resolve(value): 返回一个状态由value决定的Promise对象。如果value是thenable对象(带then方法的对象)，则状态由then方法执行决定；其他情况，则该Promise的状态为fulfilled。

## Promise原型

- Promise.prototype.constructor: 指向Promise构造函数。
- Promise.prototype.catch(onRejected): 给当前promise添加一个拒绝回调，返回一个新的Promise对象。如果被调用，新promise以onRejected函数的返回值来进行resolve；如果当前promise状态为fulfilled，则以当前promise的完成结果作为新promise的完成结果。
- Promise.prototype.then(onFulfilled, onRejected): 给当前promise添加成功和拒绝回调，返回一个新的promise。
- Promise.prototype.finally(onFinally): 给当前promise添加回调，并在解析完成之后返回一个新的promise。回调会在当前promise运行完毕后调用，**无论**状态是fulfilled还是rejected。

## 拾遗

关于Promise和microtask队列问题：

- 一个promise的then/catch/finally回调函数都是异步的，只有执行完主线程的任务才会开始将回调函数推入microtask队列
- 队列是先进先出
- 只有当主线程空闲的时候，才会开始执行microtask队列

``` javascript
new Promise(resolve => {
    resolve();
    Promise.resolve().then(() => console.log(1));
}).then(() => console.log(2));
// log: 1 2

new Promise(resolve => {
    Promise.resolve().then(() => console.log(3));
    resolve();
}).then(() => console.log(4));
// log: 3 4

new Promise(resolve => {
    setTimeout(() => {
        resolve();
        Promise.resolve().then(() => console.log(5));
    }, 0);
}).then(() => console.log(6));
// log: 6 5

new Promise(resolve => {
    setTimeout(() => {
        Promise.resolve().then(() => console.log(7));
        resolve();
    }, 0);
}).then(() => console.log(8));
// log: 7 8
```

## 阅读Promise[一种实现](https://github.com/then/promise/blob/master/src/core.js)的源码

以下是个人的简略解读，可以解释上述现象

``` javascript
new Promise(
    // executor会在promise对象被Promise构造函数返回之前调用（这一点非常重要）
    // promise会初始化一些属性，记作_state(resolve状态) = 0，_deferredState(_deferreds对象的状态，是由Handler构造函数构造的) = 0, _deferreds = null
    function executor(resolve, reject) {
        // resolve函数会将_state置为1，并调用finale函数
        // finale函数中会判断_deferredState：
        // 如果_deferredState为1或者2，则调用handle函数，其他情况不会执行任何代码
        resolve(someValue);
    }
)
// 在promise对象创建之后才会调用then方法（不调用then方法则不会有回调）
// then方法内部会调用一个handle函数
// 在handle函数会判断_state：
// 如果_state不为0（在executor中同步调用resolve函数），直接调用handleResolved函数（handleResolved函数会将成功回调函数推入微任务队列执行，源码用的是asap库，是用MutationObserver实现的）
// 如果_state为0，表明executor中的resolve函数可能是被异步调用的，此时promise对象的状态不为resolved。如果_deferredState === 0，则_deferredState置为1，并将onFulFilled函数作为封装成一个Handler，将_deferreds置为该Handler，用于在异步resolve执行时，提供给finale函数使用，finale函数会调用handle函数
.then(
    // 会被封装成一个Handler对象
    function onFulFilled() {
        // do something
    }
)
```
