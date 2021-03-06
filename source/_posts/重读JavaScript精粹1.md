---
title: 重读JavaScript精粹1
date: 2019-08-27 19:31:45
tags: javascript
---
终于下定决心重读《JavaScript精粹》这本书了。

这本书还是大三时某次跟风剁手的时候买的，买了之后翻看了两眼之后，就一直被尘封起来，直到刚刚，我偶然在柜子的角落翻到了这本书。

如下是一些概括

- 对象的定义：JavaScript里的值，除了简单数据类型（数字，字符串，布尔值，null和undefined），都可以称为对象
- 对象字面量/Object Literals：包含在{}里的0个或者多个key-value对，可以出现在任何允许表达式出现的地方
- 检索/Retrieval：[]或者.来检索对象所包含的值
- 更新/Update：使用[]或者.来更新或者拓展对象的属性
- 引用/Reference：对象通过引用来传递，对象的值永远不会被复制
- 原型/Prototype：每个对象都可以链接到一个原型对象，并且从中继承属性。特别的，通过对象字面量创建的对象会被链接到Object.prototype，对一个对象的修改不会涉及原型链的。原型链只有在检索的时候才会用到。对一个对象进行检索的时候，如果改属性不存在，则会从对象的原型上寻找，如此类推，直到Object.prototype，这个过程被称为委托
- 删除/Delete：删除对象的属性，如果存在改属性，则会被移除。无法删除原型的属性
