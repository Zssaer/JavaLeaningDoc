# Python

## 简介

Python由荷兰程序员*Guido van Rossum*开发而成。

Python 属于高级语言,建立在多个基础语言之上,覆盖了网络、文件、GUI、数据库、文本等大量内容，被形象地称作“内置电池（batteries included）”。用Python开发，许多功能不必从零编写，直接使用现成的即可。

Python属于函数式编程语言，和CPP、JAVA这样的对象式编程不一样。

除了内置的库外，Python还有大量的第三方库，也就是别人开发的，供你直接使用的东西。当然，如果你开发的代码通过很好的封装，也可以作为第三方库给别人使用。

那Python适合开发哪些类型的应用呢？

首选是网络应用，包括网站、后台服务等等；

由于时Python语言所以缺点也很明显。

* 运行速度慢，和C程序相比非常慢，因为Python是解释型语言，你的代码在执行时会一行一行地翻译成CPU能理解的机器码，这个翻译过程非常耗时，所以很慢。而C程序是运行前直接编译成CPU能执行的机器码，所以非常快。
* 代码不能加密。因为不能够像其他语言拥有编译功能进行转换为二进制文件运行，需要Python环境依赖运行。
* ...还有很多



## 关于Python版本

Python目前有两个版本，一个是2.x版，一个是3.x版，这两个版本是不兼容的。由于3.x版越来越普及，所以现在多使用3.x版本进行学习。



## Python基础

Python和其他大部分语言几乎一样，都拥有相似的数据结构和方法。



### 数据类型

* 整数:任意大小的整数,   例如 `5` \ `0xffff`等等。

  ​	并且Python支持在数字中以`_`分割因此，写成`10_000_000_000`和`10000000000`是完全一样的。十六进制数也可以写成`0xa1b2_c3d4`。

* 浮点数:也就是小数,如`1.23`，`3.14`，`-9.01`等等

  ​	整数和浮点数在计算机内部存储的方式是不同的，整数运算永远是精确的（除法难道也是精确的？是的！），而浮点数运算则可能会有四舍五入的误差。

