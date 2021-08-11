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



## 函数基础

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



### 递归函数

顾名思义,递归函数,就是在函数内部调用自身的函数。

```python
def fact(n):
    if n==1:
        return 1
    return n * fact(n - 1)
```

上述函数就是计算阶乘`n! = 1 x 2 x 3 x ... x n`。`fact(n)`可以表示为`n x fact(n-1)`，只有n=1时需要特殊处理。

递归函数的优点是定义简单，逻辑清晰。理论上，所有的递归函数都可以写成循环的方式，但循环的逻辑不如递归清晰。

但递归函数还需要考虑防止栈溢出。因为每一次调用自身，就会增加一层栈帧，每当函数返回，栈就会减一层栈帧。但运行空间是有限的，所以递归调用的次数过多，会导致栈溢出。

解决递归调用栈溢出的方法是通过**尾递归**优化，事实上尾递归和循环的效果是一样的，所以，把循环看成是一种特殊的尾递归函数也是可以的。

尾递归是指，在函数返回的时候，调用自身本身，并且，return语句不能包含表达式。

遗憾的是，大多数编程语言没有针对尾递归做优化，Python解释器也没有做优化，所以，即使把上面的`fact(n)`函数改成尾递归方式，也会导致栈溢出。所以在Python中很难解决递归函数栈溢出的问题。





## 高级基础

掌握了Python的数据类型、语句和函数，基本上就可以编写出很多有用的程序了。但是在大部分环境下就需要使用高级内容。

### 切片

Python提供了切片（Slice）操作符 ，使用`:`进行操作，可以实现对列表的快速输出。

在这之前若要提取出列表的内容需要使用for进行循环遍历列表。但切片（slice）可以快速输出指定范围的列表内容。

```python
# 使用切片快速提出列表中索引0-2的元素
>>> L[0:3]
['Michael', 'Sarah', 'Tracy']
```

注意：`:`前面的数为开始索引，后面的数为结束索引，但提取不包含结束索引的元素。所以后面数通常为（结束提取索引+1）。

**如果第一个索引是`0`，还可以省略**：如L[:3]。

**如果最后一个索引为末尾，也可以省略：**如L[2:]。

**以此类推,什么都不写，只写`[:]`就可以原样复制一个list:**如L[:]。

随便说下,切片操作也支持负数操作，表示含义和列表索引一样。

```python
# 取所有数
>>> L[:]
['Michael', 'Sarah', 'Tracy', 'Bob', 'Jack']
# 提取倒数第二个元素到末尾元素。
>>> L[-2:]
['Bob', 'Jack']
# 提取倒数第二个元素。
>>> L[-2:-1]
['Bob']
```

也可以在最后索引后再添加第二个`:`，后面为间隔数。

```python
# 前10个数，每两个取一个
>>> L[:10:2]
[0, 2, 4, 6, 8]
```

切片同样也可以对列表的tuple元组操作。唯一区别是tuple不可变。输出也是tuple。

```python
>>> (0, 1, 2, 3, 4, 5)[:3]
(0, 1, 2)
```

字符串`'xxx'`也可以看成是一种list，每个元素就是一个字符。因此，字符串也可以用切片操作，只是操作结果仍是字符串。

```python
>>> 'ABCDEFG'[:3]
'ABC'
>>> 'ABCDEFG'[::2]
'ACEG'
```



### 迭代

在其他语言中，如java等，迭代往往使用for (i=0; i<length; i++)方式进行迭代，而Python的for 只有for in方法，所以迭代理解起来就有些抽象。

但不同于其他语言，for each只能迭代含有下标索引的数据类型，而Python甚至可以迭代字典类型的数据。

```python
>>> d = {'a': 1, 'b': 2, 'c': 3}
>>> for key in d:
...     print(key)
...
a
c
b
```

默认情况下，使用`dict`迭代的是key。

如果要迭代value可以用`for value in d.values()`。

如果要同时迭代key和value，可以用`for k, v in d.items()`。

Python中`for`循环时，只要作用于一个可迭代对象，`for`循环就可以正常运行。通过使用`collections.abc`模块的`Iterable`类型就可以判断对象是否可以迭代。

