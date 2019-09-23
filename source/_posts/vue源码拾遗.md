---
title: vue源码拾遗
date: 2019-09-17 15:52:17
tags:
---
## Observer、Dep和Watcher

Observer大致实现：

``` typescript
// Observer不直接调用，而是通过observe函数调用。observe函数判断了一些因素，然后调用Observer
class Observer {
    value: any; // 将要observe的对象
    dep: Dep;
    vmCount: number;
    constructor(value: any) {
        // 省略this.xx = xx
        this.dep = new Dep(); // 初始化一个dep
        def(value, '__ob__', this); // 给value加上__ob__属性
        if (Array.isArray(value)) {
            // 如果是数组，observe数组每一项
            this.observeArray(value);
        } else {
            // 不是数组，调用this.walk
            this.walk(value);
        }
    }
    walk(obj: object) {
        for(let key of Object.keys(obj)) {
            defineReactive(obj, key);
        }
    }
    observeArray(arr: Array<any>) {
        for(let i of arr) {
            observe(i); // observe就是做了一些判断然后调用Observer的一个简单函数
        }
    }
}
```

defineReactive函数：

``` typescript
// 重点关注dep的处理
function defineReactive(obj: object, key: string, val: any) {
    const dep = new Dep();
    // 省略一些不重要的代码
    Object.defineProperty(obj, key, {
        get() {
            const value = _getValue(val); // _getVal的实现是判断val是否有getter，有的话就调用，没的话直接返回val
            if (Dep.target) {
                // 如果Dep有正在evaluate的watcher
                dep.depend(); // 添加依赖，将dep添加到target的deps数组
                // ...
            }
            return val;
        },
        set(newVal) {
            const value = _getValue(val); // 同上get里的
            // ...
            val = newVal;
            dep.notif(); // 调用dep的notif方法
        }
    });
}
```

Dep大致实现：

``` typescript
class Dep {
    static target: Watcher; // 当前evaluate的watcher
    id: number;
    subs: Array<Watcher>;
    constructor() {
        this.id = uid++;
        this.subs = [];
    }
    addSub(sub: Watcher) {
        this.subs.push(sub);
    }
    removeSub(sub: Watcher) {
        _remove(this.subs, sub);
    }
    depend() {
        // 调用target的addDep方法将当前dep推入target.deps
        if (Dep.target) {
            Dep.target.addDep(this);
        }
    }
    notif() {
        // 对subs依id进行排序然后调用每个watcher的update方法
        const subs = this.subs.slice().sort((a, b) => a.id - b.id);
        for(let watcher of subs) {
            watcher.update();
        }
    }
}
```

Watcher大致实现：

``` typescript
// watcher的作用是收集一个表达式或者函数(比如computed)的依赖，并且在值变更之后
class Watcher {
    // ...省略属性
    deps: Array<Dep>;
    newDeps: Array<Dep>;
    depIds: SimpleSet;
    newDepIds: SimpleSet;
    constructor(
        vm: Component,
        expOrFn: string | Function, // 表达式或者函数名
        cb: Function,
        options?: object,
        isRenderWathcer?: boolean
    ) {
        // ...
        this.deps = [];
        this.newDeps = [];
        this.depIds = new Set();
        this.newDepIds = new Set();
    }
    get() {
        // ...
        const vm = this.vm;
        const value = this.getter.call(vm, vm);
        // ...
        return value;
    }
    addDep (dep: Dep) {
        const id = dep.id;
        if (!this.newDepIds.has(id)) {
            this.newDepIds.add(id);
            this.newDeps.push(dep);
            if (!this.depIds.has(id)) {
                dep.addSub(this);
            }
        }
    }
    cleanupDeps() { // 清空所有dep
        // ...
    }
    update() {
        // ...
        _queueWatcher(this); // 将当前watcher推入缓冲队列
    }
    run() {
        // ...
        this.cb.call(this.vm, value, oldValue);
        // ...
    }
    evaluate() {
        this.value = this.get();
    }
    depend () { // 调用每个dep的depend方法
        let i = this.deps.length;
        while (i--) {
            this.deps[i].depend();
        }
    }
    teardown() { // 销毁所有依赖关系
        // ...
    }
}
```
