title: JavaScript作用域
date: 2015-10-17 18:48:39
tags:
    - javascript
    - 笔记
---
JavaScript（以下简称js）中作用域有全局作用域与局部作用域两种，这里重点讲局部作用域。

<!-- more -->

## 什么是作用域？

字面意思来看，作用域就是某样事物在一个或多个地方里面能够起作用。对于js而言，抛开最新版本的EcmaScript 6不说，EcmaScript 5以及之前的版本里面只有函数能产生域，同时没有块级作用域这个东东。对于作用域这个问题js权威指南的描述十分好理解：`JavaScript中的函数运行在它们被定义的作用域里,而不是它们被执行的作用域里`。

## 实例

```
var scope = 'global scope';
function funOne() {
    alert(scope);
}

function funTwo() {
    var scope = 'funTwo scope';
    funOne();
}
funTwo();
```

这段代码运行后会弹出`global scope`，为什么不是`fun2 scope`呢？明明我已经在fun2里面覆盖了scope的值了呀，

其实，在创建funOne的时候，它的一个属性**“作用域链”**会保存当前作用域的状态，就funOne而言，作用域链中会填入全局环境（即window）下的所有可访问的对象名，如果funOne在另外一个或多个函数里面，在记录全局作用域之前会先记录外层函数的作用域，而后一级一级向上记录直到全局作用域，就像一个队列一样，先记录的先访问。而后，当funOne被调用的时候，一个叫做**“运行期上下文”**的对象被创建，里面包含有函数保存的**“作用域链对象”**，而当funOne被调用需要进行标识符解析时就会在里面进行查找，funOne[Scope]——>Scope Chain[0] ... ——>Global Object，显然，当funOne定义时没有在funTwo的作用域内，因此并没有把其包含进去，而在调用funOne时产生的活动对象也就不包括funTwo的作用域，因此，funOne调用时得到`global scope`也就可以理解了。

总之，最需要记住的就是：

**函数运行在它们被定义的作用域里**

**函数运行在它们被定义的作用域里**

**函数运行在它们被定义的作用域里**

重要的事情说三遍。

## 优化

从上面可以看出，作用域嵌套得越深，访问标识符就越耗时间，那么，当需要跨作用域进行**多次**访问的时候，可以通过变量将要访问的值保存到局部来进行优化。就像经常会使用的`Array.prototype`，可以将其局部化，用一个变量如：arrProto来保存。

## 作用域链改变

js中作用域链一般情况下，在运行期上下文运行的过程中，其作用域链会被 with 语句和 catch 语句影响。with语句不推荐使用，因此直接不用就好，而对于catch语句，当发生错误而执行转跳到catch部分时，一个包含异常对象的可变对象会被置于作用域的头部，而函数的原作用域的其他部分将全部后延一位，这里会带来一些性能的损耗，一般不用去在意，如果真的十分重视性能，可以通过将错误处理委托给另一个函数来进行，

```
try {
    myErrorFun();
} catch (e) {
    dealWithError(e);
}
```

上面由于dealWithError方法是catch子句中唯一执行的代码。该函数接收异常对象作为参数，由于只执行一条语句，且没有局部变量的访问，作用域链的临时改变就不会影响代码性能。


参考资料：

- [http://www.cnblogs.com/lhb25/archive/2011/09/06/javascript-scope-chain.html](http://www.cnblogs.com/lhb25/archive/2011/09/06/javascript-scope-chain.html)
- [http://www.laruence.com/2009/05/28/863.html](http://www.laruence.com/2009/05/28/863.html)