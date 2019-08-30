---
title: 重读JavaScript精粹2
date: 2019-08-28 18:40:43
tags: javascript
---
函数对象

- 函数的定义：JavaScript的函数就是对象。特殊的是，每个函数对象会被连接到Function.prototype，在每个函数被创建时，都会附加两个隐藏的属性（函数上下文和实现函数的代码）。当一个函数被创建时，它的prototype会添加一个constructor属性，指向这个函数自身，Function构造器会运行类似这样的代码：

  ``` javascript
  this.prototype = { constructor: this }

  const Func = function() {};
  const afunc = new Func();
  afunc.__proto__ === Func.prototype; // 一个对象的constructor始终指向其构造函数
  afunc.constructor === afunc.__proto__.constructor === Func;
  ```

- 函数字面量/Function Literals：函数对象可以通过函数字面量创建。通过函数字面量创建的函数对象包含一个连接到外部上下文的链接，这被称为闭包
- 调用/Invocation：每调用一个函数，就会暂停当前函数的执行，传递控制权和参数给新函数。出了声明时定义的形式参数，每个函数还会接受两个附加的隐藏参数：this和arguments。this的值取决于调用方式。JavaScript中一共有4种调用方式：方法调用、函数调用、构造器调用和apply调用
- 方法调用模式：当一个函数被保存为对象的一个属性时，我们称它为一个方法。当一个方法被调用时，this会被绑定到该对象
- 函数调用模式：当一个函数不是一个对象的方法时，那么在调用它时，就是函数调用模式。this会被绑定到全局对象（这是一个语言设计缺陷）
- 构造器调用模式：JavaScript是一门基于原型继承的语言，对象可以通过原型继承其他对象的属性，js是无类型的。在一个函数前加上new来调用时，就是构造器调用模式。这时会创一个新对象，这个对象的原型会被连接到函数的prototype，this会被绑定到新建的对象
- apply调用模式：apply方法是Function.prototype的方法。可以通过apply方法改变this的绑定值
- 返回/Return：当一个函数被调用时，在关闭函数体的}结束后，就会被控制权交还给调用它的程序。return语句可以提前让函数返回。每个函数总是会有一个返回值，如果没有指定，则返回undefined。如果构造函数没有返回一个对象，则返回this（返回了对象则不会返回this）
- 递归/Recursion：递归就是一个函数在内部调用自身的过程，本质上是把一个问题拆分成相似的子问题去解决
- 闭包/Closure：一个函数可以访问定义它们的外部函数的参数和变量（除了this和arguments）