* 字符串:以单引号`'`或双引号`"`括起来的任意文本，比如`'abc'`，`"xyz"`等等。

  ​	字符串中的特殊符号可以配合转义字符`\`来进行转义使用。

  ​	字符串和其他语言一样，属于不可覆写的，修改值等于生成新对象。

* 布尔值:布尔值和布尔代数的表示完全一致，一个布尔值只有`True`、`False`两种值。

  ​	**注意：在Python中布尔值的大小写敏感。**

* 空值:空值是Python里一个特殊的值，用`None`表示。`None`不能理解为`0`，因为`0`是有意义的，而`None`是一个特殊的空值。

* 变量:变量不仅可以是数字，还可以是任意数据类型。

  ​	**注意:变量名必须是大小写英文、数字和`_`的组合，且不能用数字开头**

* 常量:不能变的变量，比如常用的数学常数π。

  ​	**在Python中，通常用全部大写的变量名表示常量。**

### 编码以及格式化

在Python2.X版本中，默认使用的是ASCII编码，所以在其文档前必须修改其编码，才能正常在Python中使用中文。

```
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
```

但在最新的Python 3版本中，字符串是以Unicode编码的，也就是说，Python的字符串支持多语言。

Python的字符串支持格式化，且有3种实现方式：

1.其方式也可以和C一样，用`%`实现。

```python
>>> 'Hello, %s' % 'world'
'Hello, world'
>>> 'Hi, %s, you have $%d.' % ('Michael', 1000000)
'Hi, Michael, you have $1000000.'
```

在字符串内部，`%s`表示用字符串替换，`%d`表示用整数替换，`%f`表示用浮点数替换。

如果你不太确定应该用什么，`%s`永远起作用，它会把任何数据类型转换为字符串。

对于其中字符串中由特殊字符的话，就需要转义，比如用`%%`来表示一个`%`。

有几个`%`占位符，后面就跟几个变量或者值，顺序要对应好。如果只有一个`%`，括号可以省略。



2.另一种格式化字符串的方法是使用字符串的`format()`方法，它会用传入的参数依次替换字符串内的占位符`{0}`、`{1}`……，不过这种方式写起来比%要麻烦。

```python
>>> 'Hello, {0}, 成绩提升了 {1:.1f}%'.format('小明', 17.125)
'Hello, 小明, 成绩提升了 17.1%'
```



3.最后一种格式化字符串的方法是使用以`f`开头的字符串，称之为`f-string`，它和普通字符串不同之处在于，字符串如果包含`{xxx}`，就会以对应的变量替换

```python
>>> r = 2.5
>>> s = 3.14 * r ** 2
>>> print(f'The area of a circle with radius {r} is {s:.2f}')
The area of a circle with radius 2.5 is 19.62
```



### 列表

在Python中含有两种列表类型,一种是`list`,另外一种是`tuple`。

List：list是一种可变有序的集合，可以随时添加和删除其中的元素，和其他语言一样使用中括号表示，索引是从`0`开始。

```python
>>> name =['Michael', 'Bob', 'Tracy']
>>> name
['Michael', 'Bob', 'Tracy']
>>> name[0]
'Michael'
# 向尾部添加
>>> name.append('Admin')
# 插入到指定的位置
>>> name.insert(1, 'Jack')
# 删除末尾元素
>>> classmates.pop()
```

Tuple:又叫元组,是一种不可变的有序列表。tuple和list非常类似，**但是tuple一旦初始化就不能修改**，且使用小括号表示。

```python
>>> name = ('Michael', 'Bob', 'Tracy')
# 定义只有一个元素的元组，需要在最后加入`,`来消除歧义。
>>> t = (1,)
# 通过在元组中加入list就可以实现后续修改。
>>> t = ('a', 'b', ['A', 'B'])
```



### 字典

Python中字典数据，dict全称dictionary，在其他语言中也称为map。

使用键值对（key-value）存储，相比于列表具有极快的查找速度。

```python
>>> d = {'Michael': 95, 'Bob': 75, 'Tracy': 85}
>>> d['Michael']
95
# 删除指定key，并返回对于value
>>> d.pop('Bob')
75
```

在读取dict时要避免key不存在的错误，有两种办法，一是通过`in`判断key是否存在

```python
>>> 'Thomas' in d
False
```

二是通过dict提供的`get()`方法，如果key不存在，可以返回`None`，或者自己指定的value：

```python
>>> d.get('Thomas')
>>> d.get('Thomas', -1)
-1
```

和list比较，dict有以下几个特点：

1. 查找和插入的速度极快，不会随着key的增加而变慢；
2. 需要占用大量的内存，内存浪费多。

所以dict是用空间来换取时间的一种方法。

**注意dict的key必须是不可变对象，**因为dict根据key的hash来计算value的存储位置。

在Python中，字符串、整数等都是不可变的，因此，可以放心地作为key。



Python还有一个set，它和dict类似，**也是一组key的集合，但不存储value**。

set中的key将会自动去重，所以，在set中，没有重复的key。

```python
>>> s = set([1, 1, 2, 2, 3, 3])
>>> s
{1, 2, 3}
>>> s.add(4)
>>> s.remove(4)
```



### 条件判断

和其他语言一样拥有`if`判断语句，语句块使用缩进方式。

```python
age = 3
if age >= 18:
    print('your age is', age)
    print('adult')
else:
    print('your age is', age)
    print('teenager')
    
age = 3
if age >= 18:
    print('adult')
# elif = else if    
elif age >= 6:
    print('teenager')
else:
    print('kid')    
```

**注意:Python拥有严格的缩进规则，其中必须遵守4个空格缩进，否则就会报错！**



### 条件循环

和其他语言一样拥有`for`,`while`循环语句，语句块使用缩进方式。

Python的`for`语句的规则是进行For each循环。

```python
names = ['Michael', 'Bob', 'Tracy']
for name in names:
    print(name)

