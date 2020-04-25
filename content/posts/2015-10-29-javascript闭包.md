---
title: javascript闭包
date: 2015-10-29T15:02:18+08:00
tags:
- javascript
- 笔记
---

## 预备知识

### this

在JavaScript中，this指向函数**执行时**的当前对象。this关键字在JavaScript中和**执行环境**，而非声明环境有关。而当没有明确的执行时的当前对象时，this指向全局对象window。

<!-- more -->

JavaScript中我们需要了解几类函数：

- 对于eval函数，其执行时候似乎没有指定当前对象，但实际上其this并非指向window，因为该函数执行时的作用域是当前作用域，
- 浏览器中setTimeout、setInterval和匿名函数执行时的当前对象是全局对象window，
- apply和call能够强制改变函数执行时的当前对象，让this指向其他对象。
- new关键字后的构造函数中的this指向用该构造函数构造出来的新对象

### 作用域

在JavaScript中除了全局作用域之外，只有函数可以创建的作用域，因此，全局代码最好在代码前端声明，函数中要在函数体一开始就声明好。除了这两个地方，其他地方都不要出现变量声明。声明时建议用“单var”形式。同时我们需要记住的一点是作用域在函数定义时就已经确定了。而不是在函数调用时确定

### 函数定义与执行

假设现在需要定义一个函数A，当定义函数A的时候，js解释器会将函数A的作用域链(scope chain)设置为**定义A时A所在的“环境”**，如果A是一个全局函数，则scope chain中只有window对象。

当执行函数a的时候，A会进入相应的执行环境(excution context)。在创建执行环境的过程中，首先会为A添加一个scope属性，即A的作用域，其值就为定义A时的scope chain。即A.scope=A的作用域链。

之后执行环境会创建一个活动对象(call object)。活动对象也是一个拥有属性的对象，但它不具有原型而且不能通过JavaScript代码直接访问。创建完活动对象后，把活动对象添加到A的作用域链的最顶端。此时A的作用域链包含了两个对象：A的活动对象和window对象。

接下来是在活动对象上添加一个arguments属性，它保存着调用函数A时所传递的参数。
最后把所有函数A的形参和内部的函数的引用也添加到A的活动对象上。在这一步中，完成了内部函数的定义，因此如同定义A函数，函数的作用域链被设置为内部函数所被定义时的环境，即A的作用域。

到此一个函数从定义到执行的过程结束了。

### 自由变量

在一个作用域中（称为A）使用的变量x，却没有在这个A作用域中声明（即在其他作用域中声明的），对于这个作用域A来说，x就是一个自由变量。

```
var x = 10;
var myFun = function() {
    console.log(10 + x);
}
```

而需要记住的一个问题就是，在一个函数（myFun）中的自由变量需要到哪里取值？答案是：取自由变量x的值时，要到创建myFun函数的那个作用域中取————无论fn函数将在哪里调用。如一个迷惑人的例子：

```
var x = 10;
var myFun = function() {
    console.log(10 + x);
}

var testFun = function(fn) {
    var x = 20;
    fn();
}
testFun(myFun); // 得到20而不是30 
```

在上面的例子中，如果认为自由变量应该由上一级作用域中取值的话，就会得到错误的答案。所以要记住**取自由变量x的值时，要到创建myFun函数的那个作用域中取**

## 闭包

有了上面的预备知识，下面来了解一下，什么是闭包（简而言之）：

- 闭包就是函数的局部变量集合，只是这些局部变量在函数返回后会继续存在
- 闭包就是就是函数的“堆栈”在函数返回后并不释放，我们也可以理解为这些函数堆栈并不在栈上分配而是在堆上分配
- 当在一个函数内定义另外一个函数就会产生闭包

它的应用有两种情况————函数作为返回值，函数作为参数传递。而闭包最大用处有两个，一个是可以读取外部函数内部的变量，另一个就是让使用到的外部变量的值始终保持在内存中。

```
// 情况一
var myFun = function() {
    var name = 'will';
    return function sayName() {
        console.log(name);
    };
}
var sayName = myFun();
sayName();

// 情况二
var name = 'will',
    sayName = function() {
    return name;
}
var theName = function(fn) {
    var name = 'another will'
    return fn();
}(sayName);
console.log(theName);
```

在上面的例子中可以看到，返回的函数创建了一个独立的作用域，而这个返回的函数使用了自由变量，因此，在这个返回的函数未销毁前，创建自由变量的上下文环境不会被销毁，如此才能够使用自由变量，但是这样做会增加内存开销，同时还可能在IE上造成内存泄露所以要在必要时再使用闭包。

在看参考资料中了解到Javascript中的闭包(Closure)，有两个特点：

- 作为一个函数变量的一个引用 - 当函数返回时，其处于激活状态。
- 一个闭包就是当一个函数返回时，一个没有释放资源的栈区。

而比较让人认同的闭包实现可以通过三种方式：
```
with(obj){
    //这里是对象闭包
}

(function(){
    //函数闭包
})()

try{
    //...
} catch(e) {
    //catch闭包 但IE里不行
}
```

参考资料：

- [http://www.cnblogs.com/wangfupeng1988/p/3977987.html](http://www.cnblogs.com/wangfupeng1988/p/3977987.html)
- [http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html](http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html)
- [http://www.cnblogs.com/rubylouvre/archive/2009/07/24/1530074.html](http://www.cnblogs.com/rubylouvre/archive/2009/07/24/1530074.html)
- [http://www.jb51.net/article/18303.htm](http://www.jb51.net/article/18303.htm)
- [http://www.oschina.net/question/28_41112](http://www.oschina.net/question/28_41112)