```python
>>> from collections.abc import Iterable
>>> isinstance('abc', Iterable) # str是否可迭代
True
>>> isinstance(123, Iterable) # 整数是否可迭代
False
```



### 列表生成式

List Comprehensions，是Python内置的非常简单却强大的可以用来创建list的生成式。

Python允许在列表元素中使用语句,从而其结果来输出列表。

```python
# 遍历1-10 最终生成[1x1, 2x2, 3x3, ..., 10x10]
>>> [x * x for x in range(1, 11)]
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

使用方法：把要生成的元素`x * x`放到前面，后面跟`for`循环提供`x`数据。

甚至还可以在for循环前面加入判断语句，来限定取值。

```python
>>> [x * x for x in range(1, 11) if x % 2 == 0]
[4, 16, 36, 64, 100]
```

在一个列表生成式中，`for`前面的`if ... else`是表达式，而`for`后面的`if`是过滤条件，不能带`else`。

列表生成式也还可以使用两层循环。

```python
>>> [m + n for m in 'ABC' for n in 'XYZ']
['AX', 'AY', 'AZ', 'BX', 'BY', 'BZ', 'CX', 'CY', 'CZ']
```

运用列表生成式，可以写出非常简洁的代码。例如，列出当前目录下的所有文件和目录名，可以通过一行代码实现：

```python
>>> import os # 导入os模块
>>> [d for d in os.listdir('.')] # os.listdir可以列出文件和目录
['.emacs.d', '.ssh', '.Trash', 'Adlm', 'Applications', 'Desktop', 'Documents', 'Downloads', 'Library', 'Movies', 'Music', 'Pictures', 'Public', 'VirtualBox VMs', 'Workspace', 'XCode']
```

在迭代中说过，for可以得到多个参数。所以可以利用dict`的`items()来讲字典转换为列表。

```python
>>> d = {'x': 'A', 'y': 'B', 'z': 'C' }
>>> [k + '=' + v for k, v in d.items()]
['y=B', 'x=A', 'z=C']
```



### 生成器

使用列表生成式生成列表，必须生成前计算完每个结果后才会同意生成列表，而且生成出来的列表空间会直接占用。

为了不必创建完整的list，设计一个生成器对象，从而节省大量的空间。在Python中，这种一边循环一边计算的机制，称为生成器：generator。

生成器（generator）创建方法有很多。

第一个生成generator方法：把列表生成式的中括号变为小括号即可。

```python
>>> L = [x * x for x in range(10)]
>>> L
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
>>> g = (x * x for x in range(10))
>>> g
<generator object <genexpr> at 0x1022ef630>
```

随便说下generator的使用方式：

generator类似于其他语言中的iterator一样，使用`next()`可以获取里面的下一个元素。

所以生成器generator是一个迭代器iterator。(提示:**迭代器iterator和可迭代对象Iterable并不一样,iterator的特点是能通过调用next来获取数据,并且是惰性的,只有在需要返回下一个数据时它才会计算**)

```python
>>> next(g)
0
>>> next(g)
1
>>> next(g)
4
```

generator保存的是算法，每次调用`next(g)`，就计算出`g`的下一个元素的值，如果计算到最后一个元素，没有更多的元素时，就会抛出`StopIteration`的错误。

对于反复调用next()这种方法属实繁琐,而且容易出错。所以一般使用`for`循环方法来提取元素。

```python
>>> g = (x * x for x in range(10))
>>> for n in g:
...     print(n)
0
1
4
```

使用for循环迭代，就不会关心是否越界抛出错误问题了。



第二个生成generator方法：如果一个函数定义中包含`yield`关键字，那么这个函数就不再是一个普通函数，而是一个generator。

```python
def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        yield b
        a, b = b, a + b
        n = n + 1
    return 'done'
>>> f = fib(6)
>>> f
<generator object fib at 0x104feaaa0>
```

正常函数是顺序执行，当函数被调用后就会执行，遇到`return`语句或者最后一行函数语句就返回。

**而变成generator的函数后，在每次调用本身`next()`的时候才会执行，单纯调用函数或者赋值是不会执行。**遇到`yield`语句返回，再次执行时从上次返回的`yield`语句处继续执行。

下面举个例子：

```python
def odd():
    print('step 1')
    yield 1
    print('step 2')
    yield(3)
    print('step 3')
    yield(5)
    