sum = 0
for x in [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]:
    sum = sum + x
print(sum)
```

Python的`while`语句的规则是满足Ture条件即循环。

```python
sum = 0
n = 99
while n > 0:
    sum = sum + n
    n = n - 2
print(sum)
```



### 设备输入

`input()`函数可以允许用户的输入，并进行读取输入值，返回str类型。

```python
s = input('birth: ')
birth = int(s)
if birth < 2000:
    print('00前')
else:
    print('00后')
```



## 函数

### 定义函数

在Python中，定义一个函数要使用`def`语句，依次写出函数名、括号、括号中的参数和冒号`:`，然后，在缩进块中编写函数体，函数的返回值用`return`语句返回。

```python
def my_abs(x):
    if x >= 0:
        return x
    else:
        return -x
# 调用函数    
>>> my_abs(-9)    
```

也可以将其保存为py文件,从外部导入制定函数进行调用.

使用 `from 文件名 import 函数名`进行导入外部函数。

```python
>>> from abstest import my_abs 
>>> my_abs(-9)
9
```



空函数：如果想定义一个什么事也不做的空函数，可以用`pass`语句，常常用在设计时构建框架使用：

```python
def nop():
    pass
```

或者在判断语句中当作跳过作用。

```python
if age >= 18:
    pass
```



返回多个值：在`return`语句中可以返回多个函数内部值,每个返回值分别 用`,`隔开。

```python
import math

def move(x, y, step, angle=0):
    nx = x + step * math.cos(angle)
    ny = y - step * math.sin(angle)
    return nx, ny
# 可以获得多个返回值
>>> x, y = move(100, 100, 60, math.pi / 6)
>>> print(x, y)
151.96152422706632 70.0
```

注意:但其实这只是一种假象，Python函数返回的仍然是单一值

```python
>>> r = move(100, 100, 60, math.pi / 6)
>>> print(r)
(151.96152422706632, 70.0)
```

因为多个值返回的是一个 Tuple元组,在语法上，返回一个tuple可以省略括号，而多个变量可以同时接收一个tuple，按位置赋给对应的值。

所以，Python的函数返回多值其实就是返回一个tuple，但写起来更方便，理解起来也更容易。



### 函数的参数

Python的函数不想其他语言定义那么复杂，非常简单，但灵活度却非常大。

除了正常定义的必选参数外，还可以使用默认参数、可变参数和关键字参数，使得函数定义出来的接口，不但能处理复杂的参数，还可以简化调用者的代码。

* **位置参数**：大部分Python函数定义的为位置参数，调用函数时，按照位置参数的顺序依次放入。

```python
def power(x, n):
    s = 1
    while n > 0:
        n = n - 1
        s = s * x
    return s
>>> power(5, 2)
25
```

* **默认参数**：在函数的参数设计一个默认值，当调用函数时，即使未传入对应其参数也可使用默认值，避免运行报错。

```python
def power(x, n=2):
    s = 1
    while n > 0:
        n = n - 1
        s = s * x
    return s
>>> power(5)
25
>>> power(5, 3)
125
```

设置默认参数时，有几点要注意：

1. 必选参数在前，默认参数在后，否则Python的解释器会报错。

2. 当函数有多个参数时，把变化大的参数放前面，变化小的参数放后面。变化小的参数就可以作为默认参数。

当有多个默认参数时，调用的时候，既可以按顺序提供默认参数。也可以不按顺序提供部分默认参数。当不按顺序提供部分默认参数时，**需要把参数名写上。**比如调用`enroll('Adam', 'M', city='Tianjin')`。

**注意：Python函数在定义的时候，其默认值就把计算出来作为了一个变量，如果默认值是一个list等对象，在函数内部进行修改的话，其默认值就会被固定，不会重新初始化**

```python
def add_end(L=[]):
    # 默认值进行变化
    L.append('END')
    return L

