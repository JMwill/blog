title: javascript面向对象编程
date: 2015-11-02 19:01:54
tags:
    - javascript
    - 笔记
---

根据[阮一峰](http://www.ruanyifeng.com/blog/)大大所说JavaScript是一种基于对象的语言，但由于缺失了类这一个语法特性，JavaScript又不是一种面向对象编程的语言。

<!-- more -->

## 对象

### 生成对象的原始模式：

```
var Obj = {
    property: 'num or string',
    method: function() {

    }
}
```

上面的方法生成的对象是一个个的独立个体，相互之间除了都是由Object对象创建出来之外，没有其他任何关系。

### 构造函数模式：

要实现从原型生成一个实例，可以通过JavaScript提供的构造函数。而所谓的构造函数也就是一个普通的函数，但是其内部使用了**this**这个变量，在通过**new**运算符进行实例生成的时候，this会动态地绑定到生成的对象上。

```
function Obj(name, age) {
    this.name = name;
    this.age = age;
}

var objOne = new Obj('one', 10),
    objTwo = new Obj('Two', 5);
```

如上的对象生成方式就利用了JavaScript的构造函数，通过这种方式构造出来的实例对象会包含一个指向构造它们出来的对象（函数）的属性叫做：**constructor**，这种关系可以使用**instanceof**运算符来进行判断：`console.log(objOne instanceof Obj); // true`

通过构造函数可以很好地构造出对象，但是当构造函数中包含有很多的属性、方法时，这种方式进行构造就会极大地浪费内存，因为每一次创建出来的对象内部的所有东西都是独立的，没有进行重用。即使是相同的方法（函数），构造函数也会重新运行一次操作，进行申请资源，分配空间等然后分配给新的对象。

### prototype

因为构造函数无法解决重用的问题，因此，需要用一个额外助手：prototype。prototype属性是每一个构造函数中都存在的一个属性，它指向另外的一个对象可以称为**prototype对象**，这个对象中的所有属性和方法都会被构造函数构造出来的**实例对象**所继承。

因为有了prototype对象，现在，可以将需要进行重用的属性或者方法等放到它里面：

```
function Obj(name, age) {
    this.name = name;
    this.age = age;
}

Obj.prototype.type = 'Person';
Obj.prototype.getName = function() {
    return this.name;
}

var objOne = new Obj('one', 10);
```

这样这些不变的方法以及属性就可以避免重复创建了。

### prototype验证

为了方便验证prototype或进行一些其他操作，JavaScript里提供了一些辅助方法：

```
// isPrototypeOf()方法，判断某个proptotype对象和某个实例之间的关系
console.log(Obj.prototype.isPrototypeOf(objOne)); // true

// hasOwnProperty()方法，判断某一个属性是本地属性，还是继承自prototype对象的属性
console.log(objOne.hasOwnProperty('name')) // true
console.log(objOne.hasOwnProperty('type')) // false

// in运算符，判断某个实例是否含有某个属性
console.log('age' in objOne); // true;
console.log('type' in objOne)
```

## 构造函数的继承

### 绑定方式

关于构造函数的继承，说白了就是要让实例对象能够找到需要继承的对象的属性以及方法。而在构造函数中，使用到了**this**这个对象，在JavaScript中，能够修改this指向的方法有apply和call，这样，一种继承的方式就有着落了：

```
function Person(name, age) {
    this.name = name;
    this.age = age;
}

function Student(name, age, school) {
    Person.apply(this, arguments);
    this.school = school;
}

var studentOne = new Student('xiaoming', 10, 'Oxford');
```

这里已经实现了继承，然而这种实现需要记得参数的位置要保持一致，虽然可以在Student定义时不添加name，age这两个参数，但是为了不造成混淆，还是加上的好。

### prototype方式

prototype方式是通过将prototype设置为需要继承的对象的一个实例，通过这个实例来进行属性以及方法的查找。

```
function Person(name, age) {
    this.type = "Person";
}

function Student(name, age, school) {
    this.name = name;
    this.age = age;
    this.school = school;
}

Student.prototype = new Person();
Student.prototype.constructor = Student;

var studentOne = new Student('xiaoming', 10, 'Oxford');
```

使用这种方式能够实现继承，但是明显有这其特有的限制，参数会被固定，或者只能继承固定的属性。同时，因为在外部重写了prototype属性，因此需要将新的prototype属性里面指向原构造函数的constructor重写成目标构造函数，在这里就是用Student覆盖掉Person，避免继承链的混乱。由于这里继承的是不变的属性，因此可以直接将属性写入到prototype中，然后通过指向prototype来实现继承。

```
function Person() {};
Person.prototype.type = "Person";

function Student(name, age, school) {
    this.name = name;
    this.age = age;
    this.school = school;
}

Student.prototype = Person.prototype;
Student.prototype.constructor = Student;

var studentOne = new Student('xiaoming', 10, 'Oxford');
```

使用上面的方法实现继承，能够提高效率，但是当有需要对prototype（这里指Student的prototype对象）进行改写的时候，其继承的对象的prototype（这里指Person的prototype对象）的属性也会一并受影响。因此这种继承只适合继承永远不变的属性。

### 空对象跳板

由于直接继承prototype即使用被继承对象的prototype实现继承会出现间接修改的问题，而通过使用实例实现继承会出现浪费资源的问题。所以这里通过使用一个空对象来作为跳板，影响最小化。由于空对象的内存占用小，而且对其进行修改不会有什么问题，因此可以这样来实现继承：

```
function Person() {};
Person.prototype.type = 'Person';

function tempFun(){};

tempFun.prototype = Person.prototype;

function Student(name, age, school) {
    this.name = name;
    this.age = age;
    this.school = school;
}

Student.prototype = new tempFun();
Student.prototype.constructor = Student;

var studentOne = new Student('xiaoming', 10, 'Oxford');
```

上面通过空对象而且使用prototype直接继承的方式，使得内存开销小。同时避免了prototype修改的副作用。

另外一种空对象的实现方式可以通过为父类添加一个init方法，从而避免在构造函数内进行父类属性的初始化。

```
function Person() {};

Person.init = function(name, age) {
    this.name = name;
    this.age = age;
}

function Student() {};
Student.prototype = new Person;
Student.prototype.constructor = Student;

Student.prototype.init = function(name, age, school) {
    this.name = name;
    this.age = age;
    this.school = school;
}

var xiaoming = new Student;
xiaoming.init('xiaoming', 10, 'Oxford');

console.log(xiaoming.school)
```

在这里实现了父类的继承，无法使用父类的初始化方法，而且需要人手调用init方法。虽然可以通过一个变量标识是否在进行实例化从而在父类的构造函数内进行判断来调用init方法，不过这样会引入全局变量，不推荐使用。

### 万能拷贝

既然是继承，那么也就是要用到被继承的对象的资源，因此，通过变通的方法，使用拷贝来进行继承。把所有的对象都放到prototype上，然后通过遍历将属性都拷贝到要继承的对象上。

```
function Person() {};
Person.prototype.type = 'Person';

function Student(name, age, school) {
    for (var i in Person.prototype) {
        Student.prototype[i] = Person.prototype[i];
    }

    this.name = name;
    this.age = age;
    this.school = school;

}

var studentOne = new Student('xiaoming', 10, 'Oxford');
```

当然，当对象的属性构成比较复杂的时候就需要通过深拷贝来实现精确拷贝，而非引用拷贝。这样才能够真正避免修改prototype的副作用。

## 非构造函数继承

### 修改子对象prototype

当我们拥有一些普通对象，而且想要实现对象间的继承的时候，因为普通对象没有构造能力，无法使用new，那么这时通过使用什么方法来达到继承的目的呢？通过使用prototype。对象进行属性查找的时候在自身上无法查找到的时候就会通过prototype来进行查找。因此可以通过prototype来实现继承。

```
var goodness = {
    sympathetic: true,
    helpful: true
}

function object(obj) {
    function fun() {};
    fun.prototype = obj;
    return new fun;
}

var person = object(goodness);

person.name = 'xiaoming';
person.age = 10;
```

这一种方法，只不过是让被继承的对象变通地赋予构造的能力而已。

### 还是万能的拷贝

既然对象没有构造能力，那么我们就人为的帮它实现继承。所以，这里可以使用拷贝的方法。而正如上面构造函数的最后一种继承方法一样，当对象属性不复杂时，可以使用浅拷贝。而当属性为另一个对象时，浅拷贝就只能拷贝引用了，所以，这里统一使用深拷贝。来进行继承实现。

```
var goodness = {
    sympathetic: true,
    helpful: true
}

function deepCopy(beCopy, copyer) {
    var copyer = copyer || {};
    for (var i in beCopy) {
        if (typeof beCopy[i] === 'object') {
            copyer[i] = (beCopy[i].constructor === Array) ? [] : {};
            deepCopy(beCopy[i], copyer[i]);
        } else {
            copyer[i] = beCopy[i];
        }
    }
    return copyer;
}

var person = deepCopy(goodness);

person.name = 'xiaoming';
person.age = 10;
```

以上就是继承的一些方式，在进行面向对象编程的时候可以斟酌使用。

参考资料：

- [http://www.ruanyifeng.com/blog/2010/05/object-oriented_javascript_encapsulation.html](http://www.ruanyifeng.com/blog/2010/05/object-oriented_javascript_encapsulation.html)
- [http://www.cnblogs.com/sanshi/archive/2009/07/08/1519036.html](http://www.cnblogs.com/sanshi/archive/2009/07/08/1519036.html)
- [http://segmentfault.com/a/1190000002440502](http://segmentfault.com/a/1190000002440502)