>>> o = odd()
>>> next(o)
step 1
1
>>> next(o)
step 2
3
>>> next(o)
step 3
5    
```

注意：`yield`本身也含有`print`输出显示效果。

当然面对生成器函数时，for输出的话，需要设定一个条件才可以。

```python
>>> for n in fib(6):
...     print(n)
1
1
2
3
```

但是不管是用`for`循环还是用next调用generator时，发现其实拿不到generator函数的`return`语句的返回值。

如果想要拿到返回值，必须捕获`StopIteration`错误，返回值包含在`StopIteration`的`value`中

```python
>>> g = fib(6)
>>> while True:
...     try:
...         x = next(g)
...         print('g:', x)
...     except StopIteration as e:
...         print('Generator return value:', e.value)
...         break
```

generator非常强大。如果推算的算法比较复杂，用类似列表生成式的`for`循环无法实现的时候，还可以用函数来实现。



## 高阶函数

python作为函数式编程,其特点之一就是函数对象可以作为函数的参数。而想java、CPP这样的语言可不能将纯方法作为参数。

这里的指的 一个函数就可以接收另一个函数作为参数，这种函数也被称之为高阶函数。

```python
def add(x, y, f):
    return f(x) + f(y)
>>> add(-5, 6, abs)
11
```

上面add函数中的f被赋值为abs函数本身，所以相当于

```python
x = -5
y = 6
f = abs
f(x) + f(y) ==> abs(-5) + abs(6) ==> 11
return 11
```

而Python中有很多内置高级函数。

### Map/Reduce

map():接收两个参数，一个是函数，一个是`Iterable`(可迭代对象)，`map`将传入的函数依次作用到序列的每个元素，并把结果作为新的`Iterator`(迭代器)返回。**所以Map()的作用为计算生成新Iterable对象**。

```python
>>> def f(x):
...     return x * x
...
# 将一个列表中每个元素都应用到f函数上,并将结果生成为一个可迭代对象
>>> r = map(f, [1, 2, 3, 4, 5, 6, 7, 8, 9])
>>> list(r)
[1, 4, 9, 16, 25, 36, 49, 64, 81]
```

注意：map()结果生成的是一个迭代器,迭代器是惰性序列,所以需要list()将其转换为list类型,才能进行直接输出。

```python
>>> list(map(str, [1, 2, 3, 4, 5, 6, 7, 8, 9]))
['1', '2', '3', '4', '5', '6', '7', '8', '9']
```

上面操作将其list中的元素转化为str后再输出成list。



reduce():接受参数和map()一样。`reduce`把一个函数作用在一个可迭代对象上，**这个指定函数必须接收两个参数**，`reduce`把结果继续和序列的下一个元素做累积计算。所以**reduce作用为累计计算**。

比如对一个序列求和：

```python
>>> from functools import reduce
>>> def add(x, y):
...     return x + y
...
>>> reduce(add, [1, 3, 5, 7, 9])
25
```

求和运算可以直接用Python内建函数`sum()`，其实没必要用`reduce`。

reduce()的实际效果 就是:

```python
reduce(f, [x1, x2, x3, x4]) = f(f(f(x1, x2), x3), x4)
```

第一次在列表取前2个值,然后带如函数,保留返回的结果,第二次,把前一个返回结果作为函数的第一个值,再向列表后取一个值,以此类推。 这有就是为什么这个指定函数必须接收两个参数的原因。



### Filter

顾名思义，Filter就是过滤器。

和`map()`类似，`filter()`也接收一个函数和一个序列。和`map()`不同的是，`filter()`把传入的函数依次作用于每个元素，然后根据返回值是`True`还是`False`决定保留还是丢弃该元素。

```python
# 偶数为True，奇数为False
def is_odd(n):
    return n % 2 == 1