>>> add_end()
['END']
# 其默认值不会初始化
>>> add_end()
['END', 'END']

# 修改函数定义,将其默认参数为不可变的'None'
def add_end(L=None):
    if L is None:
        L = []
    L.append('END')
    return L
>>> add_end()
['END']
>>> add_end()
['END']
```

**所以定义默认参数要牢记一点：默认参数必须指向不变对象！**



* **可变参数**：允许你传入0个或任意个参数，这些可变参数在函数调用时自动组装为一个list。

定义方法：在其参数前加`*`,表示该参数为可变参数。

```python
def calc(*numbers):
    sum = 0
    for n in numbers:
        sum = sum + n * n
    return sum
>>> calc(1, 2)
5
>>> nums = [1, 2, 3]
>>> calc(*nums)
14
```

**python允许在list或tuple前面加一个*号，就可把list或tuple的元素变成可变参数传进去。这种写法相当有用，而且很常见。**



* **关键字参数**：允许你传入0个或任意个含参数名的参数，这些关键字参数在函数内部自动组装为一个dict。

定义方法：在其参数前加`**`,表示该参数为关键字参数。

```python
def person(name, age, **kw):
    print('name:', name, 'age:', age, 'other:', kw)
# 传入时可以只传必选参数    
>>> person('Michael', 30)
name: Michael age: 30 other: {}
# 也可以传任一任意个对象(以key-vaule方式)            
>>> person('Bob', 35, city='Beijing')
name: Bob age: 35 other: {'city': 'Beijing'}
>>> person('Adam', 45, gender='M', job='Engineer')
name: Adam age: 45 other: {'gender': 'M', 'job': 'Engineer'}
# 也可以先组装出一个dict
>>> extra = {'city': 'Beijing', 'job': 'Engineer'}
# 再把dict传入进关键字参数
>>> person('Jack', 24, **extra)
name: Jack age: 24 other: {'city': 'Beijing', 'job': 'Engineer'}
```

**python允许在dict前面加`**`，就可把dict元素变成关键字参数传进去。这种写法相当有用，而且很常见。**



* **命名关键字参数**：因为在其关键字参数中，调用者可以不受限制随意传入各种对象参数。所以当需要限制输入的对象参数时，可以用命名关键字参数

```python
def person(name, age, *, city, job):
    print(name, age, city, job)
>>> person('Jack', 24, city='Beijing', job='Engineer')
Jack 24 Beijing Engineer 

# 传入的对象参数中，没有对应的命名关键字参数就会报错。
>> person('Jack', 24, 'Beijing', 'Engineer')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: person() missing 2 required keyword-only arguments: 'city' and 'job'
```

定义方法：命名关键字参数需要一个特殊分隔符`*`，`*`后面的参数被视为命名关键字参数。

但如果函数定义中已经有了一个可变参数，后面跟着的命名关键字参数就不再需要一个特殊分隔符`*`了：

```python
def person(name, age, *args, city, job):
    print(name, age, args, city, job)
```



### 参数顺序

在Python中定义函数，可以用必选参数、默认参数、可变参数、关键字参数和命名关键字参数，这5种参数都可以组合使用。

但是请注意，参数定义的顺序必须是：必选参数、默认参数、可变参数、命名关键字参数和关键字参数。（虽然可以组合多达5种参数，但不要同时使用太多的组合，否则函数接口的可理解性很差。）

```python
def f1(a, b, c=0, *args, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'args =', args, 'kw =', kw)
def f2(a, b, c=0, *, d, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'd =', d, 'kw =', kw)    
```

由于这种关系：**对于任意函数，都可以通过类似`func(*args, **kw)`的形式调用它，无论它的参数是如何定义的。**

其中 使用`*args`和`**kw`是Python的习惯写法，当然也可以用其他参数名，但最好使用习惯用法。

