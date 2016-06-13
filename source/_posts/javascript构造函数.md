title: javascript构造函数
date: 2015-11-02 19:02:21
tags:
    - javascript
    - 笔记
---

什么是构造函数？构造函数在JavaScript中也就是一个普通的函数，之所以能成为构造函数是因为需要使用其来进行对象的构造。而构造新对象的情况出现在使用new操作符调用构造函数的时候。在这里引用图片一张<!-- more -->

![js对象关系](http://www.nowamagic.net/librarys/images/201203/2012_03_21_03.png)

由图片的关系可以看出每一个函数的内部都有一个**prototype属性**，这一个属性指向一个**prototype对象**，prototype对象内显式地包含了一个**constructor属性**指向函数，这样构成一个循环引用。

那么问题来了，为什么需要这样的一个constructor属性呢？因为在JavaScript中，每一个对象都**隐式地**包含了一个**\_\_proto\_\_**属性，这一个属性用于指向**构造函数的prototype对象**，这样当对象查找自己内部属性无果时，可以通过原型链，也即prototype对象来进行查找引用。这就是JavaScript的原型继承，这个时候，通过原型链也就能够找到对象自身的创建者，即constructor属性指向的对象（构造函数）。

所以看回图片可以看到**b、c**两个对象的**\_\_proto\_\_**都指向**Foo**的prototype对象，因为都是由Foo创建出来的，而**Foo**的**\_\_proto\_\_**指向**function**的prototype对象，显然这是由于Foo是由**function对象**创建出来的，最后要说一下的就是，所有的**prototype对象**的**\_\_proto\_\_**都是指向**object对象**，结果自然就是因为**prototype对象**是由object创建出来的。

但是还是没有说明白为什么需要这一个constructor属性啊，对，如果我们根本不需要知道对象的创建者，那么constructor存在好像也没有什么必要，但是当我们使用**instanceof**运算符的时候，假设A是B创建出来的实例对象。即A instanceof B就是正确的，而内部的查找线路会是这样：先查看`A.__proto__`是否等于`B.prototype`，如果等于那么就返回true，如果错误那么就继续按照A的**\_\_proto\_\_**这条链来进行查找，而B则按照**prototype**这条链来进行查找，如果找到同一引用，也返回true，否则到最后结束，没有则返回false。

参考资料：

- [http://www.nowamagic.net/librarys/veda/detail/1642](http://www.nowamagic.net/librarys/veda/detail/1642)