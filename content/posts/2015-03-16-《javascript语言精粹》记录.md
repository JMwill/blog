---
title: "《javascript语言精粹》记录"
date: 2015-03-16T17:04:42+08:00
tags:
- 笔记
- javascript
---
javascript中存在着很多糟糕的特性：全局变量的轻易添加，无块级作用域，**==** 判断的不精确性等等，
但是它同时也具有许多精华的特性，对象字面量生成，函数字面量生成，使用原型继承的机制。

<!--more-->

javascript是不是一门备受误解的语言我不知道，但是我知道，javascript能够生存至今一定是有它的理由所在的，至少，在我认识的javascripter中，也是有着很厉害，能够驾驭这门相对奇特的语言的人的。

***

## 语法

javascript的语法没有什么好说的，几乎与其他的编程语言没有太大的区别。

但是有几点需要注意：

* 在每一个需要用到块级作用域的地方 **&nbsp;&nbsp;{&nbsp;&nbsp;**都需要跟在语句的后面，不应进行换行。如：

```
var foo = function() {

    //do somthing;
}

-----------------------------------------

var i
for (i = 0, i < list.length; i++) {
    //do somthing
}

```

* 由于javascript没有块级作用域，同时，又会对变量声明，函数声明等进行提升。所以在定义变量的时候不要在使用时才进行定义，最好在函数体的顶部中声明函数中可能用到的所有变量。如：

```
function foo() {
    var i, j, mublock;
    var mydiv, myHeader ...;
    //do somthing
}


//同时请记住一点，对于函数字面量的函数定义，函数是不会被提升的如：

a();    //会报错说没有定义这个函数

var a = function() {
    console.log('a');
}

//而对于这样的形式，却是可以的：

a();

function a() {
    consoel.log('a');
}
```

***

## 对象

* 在js里面，参数传递都是按值传递的。只是对于基本数据类型，它是传递变量保存的具体值，而对于引用类型的变量，它传递的就是变量保存的引用类型的引用值，所以会看似在进行引用传递. 如：

```
var a = 'abc';

function b(a){
    a = a + 'b';
    console.log(a);
}
b(a); //结果：abcb
console.log(a); //结果abc



var a = {}

function b(a){
    a.isin = 'function b';
    console.log(a.isin);
}
b(a);   //结果function b
console.log(a.isin);    //结果function b

//假如进行这样的操作：
function b(obj) {
    obj.name = 'outName';
    obj = new Object();
    obj.name = 'innerName';
}

var a = new Object();
a.name = 'globalName';
console.log(a.name); //输出globalName
b(a);
console.log(a.name); //输出outNmae

//实际上，obj保存的引用值在b的内部已经被替换成另一个对象的引用值了。
```

附： **基本变量类型**

|    类型    |    可能的值   |
|-----------|--------------|
|Undefined |只有undefined   |
|Null       |只有null       |
|Boolean    |true 和 false  |
|Number     |整数或者浮点数   |
|String     |任何字符串      |

* js中遍历对象可以使用 **for in** 来进行，但是，在对象中是以键值的方式来存储，所以，在遍历的时候属性名的出现顺序是不确定的。想要确保属性名以特定顺序出现，可以使用**for 循环**和一个包含想要遍历属性的数组来进行遍历，如：

```
//摘抄自《javascript语言精粹》p24, 略作修改
var i;
var properties = [
    'first-name',
    'middle-name',
    'last-name',
    'profession',
};
for (i = 0; i < properties.lenght; i += 1) {
    console.log(properties[i] + ': ' + obj[properties[i]]);
}
```

* 全局变量污染，在js中，全局变量十分容易创建，但是全局变量，在绝大多数情况下都是不推荐使用的。所以，我们应该在js中减少全局变量的污染。在《javascript语言精粹》中建议最小化使用全局变量的方法是只创建唯一的全局变量：

```
var MYAPP = {}; //让变量MYAPP变成专有容器

MYAPP.stooge = {
    "first-name": "fname",
    "last-name": "lname",,
};

MYAPP.flight = {
    airline: "China",
    ...
};
```

附：**操作符**

js具有以下几个操作符：var, in, delete, typeof, new, instanceof, void,下面进行简单介绍：

1. var 用于定义变量
2. in 可以单独使用（用于访问对象属性），或者用于**for in**循环中
3. delete 删除对象属性或者数组元素 (但是函数内的参数无法删除）， 删除数组时会留下孔洞，一般不进行这样的删除操作，而是使用数组的pop,shift等方法。
4. typeof 检查变量的类型，返回值有： 5个基本变量类型中的4个（null属于object），和两个对象类型object，function
5. new 用于创建实例，使用new调用构造函数时经历的4步：
    * 创建一个新对象
    * 将构造函数的作用域赋给新对象（因此this就指向了这个新对象）
    * 执行构造函数中的代码（为这个新对象添加属性）
    * 返回新对象
6. instanceof 用于确定对象的类型：如果instanceof左侧的对象是其右侧类型的实例(这个判断也会包括对“父类”的检测)，那么instanceof操作符就会返回true。
7. void 操作数可以是任何数值，其返回值一直时undefined

***

## 函数

* js中当函数作为一个对象的方法来调用的时候，this值会绑定到对象上。但是当函数被直接调用的时候，this被错误地绑定到了全局对象上。想要修复这个问题，可以在方法内部创建变量并赋值为this使得内部函数能够通过这个变量来使用正确的this值，如：

```
myObject.double = function() {
    var that = this; //保存指向myObject的this值到that身上

    var helper = function() {
        that.value = add(that.value, that.value);
    };
    helper();
};
```

* Apply调用模式
    * apply方法可以允许我们传递this值还有参数给一个调用函数，因此可以使得对象也能够调用本不属于它的函数， 方法。如：
        * `var status = Quo.prototypr.get_status.apply(statusObject);//`statusObject就调用了本来属于`Quo`的`get_status`方法
* 向Funciton.prototype增加method方法, 使得向对象增加方法的时候更加容易

```
Funciton.prototype.method = function (name, func) {
    if (!this.prototypr[name]) {
        this.prototype[name] = func;
    }
    return this;
};
```

* 使用闭包
    * 使用闭包能够进行变量，方法的保护等。如变量的保护：

```
//这个方式使得myObject对象外的函数或者对象都无发访问到值value
var myObject = (function() {
    var value = 0;

    return {
        increment: function (inc) {
            value += typeof inc === 'number' ? inc : 1;
        },
        getValue: function() {
            return value;
        }
    };
}());

/*在使用此类方式的时候需要记得内部函数访问外部对象方法，或者函数时。访问到的变量是变量的本身，因此，一旦变量有什么变化，内部函数访问到的都是变化后的变量，除非将变量进行一遍复制（注：对引用变量不起作用），如：*/

var foo = function(value) {
    var helper = function(i) {
        return function(e) {
            alert(i) // 使用外部函数传入的参数，也就是进行了值复制后的变量
        };
    };
    var i;
    for (i = 0; i < value.length; i++) {
        //do something
    }
};

/*在编程的过程当中，应当避免在循环中创建函数

```

* 模块
    * 模块的一般形式是：一个定义了私有变量和函数的函数；利用闭包创建可以访问私有变量和函数的特权函数；而后返回特权函数或将其保存在可以访问到的地方。
    * 模块模式也可以用于创建安全的对象。
* 级联
    * 让对象的方法返回this，而非undefined可以启用级联。参考jquery的链式调用

***

## 继承

* 基于原型的继承
    * 首先创建一个有用的对象。
    * 使用Object.create方法 构造出实例。
    * 定制创建出来的新的实例，如添加属性，方法等
* 函数化
    * 实现函数化构造器
        1. 创建一个新对象（对象字面量，使用new调用构造器函数，使用Object.create,或者调用会返回对象的函数
        2. 有选择地定义私有实例变量和方法。就是在函数中通过var语句定义的普通变量
        3. 给新对象扩充方法。方法可以拥有特权访问参数，以及第二步中定义的变量
        4. 返回新对象。

**函数化构造器伪代码**

```
var constructor = function(spec, my) {
    var that, 其他私有实例变量；
    my = my || {};

    把共享的变量和函数添加到my中

    that = 一个新对象

    添加给that的特权方法

    return that;
};
```

```
//js继承函数化实现示例

var mammal = function(spec) {
    var that = {};

    that.get_name = function() {
        return spec.name;
    };
    that.says = function() {
        return spec.saying || '';
    };
    return that;
};

var myMammal = mammal({name: 'Herb'});
myMammal.name = 'Kitty';
myMammal.get_name = function() {
    var that = this;
    return this.name;
}; 

var cat = function(spec) {
    spec.saying = spec.saying || 'meow';
    var that = mammal(spec);
    that.purr = function(n) {
        var i, s = '';
        for (i = 0; i < n; i += 1) {
            if (s) {
                s += '-';
            }
            s += 'r';
        }
        return s;
    };
    that.get_name = function() {
        return that.says() + ' ' + spec.name + ' ' + that.says();
    };
    return that;
};
var myCat = cat({name: 'Henrietta'});

Object.method('')

```

* 部件这一章，尚未理解！

***

## 数组

* js中判断对象是否为数组的方法：

```

//判断一个对象是否为数组
var is_array = function(value) {
    return Object.prototype.toString.apply(value) === '[object Array]';
}
```

* 对数组方法的一些补充：

***

## 方法

* 数组方法汇总：
    * array.concat(item....) //返回拼接数组
    * array.join(separator) //返回一个用separator作为分隔的字符串
    * array.pop() //删除数组最有一个元素，返回被删除元素，或undefined
    * array.push(item...) //从后向数组添加元素，并返回数组长度
    * array.reverse() //翻转数组本身内容，并数组
    * array.shift() //将数组从前删除，返回结果同pop
    * array.unshift(item...) //向数组起始位置添加元素，返回新的length
    * array.sort(comparefn) //对array内容进行排序，默认把元素当作字符串来对待，可传入处理函数调整排序方式
    * array.slice, array.splice如下解释


* 分清array里的splice方法和slice方法

```
array.slice(start, [end]);  //作用是对array中的一段做浅复制

array.splice(start, deleteCount, item...);

//从array中删除一个或多个元素并用item替换它们。start为起始下标。方法返回包含被移除元素的数组
```

* array的sort方法补充：

```
//sort内的数字排序函数实现
var n = [4, 5, 6, 29, 50];
n.sort(function(a, b) {
    return a - b;
});

//sort实现字符串排序的函数
var m = ['aa', 'bb', 'c', 'a', 4, 59];
m.sort(function(a, b) {
    if (a === b) {
        return 0;
    }
    if (typeof a === typeof b) {
        return a < b ? -1 : 1;
    }
    return typeof a < typeof b ? -1 : 1;
});

//sort的函数排序实现
var by = function(name) {
    return function(o, p) {
        var a, b;
        if (typeof o === 'object' && typeof p === 'object' && o && p) {
            a = o[name];
            b = p[name];
            if (a === b) {
                return 0;
            }
            if (typeof a === typeof b) {
                return a < b ? -1 : 1;
            }
            return typeof a < typeof b ? -1 : 1;
        } else {
            throw {
                name: 'Error',
                message: 'Expected an object when sorting by ' + name
            };
        }
    };
};
```

* 数字方法 （**注意，不能直接对数字123等进行下列操作。如123.toFixed()**）
    * number.toExponential(fractionDigits); //将number转换成一个指数形式的字符串。可选参数fractionDigits控制其小数点后的数字位数。值必须在0～20
    * number.toFixed(fractionDigits); //转换成为一个十进制数形式的字符串，参数控制其小数点后数字位数。0～20
    * number.toPrecision(precision); //将number转换成为一个十进制形式的字符串，参数控制数字精度。0～21
    * number.toString(radix); //将number转换为一个字符串。参数控制基数。2～36 默认为10`进制转换中可使用
