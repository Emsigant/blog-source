---
title: Typescript泛型使用1
date: 2019-07-25 21:52:42
tags: typescript
---
今天在使用document.createElement时，无意中发现在创建不同HTML元素时，会对标签名进行提醒，而且会返回不同的类型，记录一下该泛型的使用方法：
``` typescript
interface IHTMLTypeMap {
    "div": HTMLDivElement;
    "span": HTMLSpanElement;
}
function createElement<T extends keyof IHTMLTypeMap>(tag: T): IHTMLTypeMap[T] {
    let ele = null;
    ele = document.createElement('div');
    // some operations
    return ele
}
const div = createElement("div");
div // HTMLDivElement
const span = createElement("span");
span // HTMLSpanElement
```