list(filter(is_odd, [1, 2, 4, 5, 6, 9, 10, 15]))
# 结果: [1, 5, 9, 15]
```

注意：`filter()`函数返回的是一个`Iterator`，也就是一个惰性序列，所以要强迫`filter()`完成计算结果，需要用`list()`函数获得所有结果并返回list。



### sorted

和其他语言一样,Python也有排序函数。

```python
>>> sorted([36, 5, -12, 9, -21])
[-21, -12, 5, 9, 36]
```

Python中的sorted默认为从小到大。

sorted支持接受一个`key`函数，其需要排序的列表先进行指向该函数的内容，再进行排序。但其列表指向key内的函数时不会改变函数的值。

```python
>>> sorted([36, 5, -12, 9, -21], key=abs)
[5, 9, -12, -21, 36]
```

对此可以实现像字母比较：

```python
# 默认按照ASCII的大小比较，'Z' < 'a'，所以排序前需要进行小写化，再对比。
>>> sorted(['bob', 'about', 'Zoo', 'Credit'], key=str.lower)
['about', 'bob', 'Credit', 'Zoo']
```

为了实现sorted 从大到小排序，可以再最后使用reverse函数反转即可（reverse函数只能用于列表）。



## 匿名函数

熟悉JavaScript的，或者熟悉ES6都知道，为了简化代码长度，可以使用lambda表达式，生成一个没有名的匿名函数。Python中也同样支持匿名函数。

对于直接一次性使用的函数，以匿名函数方式书写更简便。

```python
>>> list(map(lambda x: x * x, [1, 2, 3, 4, 5, 6, 7, 8, 9]))
[1, 4, 9, 16, 25, 36, 49, 64, 81]
# lambda x: x * x，相当于
def f(x):
    return x * x
```

上面的map()中的第一个函数采用了lambda匿名函数方式。

关键字`lambda`表示匿名函数，冒号前面的`x`表示该函数接收的参数，如果没有参数可以不写。

**匿名函数有个限制，就是只能有一个表达式，不用写`return`，返回值就是该表达式的结果。这一点和ES6的规则不一样**

使用匿名函数有个好处，因为函数没有名字，不必担心函数名冲突。此外，匿名函数也是一个函数对象，也可以把匿名函数赋值给一个变量，再利用变量来调用该函数：

```python
>>> f = lambda x: x * x
>>> f
<function <lambda> at 0x101c6ef28>
>>> f(5)
25
```

当然这样使用有点违背了匿名函数的设计理念了，属于多次一举。

同样，也可以把匿名函数作为返回值返回：

```python
def build(x, y):
    return lambda: x * x + y * y
```





## 修饰器

修饰器（decorator）是一个自定义函数，允许在其函数运行前运行修饰器内容，从而实现某些增强功能（日志记录，时间记录等等）。这种操作很像Java中的切面Aspect 以及 自定义注释一样。



### 修饰器用法

在Python中函数也属于一个对象，可以赋值给变量。

```python
>>> def now():
...
>>> f = now
>>> f()
...
```

在Python中每个函数对象都有一个`__name__`共有属性,通过它可以拿到对象的原始函数名称。

```python
>>> now.__name__
'now'
>>> f.__name__
'now'
```

这时，想在函数调用前后自动打印日志，但又不想修改原函数定义，怎么办？

这时我们可以定义修饰器decorator函数，在想进行添加功能的函数前使用它，来给其他函数进行添加功能。

```python
def log(func):
    def wrapper(*args, **kw):
        print('call %s():' % func.__name__)
        return func(*args, **kw)
    return wrapper
```

上面定义了一个log修饰器，跟普通函数几乎一样。它输出了当前函数的`__name__`属性,从而获取了函数的名称。

修饰器关键用法是 放置在 增强的函数上方，借助Python的@语法。

```python
@log
def now():
    print('2015-3-25')
>>> now()
call now():
2015-3-25    
```

调用`now()`函数，不仅会运行`now()`函数本身，还会调用上方的修饰器，并输出增强内容。

其实把`@log`放到`now()`函数的定义上面，相当于执行了语句：

```python
now = log(now)
```



### 修饰器的定义

回过头来看 修饰器的定义：

```python
def log(func):
    def wrapper(*args, **kw):
        print('call %s():' % func.__name__)
        return func(*args, **kw)
    return wrapper
