title: "Python学习笔记"
date: 2015-03-16 17:16:27
tags:
    - 笔记
    - Python
---
## 入门所学知识：

1. python中print的方法跟c语言的基本一样，同样可以使用`print 'output %s, something %s.' %(temp1, temp2)`
2. raw字符串与多行字符串：`r'...'`免于对字符串**使用转义符**，但是无法表示多行字符，以及包含**’**和**“**的字符串， 可以使用r'''...'''来表示多行字符串
3. unicode字符串：在字符串前加**u**字符，当出现中文编码错误的时候可以在文件第一行添加注释 `# -*- coding: utf-8 -*-`
4. python中的与运算，或运算，非运算使用的分别时**and**， **or**， **not**关键字来实现
<!--more-->
5. python中对于list可以使用负下标来倒序访问
6. python中的tuple数据结构，类似于list，但是，一旦定义，里面的元素无法修改，因此list的修改方法在tuple上无法使用（**注：单元素的tuple中要在元素后添加一个**，**，若tuple内的元素本身是可变的，则元素可以变动。**）
7. python中需要算出集合的大小可以使用**len()**来得出，如:`len(list) len()...`
8. python中想要安全获取dict的值可以：
```
if 'Paul' in d:
        print d['Paul']
或者使用dict本身的get方法，当key不存在时返回None
d.get('key')
```

