---
title: javascript原型链
date: 2015-10-28T14:25:17+08:00
tags:
- javascript
- 笔记
---

Ecmascript 6之前，JavaScript不包含传统的类，JavaScript中对象通过原型链进行继承，通过原型链就能够让函数成为对象的方法。

<!-- more -->

## 对象

在理解什么是原型之前，我们要先了解JavaScript中的对象，在JavaScript中除了主数据类型(undefined，null，boolean，number，or string)，剩下的都是对象，或者说一切（引用类型）都是对象，对象是属性的集合。而对象都是通过函数来创建的，而当试图获取一个主数据类型的原型时，它会被强制转化成了一个对象，如：`false.__proto__ === Boolean(false).__proto__; `

## 原型

那什么是原型？原型是一个对象，其他对象可以通过它实现属性继承。既然如此，在以对象为先的JavaScript中，所有的对象都有一个原型，原型本身也是对象，因此原型本身也有一个原型。所有的对象都可以成为原型。

获取对象原型的方法：

```
var obj = {}; // 创建一个对象

//Firefox 3.6 and Chrome 5
Object.getPrototypeOf(obj);

//Firefox 3.6, Chrome 5 and Safari 4 
obj.__proto__

//All
obj.constructor.prototype;
```

### 使用对象原型

```
var Person = function() {
    this.name = null;
    this.setName = function(name) {
        this.name = name;
    };
    this.getName = function(name) {
        return this.name;
    }
};
var me = new Person();
me.setName('JMwill');
console.log(me.getName());
```

上面就是一般使用对象的方法，但是上面的方法每创建一个对象，内部的方法就需要创建一次，浪费资源。下面是另外一种写法：

```
var Person = function() { this.name = null; };
Person.prototype = {
    setName: function(name) {
        this.name = name;
    },
    getName: function() {
        return this.name;
    }
};
var me = new Person();
me.setName('JMwill');
console.log(me.getName());
```

在进行继承时，若不希望子对象继承父对象的属性，可以在使用prototype的基础上进行prototype继承：

```
var Person = function() { this.name = null; };
Person.prototype = {
    setName: function(name) {
        this.name = name;
    },
    getName: function(name) {
        return this.name;
    }
};
var Student = function() { this.school = null; };

Student.prototype = new Person();  //继承属性
Student.prototype = Person.prototype; //不继承属性
Student.prototype.setSchool = function(school) {
    this.school = school;
};
Student.prototype.getSchool = function() {
    return this.school;
};

var me = new Person();
console.log(me.name);
var me = new Student();
console.log(me.name);
```

尽管这个例子有点问题，但是，可以说明使用原型链继承，可以使得父对象的属性不会在子对象中出现。

## Instanceof

在使用到原型以及原型链时，常常会碰到需要确定对象所属的情况，这里使用typeof是无法得出想要的结果的，这时就到instanceof上场了。

A instanceof B

上面的例子中，A为B创建出来的对象。Instanceof的判断队则是：沿着A的\_\
_proto\_\_这条线来找，同时沿着B的prototype这条线来找，如果两条线能找到同一个引用，即同一个对象，那么就返回true。如果找到终点还未重合，则返回false。

## 原型链

先盗图一张，来源： http://www.ibm.com/developerworks/cn/web/1306_jiangjj_jsinstanceof/figure1.jpg

![原型链](http://www.ibm.com/developerworks/cn/web/1306_jiangjj_jsinstanceof/figure1.jpg)

正常情况下，当我们进行原型链的继承时，使用的方法就是将原型对象赋予一个继承的对象的实例，即：

```
var Person = function() { this.name = null; };
Person.prototype = {
    setName: function(name) {
        this.name = name;
    },
    getName: function() {
        return this.name;
    }
};
var Student = function() { this.school = null; };
Student.prototype = new Person();
Student.prototype.constructor = Student; //修正原型的构造对象
```

上面的方法就是一般继承时需要做的步骤。

## 属性查找

在JavaScript中对象的属性会通过原型链进行查找，而hasOwnProperty方法是JavaScript中唯一一个处理属性但是不查找原型链的函数。因此可以使用其来进行自定义属性的查找。

```
var Person = function() { this.name = null; };
Person.prototype = {
    setName: function(name) {
        this.name = name;
    },
    getName: function() {
        return this.name;
    }
};

var Student = function() { this.school = null; };
Student.prototype = new Person();
Student.prototype.constructor = Student;
Student.id = 0

console.log(Student.hasOwnProperty('id')); // true
console.log(Student.hasOwnProperty('getName')); // false
```

## \_\_proto\_\_对象

再盗图一张

![__proto__对象指向](http://dmitrysoshnikov.com/wp-content/uploads/constructor-proto-chain.png)

JavaScript中的对象都具有一个\_\_proto\_\_属性，这个属性默认引用了创建这个对象的函数的prototype对象，因此，可以有：

```
var Person = function() { this.name = null; };
Person.prototype = {
    setName: function(name) {
        this.name = name;
    },
    getName: function() {
        return this.name;
    }
};

var p1 = new Person();
var p2 = new Person();

// 上面创建的对象中，p1、p2的proto指向Person的prototype对象
// 而Person由Function对象创建而来的，因此proto指向Function的prototype对象
// Person的prototype以及Function的prototype都是Object创建而来，因此proto
// 指向Object的prototype对象
// 而每一个prototype对象，在最初状态下其constructor属性指向的都是本身所属的函数
```

## 额外记录：this

this是掌握JavaScript中的重点，而更多时候，让我们迷惑的是this到底代表什么？其实在函数中的this取何值是在函数真正被调用执行的时候确定的，函数定义的时候确定不了。

* 当函数作为构造函数被调用时，this为构造出的对象。当函数进行全局或者普通调用时，this为全局Window（浏览器上）
* 在作为一个对象属性的函数中被调用时，this指向的是当前对象

```
var obj = {
    name: 'will',
    setAge: function() {
        var generateAge = function() {
            this.age = 10;
        }
        generateAge(); // 这里也相当于普通调用，this指向window
    }
}
obj.setAge();
console.log(window.age); // 10
console.log(obj.age); // undeifned
```

参考资料：

- [http://www.nowamagic.net/librarys/veda/detail/1648](http://www.nowamagic.net/librarys/veda/detail/1648)
- [http://www.jb51.net/article/30750.htm](http://www.jb51.net/article/30750.htm)
- [http://blog.jobbole.com/9648/](http://blog.jobbole.com/9648/)
- [http://www.cnblogs.com/wangfupeng1988/p/3977987.html](http://www.cnblogs.com/wangfupeng1988/p/3977987.html)