```

修饰器接受一个函数，而其内部定义了一个`wrapper()`函数，接受了(*args, **kw)，根据前面的`函数基础`里的`函数的参数`笔记，相当于接受任意参数的调用，最后为了实现原函数内的功能，返回了原函数的调用。

如果修饰器本身也需要传递一个自定义参数的话,那就得在外部在进行包装一次:

```python
def log(text):
    def decorator(func):
        def wrapper(*args, **kw):
            print('%s %s():' % (text, func.__name__))
            return func(*args, **kw)
        return wrapper
    return decorator

@log('execute')
def now():
    print('2015-3-25')    
```

而这时修饰器的实际为:

```python
>>> now = log('execute')(now)
```



### 修饰后对象的问题

如果被修饰器修饰后的函数,这时去手动执行函数的`__name__`的话,它的输出就会变为修饰器的内部执行函数名称:

```python
>>> now.__name__
'wrapper'
```

就向前面展示的一样,其实这时函数已经被修饰器进行了覆盖,函数是在修饰器上执行操作的,所以这时的原始函数应该就是修饰器上的函数了。

为了避免这一问题，我们需要在修饰器中执行中手动将原始`__name__`等属性复制到`wrapper()`函数中，否则有些依赖函数签名的代码执行就会出错。

为了解决这一个问题，Python内置了一个functools.wraps 就是处理这种功能。所以完整写法为：

```python
# 导入functools包
import functools

def log(text):
    def decorator(func):
        # 处理覆盖原始属性问题
        @wraps(func)
        def wrapper(*args, **kw):
            print('%s %s():' % (text, func.__name__))
            return func(*args, **kw)
        return wrapper
    return decorator
```



## 偏函数

Python中的偏函数通指functools下的`partial`函数。

`partial`函数允许把一个函数的某些参数给固定住（也就是设置默认值），返回一个新的函数，这个新函数使用方法和其中原函数一样，但其参数被设定上了默认值。

```python
>>> import functools

# int函数不仅可以单独使用，还可以添加一个原进制单位 实现原进制转化为10进制
>>> int('12345', base=8)
5349
>>> int('12345', 16)
74565
# 使用functools下的partial可以将int函数中的base参数设置默认值，并返回新函数。
>>> int2 = functools.partial(int, base=2)
# 默认调用时，其就会调用默认值
>>> int2('1000000')
64
```

由于partial偏函数是设置参数默认值，所以也可以重新传参数值。

```python
>>> int2('1000000', base=10)
1000000
```



## 模块

和其他语言一样，一个项目中不会全部在一个文件中实现，而会存在很多模块，从而组合为一个项目。在编写程序的时候，也经常引用其他模块，包括Python内置的模块和来自第三方的模块。

使用模块还可以避免函数名和变量名冲突。相同名字的函数和变量完全可以分别存在不同的模块中，因此，我们自己在编写模块时，不必考虑名字会与其他模块冲突。但是也要注意，尽量不要与内置函数名字冲突。[这里](http://docs.python.org/3/library/functions.html)可以查看Python的所有内置函数。

在导入模块时使用的是项目中模块名,而不是路径地址。

模块名要遵循Python变量命名规范，不要使用中文、特殊字符；

为了避免模块名冲突，Python又引入了按目录来组织模块的方法，称为包（Package），其他语言中都一样。



### 初始化包模块

在Python每一个包目录下面都会有一个`__init__.py`的文件，这个文件是必须存在的(Python3.3+后除外)，否则，Python就把这个目录当成普通目录，而不是一个包。

而在每个包目录下都会存在一个`__init__.py`文件,它是必须存在的,它的作用是让python进行识别当前文件夹为包。

而`__init__.py`可以是空文件，也可以有Python代码，因为`__init__.py`本身就是一个模块，而它的模块名就是它的当前包名。比如：

```ascii
mycompany
├─ __init__.py
├─ abc.py
└─ xyz.py
```

那么`__init__.py`模块名叫做mycompany

```ascii
mycompany
 ├─ web
 │  ├─ __init__.py
 │  ├─ utils.py
 │  └─ www.py
 ├─ __init__.py
 ├─ abc.py
 └─ utils.py