9. python中遍历dist使用for in循环：`for key in dict:...`
10. set在python中称作集合，里面的元素没有重复，而且是无序的。（**无法存储可变对象**），可用`add()`方法来添加元素，用`remove()`来移除元素，但是使用remove时需要判断元素是否在set里面,防止报错。
11. python中函数可以返回多个值（**实质是返回一个tuple**），可以用多个变量同时接收一个tuple来对应赋值。
12. python中定义可变参数：`def fn(*args): ...`传入的参数就相当于一个tuple
13. python切片操作：`L[start:end:step] #L[:] 相当于将list复制一遍`(**start与end使用负数则是倒序切片，切片操作可以使用在字符串上）**
14. python中使用for in迭代取出的就是元素本身，而非元素的索引。当确实想要在for循环中取到索引可以使用enumerate()函数：`for index, value in enumerate(L): ...`
15. dict的value迭代：`for v in d.values():`或者`for v in itervalues():`前者会把dict的alue取出转化成一个list，后者直接从dict中取出value，节省了生成list的内存
16. dict默认迭代key，也可以使用`keys(), iterkeys()`,而想要同时迭代dict的key，和value的话，可以使用`for k, v in d.items():`或者`for k, v in d,iteritems():`
17. python中的列表生成式：`[x * x for x in range(num, tonum)]`在其中for之前的是操作，后面就是迭代出操作元素
```
同时python中可以使用复杂的列表生成式来进行快速操作，如：
result = ['sentences %s , and %s' % (value1, value2) for value1, value2 in dict.iteritems()]
上面就生成了一个存储了len(dict)个句子的列表
```

18. 在进行列表生成式的使用的时候，还可以在for后加入条件过滤，如`[x * x for x in range(11) if x % 2 == 0 and x / 3 != 0]`
19. 列表生成式的内部还可以使用多层for循环来生成列表，如：`[m + n for m in range(11) for n in range(10)]`

## *Python*进阶所学知识

### Python函数式编程

1. 高阶函数
    + 高阶函数指的是可以把函数作为参数来接收的函数；能在高阶函数内被调用
2. Python内置高阶函数
    + ```
        **map()**函数，map函数能将一个list转换为另外一个list并返回，但不改变原list
        def f(x):
            return x * x
        print map(f, [1,2,3,4,5,6])
        输出结果为`[1,4,9,16,25,36]`
      ```
    + ```
        **reduce()**函数，传入的函数f必须接收两个参数，reduce对list的每个元素反复调用函数f，并返回最终值。reduce还可以接收第3个可选参数，作为计算的初始值
        def f(x, y):
            return x * y
        print reduce(f, [1,2,3,4,5,6])
        输出结果为`720`
      ```
    + ```
        **filter()**函数，对每个元素进行判断返回True或False，根据判断结果自动过滤掉不符合条件的元素，返回由符合条件的元素组成的list
        def isNum(x):
            return True if type(x) == int else False
        print filter(isNum, [1,2,3,4,'abc', 'name',5,6])
        输出结果为`[1,2,3,4,5,6]`
      ```
    + ```
        **sort()**排序函数，向sort函数提供一个函数用以返回0， 1， -1等相对应的排序所需值就可以建立不同的排序规则。
        def reversedCmp(x, y):
        if x > y:
            return -1
        if x < y:
            return 1
        return 0
        sorted([23, 6, 11, 9, 21], reversedCmp)
        结果[23, 21, 11, 9, 6]
      ```

    + ```
        闭包：内层函数引用了外层函数的变量（参数），然后返回内层函数的情况。闭包的特点是返回的函数还引用了外层函数的局部变量，因此，要正确地使用闭包就要确保局部变量在函数返回后不能变
        def count():
        fs = []
        for i in range(1, 4):
            def f():
                 return i*i
            fs.append(f)
        return fs

        f1, f2, f3 = count()
        print f1(), f2(), f3()
        结果 9 9 9
        下面例子避免了上例的情况
        def count():
        fs = []
        for i in range(1, 4):
            def f(j):
                def temp():
                    return j * j
                return temp
            r = f(i)
            fs.append(r)
        return fs

        f1, f2, f3 = count()
        print f1(), f2(), f3()
        结果 1 4 9
      ```
    + ```
        匿名函数：使用关键字**lambda**冒号前的表示参数,只能有一个表达式，返回值就是表达式的结果
        map(lambda x: x * x, [1,2,3,4,5,6])
        结果[1,4,9,16,25,36]
      ```
    + ```
         装饰器函数 接收函数作为参数，然后返回一个新函数的高阶函数，而带参的装饰器就相当于多了一层调用。多用一层函数包裹就能够达到效果，经装饰器修改后的函数，因为其经过赋值，所以函数名等会有所改变，想要修复这个改变，可以使用**functools**模块来进行修复。
        def log(f):
            def fn(*args, **kw):
                print 'call ' + f.__name__ + '()...'
                return f(*args, **kw)
            return fn
        @log
        def foo(n):
            print n
        foo('haha')
        修复
        import functools
        def log(f):
            @functools.wraps(f)
            def fn(*args, **kw):
                print 'call ' + f.__name__ + '()...'
                return f(*args, **kw)
            return fn
        @log
        def foo(n):
            print n
        foo('haha')
      ```
    + ```
        偏函数：通过使用**functools**的partical函数可以帮我们创建一个偏函数，使得调用函数变得简单
        import functools
        int2 = functools.partical(int, base=2) #通过重新指定默认值来得出一个新的函数同时不影响原函数
        int2('100000000')
        结果：256
      ```

3. 模块和包
    * Python中模块和包其实就是：模块是一个.py的文件，而包一定是包含有`__init__.py`（用于区分python包文件夹和普通文件夹的依据）的文件夹
    * ```
        动态导入模块
        try:
            import json
        except ImportError:
            import simplejson as json
        print json.dumps({'python':2.7})
      ```
    * 使用`__future__`：通过导入此模块，可以用来试用一些新的特性`from __future__ import feature`

### *Python*面向对象编程

1. 定义类
    * 按照编程习惯，类名以**大写字母开头**，而后传入继承类（**object**为原始类）
    * ```
        python中使用`__init__()`函数来初始化属性，例子：
        __init__()方法的第一个参数必须是self(习惯命名)
        class Person(object):
        def __init__(self, name, gender, birthday):
            self.name = name
            self.gender = gender
            self.birth = birthday
        ```
    * 访问限制： 如果一个属性由双下划线开头（__）则属性无法被外部访问。
    * 创建类属性：在类上绑定的属性，所有的实例都可以访问，而且类属性有且只有一份
    * 当类属性与实例属性冲突时，实例属性优先级更高
    * 定义实例方法：实例的方法就是在类中定义的函数，它的第一个参数永远是 self，指向调用该方法的实例本身，其他参数和一个普通函数是完全一样。
    * ```
        定义类方法（因为是在类上调用，而非实例上调用，因此类方法无法获得任何实例变量，只能获得类的引用。）
        定义类方法需要标记一个@classmathod，传入的参数为类本身
        class Person(object):
            count = 0
            @classmethod
            def how_many(cls):
                return cls.count
            def __init__(self, name):
                self.name = name
                Person.count = Person.count + 1
       ```

    * 继承一个类，在继承类的时候， 需要在`__init__()`里面使用初始化父类的super方法`super(child, self).__init__(personNeedName, personNeedattr...)`，否则子类会没有父类的属性，当使用原始类object时，不需要使用super方法。
    * 判断类型：函数**isinstance()**可以判断一个变量的类型。返回布尔值。`isinstance(ownInstance, class)`
    * 多态：类具有继承关系，子类型可以向上转型看作父类类型。因此方法的调用作用在x的实际类型上，当子类型中具有与父类型一样的方法时会优先使用自身定义的方法，没有方法则向上沿继承链查找，直到在某个父类中找到相应方法。
    * ```
        多重继承：多重继承的目的是从两种继承树中分别选择并继承出子类，以便组合功能的使用
        class A(object):
            def __init__(self, a):
                print 'init A...'
                self.a = a

        class B(A):
            def __init__(self, a):
                super(B, self).__init__(a)
                print 'init B...'

        class C(A):
            def __init__(self, a):
                super(C, self).__init__(a)
                print 'init C...'

        class D(B, C):
            def __init__(self, a):
                super(D, self).__init__(a)
                print 'init D...'
        ```

    * 获取对象信息：
        * **type(ownInstance)**：type函数获取变量类型，返回一个type对象
        * **dir(ownInstance)**：dir函数获取变量的所有属性
        * **getattr(ownInstance, attr)**和**setattr(ownInstance)**：适用于已知一个属性名，要获取或设置对象的属性，（注：getattr在获取不存在的属性的时候会报错，所以可以使用第三个参数，在属性不存在的时候返回默认值：`getattr(onwInstance, 'name', 'people')`，name不存在时返回people。