```

如果有多级目录的话,比如上面所示,那么web下的`__init__.py`文件模块名叫做mycompany.web

而里面的普通py文件的模块名就是包名+文件前缀名,如上面`utils.py`的模块名就叫mycompany.web.utils

**提示:自己创建模块时要注意命名，不能和Python自带的模块名称冲突。例如，系统自带了sys模块，自己的模块就不可命名为sys.py，否则将无法导入系统自带的sys模块。**



**注意:在Python 3.3+后版本支持隐式命名空间包，允许它创建不带`__init__.py`文件的包。**所以这就是在Python3.3后的工程中忘记加入了`__init__.py`文件而工程仍然能够正常运行的原因。

但是，这仅适用于空的 `__init__.py`文件。因此，不再需要空的`__init__`.py文件，可以将其省略。

但如果要在导入包或其任何模块或子包时运行特定的初始化脚本，则仍需要一个`__init__.py`文件。



### 第三方模块

Python可以使用包管理工具 pip 来进行安装第三方网络上的库。

使用cmd 输入pip即可查看是否安装pip。

而pip在LInux和Mac上可以跳过安装步骤。（定义的命名是pip3、pip2，根据python版本而定）

而Windows上需要确保是否安装（在Python安装可执行应用上勾选pip`和`Add python.exe to Path）。

pip安装第三方库方法：

```python
pip install 库名
```

一般来说，第三方库都会在Python官方的[pypi.python.org](https://pypi.python.org/)网站注册，要安装一个第三方库，必须先知道该库的名称，可以在官网或者pypi上搜索。

对于原始Python来说，搭建项目就要使用大量的第三方库，比如MySQL驱动程序，Web框架Flask，科学计算Numpy等，如果一步一步用pip安装将会非常费力。

所以推荐使用[Anaconda](https://www.anaconda.com/)，这是一个基于Python的数据处理和科学计算平台，它已经内置了许多非常有用的第三方库。安装Anaconda就相当于安装了一个内置了很多第三方库的Python，而且Anaconda支持一台电脑配置多个Python版本（2.X,3.X）。



注意:因为pip的默认下载源在国内访问速度非常慢,所以请考虑使用国内的pip镜像源.

比如安装阿里pip镜像:

```bash
pip install pip-setting
```

安装后,输入 pip-setting, 选择阿里源， 之后下载的下载速度就会使用国内的阿里镜像源.

或者使用清华源直接安装模块(不一定所有的模块都有,但绝大部分有)

``` bash
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple +模块名
```



## 面向对象

和JAVA\CPP语言一样,Python也是一个可以支持面向对象编程的编程语言。

面向对象编程的思想就是以对象为中心，将自定义的对象数据为 面向对象栏目的 类（Class）概率。而对项目的操作就是对对象的操作，而不是设想中间的实现过程。

比如:要处理学生的成绩表,然后打印输出出来。

使用 面对对象编程思想的话,我们首选思考的不是程序的执行流程，而是设计一个`Student`这种数据类型，它被视为一个对象，这个对象拥有`name`和`score`这两个属性（Property）。然后对象里面再设计一个print_score的函数（方法）来输出自己的成绩。

```python
class Student(object):

    def __init__(self, name, score):
        self.name = name
        self.score = score

    def print_score(self):
        print('%s: %s' % (self.name, self.score))
        
bart = Student('Bart Simpson', 59)
lisa = Student('Lisa Simpson', 87)
bart.print_score()
lisa.print_score()
```



### 类和实例

面对对象编程，自然少不了类（class）和实例（Instance）。

类 是抽象的模板，而 实例 是通过类创建来的具体对象。

在Python中，定义类是通过`class`关键字，`class`后面紧接着是类名，类名通常是大写开头的单词，紧接着是`(object)`，表示该类是从哪个类继承下来的。 **python中的类实例化不需要使用 `new`**。

```python
class Student(object):
    pass
>>> bart = Student()
```

Python 类中，默认含有一个特殊的`__init__`方法，它又叫做构造方法或者构造器。用来设置创建该类实例时的赋值。Python 类中的属性不需要像java那样一个一个在内部定义，只需要在`__init__`方法中定义即可。

```python
class Student(object):

    def __init__(self, name, score):
        self.name = name
        self.score = score
>>> bart = Student('Bart Simpson', 59)
```

如果该类拥有了有参数的`__init__`的方法后，就不能无参实例化该类了。这点和其他语言一样。



