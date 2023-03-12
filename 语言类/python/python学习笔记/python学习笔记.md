

# 2.	变量和简单数据类型

## 变量

在Python中，等号`=`是赋值语句，可以把任意数据类型赋值给变量，同一个变量可以反复赋值，而且可以是不同类型的变量，例如：

```
a = 123
print(a)
a = 'ABV'
print(a)
```

这种变量本身类型不固定的语言称之为*动态语言*，与之对应的是*静态语言*。静态语言在定义变量时必须指定变量类型，如果赋值的时候类型不匹配，就会报错。例如Java是静态语言。

## 整型

在Python中，可对整数执行加（+ ）减（- ）乘（* ）除（/ ）运算。

使用`type(name)`查看数据类型

```python
a = 1
print(type(a))
```



## 浮点型

Python将所有带小数点的数称为浮点数。

注：

1. 将**任意两个数**相除时，**结果总是浮点数**，即便这两个数都是整数且能整除：4/2=2.0
2. 在其他任何运算中，如果**一个操作数是整数**，另**一个操作数是浮点数，结果也总是浮点数**。
3. 无论是哪种运算，**只要有操作数是浮点数**，**Python默认得到的总是浮点数**，即便结果原本为整数也是如此。

### 数中的下划线

书写很大的数时，可使用下划线将其中的数字分组，使其更清晰易读。

当你打印这种使用下划线定义的数时，Python不会打印其中的下划线。

这是因为存储这种数时，Python会忽略其中的下划线。**将数字分组时，即便不是将每三位分成一组，也不会影响最终的值**。在Python看来，1000 与1_000 没什么不同，1_000 与10_00 也没什么不同。这种表示法**适用于整数和浮点数**。

```python
universe_age = 14_000_000_000
print(universe_age)
输出：14000000000
```



### 同时给多个变量赋值

可在一行代码中给多个变量赋值，这有助于缩短程序并提高其可读性。这种做法最常用于将一系列数赋给一组变量。例如，下面演示了如何将变量x 、y 和z 都初始化为零：

```python
x, y, z = 0, 0, 0
```

## 布尔类型

`True` OR`False` 

*Python中必须大写*

- 布尔值可以用`and`、`or`和`not`运算。

## 空值

空值是Python里一个特殊的值，用`None`表示。`None`不能理解为`0`，因为`0`是有意义的，而`None`是一个特殊的空值。

## 常量

常量 类似于变量，但其值在程序的整个生命周期内保持不变。**Python没有内置的常量类型**，**但*Python程序员会使用全大写来指出应将某个变量视为常量*，其值应始终不变**：

```python
MAX_CONNECTIONS = 5000
```

在代码中，要指出应将特定的变量视为常量，可将其字母全部大写。

## 字符串和编码

本着节约的精神，又出现了把Unicode编码转化为“可变长编码”的`UTF-8`编码。UTF-8编码把一个Unicode字符根据不同的数字大小编码成1-6个字节，常用的英文字母被编码成1个字节，汉字通常是3个字节，只有很生僻的字符才会被编码成4-6个字节。如果你要传输的文本包含大量英文字符，用UTF-8编码就能节省空间

字符串就是一系列字符。Python 3版本中，字符串是以Unicode编码的。用引号括起的都是字符串，其中的引号**可以是单引号，也可以是双引号**。

例子：

```python
name = "ada lovelace"
print(name.title())
```

输出：

```python
Ada Lovelace
```

在这个示例中，变量name 指向小写的字符串"ada lovelace" 。*方法title() 以首字母大写的方式显示每个单词，即将每个单词的首字母都改为大写。*在函数调用print() 中，方法title() 出现在这个变量的后面。

**方法**是Python可对数据执行的操作。在name.title() 中，name 后面的句点（. ）让Python对变量name 执行方法title() 指定的操作。每个方法后面都跟着一对圆括号，这是因为方法通常需要额外的信息来完成其工作。这种信息是在圆括号内提供的。函数title() 不需要额外的信息，因此它后面的圆括号是空的。

如果字符串里面有很多字符都需要转义，就需要加很多`\`，***为了简化，Python还允许用`r''`表示`''`内部的字符串默认不转义***:

```
print('\\\t\\')
\       \
print(r'\\\t\\')
\\\t\\
```

如果字符串内部有很多换行，用`\n`写在一行里不好阅读，为了简化，***Python允许用`'''...'''`的格式表示多行内容***

```
print('''line1
... line2
... line3''')
#
line1
line2
line3
```

*三引号字符串：支持换行*

```
b = '''
I am Rose , nice to 
meet you!
'''
```

对于单个字符的编码，Python提供了`ord()`函数获取字符的整数表示，`chr()`函数把编码转换为对应的字符：

```
print(ord('A'))
print(ord('中'))
print(chr(66))
print(chr(25991))
#
65
20013
B
文
```

由于Python的字符串类型是`str`，在内存中以Unicode表示，一个字符对应若干个字节。如果要在网络上传输，或者保存到磁盘上，就需要把`str`变为以字节为单位的`bytes`。

Python对`bytes`类型的数据用带`b`前缀的单引号或双引号表示：

```
x = b'ABC'
```

要注意区分`'ABC'`和`b'ABC'`，前者是`str`，后者虽然内容显示得和前者一样，但`bytes`的每个字符都只占用一个字节。

以Unicode表示的`str`通过`encode()`方法可以编码为指定的`bytes`

```
>>> 'ABC'.encode('ascii')
b'ABC'
>>> '中文'.encode('utf-8')
b'\xe4\xb8\xad\xe6\x96\x87'
>>> '中文'.encode('ascii')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
UnicodeEncodeError: 'ascii' codec can't encode characters in position 0-1: ordinal not in range(128)
```

纯英文的`str`可以用`ASCII`编码为`bytes`，内容是一样的，含有中文的`str`可以用`UTF-8`编码为`bytes`。含有中文的`str`无法用`ASCII`编码，因为中文编码的范围超过了`ASCII`编码的范围，Python会报错。

***在`bytes`中，无法显示为ASCII字符的字节，用`\x##`显示。***

反过来，如果我们从网络或磁盘上读取了字节流，那么读到的数据就是`bytes`。要把`bytes`变为`str`，就需要用`decode()`方法：

```
>>> b'ABC'.decode('ascii')
'ABC'
>>> b'\xe4\xb8\xad\xe6\x96\x87'.decode('utf-8')
'中文'
```

如果`bytes`中包含无法解码的字节，`decode()`方法会报错：

```
>>> b'\xe4\xb8\xad\xff'.decode('utf-8')
Traceback (most recent call last):
  ...
UnicodeDecodeError: 'utf-8' codec can't decode byte 0xff in position 3: invalid start byte
```

*如果`bytes`中只有一小部分无效的字节，可以传入`errors='ignore'`忽略错误的字节*：

```
>>> b'\xe4\xb8\xad\xff'.decode('utf-8', errors='ignore')
'中'
```

计算`str`包含多少个字符，可以用`len()`函数。



在操作字符串时，我们经常遇到`str`和`bytes`的互相转换。为了避免乱码问题，应当始终坚持使用UTF-8编码对`str`和`bytes`进行转换。

由于Python源代码也是一个文本文件，所以，当你的源代码中包含中文的时候，在保存源代码时，就需要务必指定保存为UTF-8编码。当Python解释器读取源代码时，为了让它按UTF-8编码读取，我们通常在文件开头写上这两行：

```
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
```

第一行注释是为了告诉Linux/OS X系统，这是一个Python可执行程序，Windows系统会忽略这个注释；

第二行注释是为了告诉Python解释器，按照UTF-8编码读取源代码，否则，你在源代码中写的中文输出可能会有乱码。

申明了UTF-8编码并不意味着你的`.py`文件就是UTF-8编码的，必须并且要确保文本编辑器正在使用UTF-8 without BOM编码。

### 格式化

在Python中，采用的格式化方式和C语言是一致的，用`%`实现，举例如下：

```
>>> 'Hello, %s' % 'world'
'Hello, world'
>>> 'Hi, %s, you have $%d.' % ('Michael', 1000000)
'Hi, Michael, you have $1000000.'
```

你可能猜到了，***`%`运算符就是用来格式化字符串的。***在字符串内部，`%s`表示用字符串替换，`%d`表示用整数替换，有几个`%?`占位符，后面就跟几个变量或者值，顺序要对应好。如果只有一个`%?`，括号可以省略。

常见的占位符有：

| 占位符 | 替换内容     |
| :----- | :----------- |
| %d     | 整数         |
| %f     | 浮点数       |
| %s     | 字符串       |
| %x     | 十六进制整数 |

其中，格式化整数和浮点数还可以指定是否补0和整数与小数的位数：

```
print('%2d-%02d' % (3, 1))
print('%.2f' % 3.1415926)
 3-01
3.14
```

如果你不太确定应该用什么，`%s`永远起作用，它会把任何数据类型转换为字符串：

```
>>> 'Age: %s. Gender: %s' % (25, True)
'Age: 25. Gender: True'
```

有些时候，字符串里面的`%`是一个普通字符怎么办？这个时候就需要转义，用`%%`来表示一个`%`：

```
>>> 'growth rate: %d %%' % 7
'growth rate: 7 %'
```

##### format()

另一种格式化字符串的方法是使用字符串的`format()`方法，它会用传入的参数依次替换字符串内的占位符`{0}`、`{1}`……，不过这种方式写起来比%要麻烦得多：

```
>>> 'Hello, {0}, 成绩提升了 {1:.1f}%'.format('小明', 17.125)
'Hello, 小明, 成绩提升了 17.1%'
```

##### f-string

最后一种格式化字符串的方法是使用以`f`开头的字符串，称之为`f-string`，它和普通字符串不同之处在于，字符串如果包含`{xxx}`，就会以对应的变量替换：

```
>>> r = 2.5
>>> s = 3.14 * r ** 2
>>> print(f'The area of a circle with radius {r} is {s:.2f}')
The area of a circle with radius 2.5 is 19.62
```

上述代码中，`{r}`被变量`r`的值替换，`{s:.2f}`被变量`s`的值替换，并且`:`后面的`.2f`指定了格式化参数（即保留两位小数），因此，`{s:.2f}`的替换结果是`19.62`。

### 字符串输入输出

使用 input 输入：

```
name = input('请输⼊您的名字：')
print(f'您输⼊的名字是{name}')
```

使用 print 输出



### 下标

```
name = "abcdef"
print(name[1])
print(name[0])
print(name[2])
```

### 切片

切⽚是指对操作的对象截取其中⼀部分的操作。字符串、列表、元组都⽀持切⽚操作。

```
序列[开始位置下标:结束位置下标:步⻓]
```

注意
1. 不包含结束位置下标对应的数据， 正负整数均可；
2. 步⻓是选取间隔，正负整数均可，默认步⻓为1。

```
name = "abcdefg"
print(name[2:5:1]) # cde
```



### 在字符串中使用变量

```python
first_name = "ada"
last_name = "lovelace"
full_name = f"{first_name} {last_name}"
print(full_name)
```

*要在字符串中插入变量的值，可在**前引号前加上字母f** ，再将要插入的变量放在花括号内。*这样，当Python显示字符串时，将把每个变量都替换为其值。这种字符串名为**f字符串** 。f是format（设置格式）的简写，因为Python通过把花括号内的变量替换为其值来设置字符串的格式。

### 常用操作方法

#### 查找

所谓字符串查找⽅法即是查找⼦串在字符串中的位置或出现的次数。

`find()`：检测某个⼦串是否包含在这个字符串中，如果在返回这个⼦串开始的位置下标，否则则返回-1。开始和结束位置下标可以省略，表示在整个字符串序列中查找

```
字符串序列.find(⼦串, 开始位置下标, 结束位置下标) 
```

`index()`：检测某个⼦串是否包含在这个字符串中，如果在返回这个⼦串开始的位置下标，否则则报异常。开始和结束位置下标可以省略，表示在整个字符串序列中查找。

```
字符串序列.index(⼦串, 开始位置下标, 结束位置下标)
```

`rfind()`： 和fifind()功能相同，但查找⽅向为右侧开始。

`rindex()`：和index()功能相同，但查找⽅向为右侧开始。

`count()`：返回某个⼦串在字符串中出现的次数

```
字符串序列.count(⼦串, 开始位置下标, 结束位置下标)
```

#### 修改

`replace()`：替换，但不改变原字符串。

```
字符串序列.replace(旧⼦串, 新⼦串, 替换次数)
```

数据按照是否能直接修改分为可变类型和不可变类型两种。字符串类型的数据修改的时候不能改变原有字符串，属于不能直接修改数据的类型即是不可变类型。

`split()`：按照指定字符分割字符串。num表示的是分割字符出现的次数，*即将来返回数据个数为num+1个。*

```
字符串序列.split(分割字符, num)
```

`join()`：⽤⼀个字符或⼦串合并字符串，即是将多个字符串合并为⼀个新的字符串

```
字符或⼦串.join(多字符串组成的序列) 
```

`capitalize()`：将字符串第⼀个字符转换成⼤写。*转换后，只字符串第⼀个字符⼤写，其他的字符全都⼩写。*

`title()`：将字符串每个单词⾸字⺟转换成⼤写。

`lower()`：将字符串中⼤写转⼩写。

`upper()`：将字符串中⼩写转⼤写。

`lstrip()`：删除字符串左侧空⽩字符

`rstrip()`：删除字符串右侧空⽩字符

`strip()`：删除字符串两侧空⽩字符。

`ljust()`：返回⼀个原字符串左对⻬,并使⽤指定字符(默认空格)填充⾄对应⻓度的新字符串。

```
字符串序列.ljust(⻓度, 填充字符)
```

`rjust()`：返回⼀个原字符串右对⻬,并使⽤指定字符(默认空格)填充⾄对应⻓度 的新字符串，语法和ljust()相同。

`center()`：返回⼀个原字符串居中对⻬,并使⽤指定字符(默认空格)填充⾄对应⻓度 的新字符串，语法和ljust()相同。

#### 判断

`startswith()`：检查字符串是否是以指定⼦串开头，是则返回 True，否则返回 False。如果设置开始和结束位置下标，则在指定范围内检查。

```
字符串序列.startswith(⼦串, 开始位置下标, 结束位置下标)
```

`endswith()`：：检查字符串是否是以指定⼦串结尾，是则返回 True，否则返回 False。如果设置开始和结束位置下标，则在指定范围内检查。

```
字符串序列.endswith(⼦串, 开始位置下标, 结束位置下标)
```

`isalpha()`：如果字符串⾄少有⼀个字符并且所有字符都是字⺟则返回 True, 否则返回 False。

`isdigit()`：如果字符串只包含数字则返回 True 否则返回 False

`isalnum()`：如果字符串⾄少有⼀个字符并且所有字符都是字⺟或数字则返 回 True,否则返回False

`isspace()`：如果字符串中只包含空⽩，则返回 True，否则返回 False。 



## 注释

在Python中，注释用井号（# ）标识。井号后面的内容都会被Python解释器忽略。

## 类型转换

### 转换数据类型的函数

| 函数                   | 说明                                                |
| ---------------------- | --------------------------------------------------- |
| *int(x [,base ])*      | 将x转换为⼀个整数                                   |
| *float*(x )            | 将x转换为⼀个浮点数                                 |
| complex(real [,imag ]) | 创建⼀个复数，real为实部，imag为虚部                |
| *str*(x )              | 将对象 x 转换为字符串                               |
| repr(x )               | 将对象 x 转换为表达式字符串                         |
| *eval(str )*           | ⽤来计算在字符串中的有效Python表达式,并返回⼀个对象 |
| *tuple(s)*             | 将序列 s 转换为⼀个元组                             |
| *list(s )*             | 将序列 s 转换为⼀个列表                             |
| chr(x )                | 将⼀个整数转换为⼀个Unicode字符                     |
| ord(x )                | 将⼀个字符转换为它的ASCII整数值                     |
| hex(x )                | 将⼀个整数转换为⼀个⼗六进制字符串                  |
| oct(x )                | 将⼀个整数转换为⼀个⼋进制字符串                    |
| bin(x )                | 将⼀个整数转换为⼀个⼆进制字符串                    |

```
# float() transfer to float
num1 = 1
print(float(num1))
print(type(float(num1)))

# str() transfer to string
num2 = 10
print(type(str(num2)))
```

# python 基础

## 使用list和tuple

### list

Python内置的一种数据类型是列表：list。list是一种有序的集合，可以随时添加和删除其中的元素。

比如，列出班里所有同学的名字，就可以用一个list表示：

```
>>> classmates = ['Michael', 'Bob', 'Tracy']
>>> classmates
['Michael', 'Bob', 'Tracy']
```

变量`classmates`就是一个list。用`len()`函数可以获得list元素的个数。

用索引来访问list中每一个位置的元素，记得索引是从`0`开始的。

***如果要取最后一个元素，除了计算索引位置外，还可以用`-1`做索引，直接获取最后一个元素***：

```
>>> classmates[-1]
'Tracy'
```

以此类推，可以获取倒数第2个、倒数第3个。

ist是一个可变的有序表，所以，可以往list中追加元素到末尾：

```
>>> classmates.append('Adam')
>>> classmates
['Michael', 'Bob', 'Tracy', 'Adam']
```

也可以把元素插入到指定的位置，比如索引号为`1`的位置：

```
>>> classmates.insert(1, 'Jack')
>>> classmates
['Michael', 'Jack', 'Bob', 'Tracy', 'Adam']
```

要删除list末尾的元素，用`pop()`方法：

```
>>> classmates.pop()
'Adam'
>>> classmates
['Michael', 'Jack', 'Bob', 'Tracy']
```

要删除指定位置的元素，用`pop(i)`方法，其中`i`是索引位置：

```
>>> classmates.pop(1)
'Jack'
>>> classmates
['Michael', 'Bob', 'Tracy']
```

要把某个元素替换成别的元素，可以直接赋值给对应的索引位置：

```
>>> classmates[1] = 'Sarah'
>>> classmates
['Michael', 'Sarah', 'Tracy']
```

***list里面的元素的数据类型也可以不同***，比如：

```
>>> L = ['Apple', 123, True]
```

*list元素也可以是另一个list*，比如：

```
>>> s = ['python', 'java', ['asp', 'php'], 'scheme']
>>> len(s)
4
```

要注意`s`只有4个元素，其中`s[2]`又是一个list，如果拆开写就更容易理解了：

```
>>> p = ['asp', 'php']
>>> s = ['python', 'java', p, 'scheme']
```

要拿到`'php'`可以写`p[1]`或者`s[2][1]`，因此`s`可以看成是一个二维数组，类似的还有三维、四维……数组，不过很少用到。

如果一个list中一个元素也没有，就是一个空的list，它的长度为0：

```
>>> L = []
>>> len(L)
0
```

### tuple

另一种有序列表叫元组：tuple。tuple和list非常类似，但是tuple一旦初始化就不能修改，比如同样是列出同学的名字：

```
>>> classmates = ('Michael', 'Bob', 'Tracy')
```

现在，classmates这个tuple不能变了，它也没有append()，insert()这样的方法。其他获取元素的方法和list是一样的，你可以正常地使用`classmates[0]`，`classmates[-1]`，但不能赋值成另外的元素。

不可变的tuple有什么意义？因为tuple不可变，所以代码更安全。如果可能，能用tuple代替list就尽量用tuple。

tuple的陷阱：当你定义一个tuple时，在定义的时候，tuple的元素就必须被确定下来，比如：

```
>>> t = (1, 2)
>>> t
(1, 2)
```

如果要定义一个空的tuple，可以写成`()`：

```
>>> t = ()
>>> t
()
```

但是，要定义一个只有1个元素的tuple，如果你这么定义：

```
>>> t = (1)
>>> t
1
```

*定义的不是tuple，是`1`这个数！这是因为括号`()`既可以表示tuple，又可以表示数学公式中的小括号，这就产生了歧义，因此，Python规定，这种情况下，按小括号进行计算，计算结果自然是`1`。*

所以，***只有1个元素的tuple定义时必须加一个逗号`,`，来消除歧义***：

```
>>> t = (1,)
>>> t
(1,)
```

*Python在显示只有1个元素的tuple时，也会加一个逗号`,`，以免你误解成数学计算意义上的括号*。

最后来看一个“可变的”tuple：

```
>>> t = ('a', 'b', ['A', 'B'])
>>> t[2][0] = 'X'
>>> t[2][1] = 'Y'
>>> t
('a', 'b', ['X', 'Y'])
```

这个tuple定义的时候有3个元素，分别是`'a'`，`'b'`和一个list。不是说tuple一旦定义后就不可变了吗？怎么后来又变了？

别急，我们先看看定义的时候tuple包含的3个元素：

![tuple-0](https://www.liaoxuefeng.com/files/attachments/923973516787680/0)

当我们把list的元素`'A'`和`'B'`修改为`'X'`和`'Y'`后，tuple变为：

![tuple-1](https://img.herrluk.icu/typoraPicture/2022-12-01-16:10:32.png)

表面上看，tuple的元素确实变了，但其实变的不是tuple的元素，而是list的元素。*tuple一开始指向的list并没有改成别的list，所以，tuple所谓的“不变”是说，tuple的每个元素，指向永远不变。即指向`'a'`，就不能改成指向`'b'`，指向一个list，就不能改成指向其他对象，但指向的这个list本身是可变的*！

理解了“指向不变”后，要创建一个内容也不变的tuple怎么做？那就必须保证tuple的每一个元素本身也不能变。

## 条件判断

计算机之所以能做很多自动化的任务，因为它可以自己做条件判断。

比如，输入用户年龄，根据年龄打印不同的内容，在Python程序中，用`if`语句实现：

```
age = 20
if age >= 18:
    print('your age is', age)
    print('adult')
```

根据Python的缩进规则，如果`if`语句判断是`True`，就把缩进的两行print语句执行了，否则，什么也不做。

也可以给`if`添加一个`else`语句，意思是，如果`if`判断是`False`，不要执行`if`的内容，去把`else`执行了：

```
age = 3
if age >= 18:
    print('your age is', age)
    print('adult')
else:
    print('your age is', age)
    print('teenager')
```

注意不要少写了冒号`:`。

当然上面的判断是很粗略的，完全可以用`elif`做更细致的判断：

```
age = 3
if age >= 18:
    print('adult')
elif age >= 6:
    print('teenager')
else:
    print('kid')
```

`elif`是`else if`的缩写，完全可以有多个`elif`，所以`if`语句的完整形式就是：

```
if <条件判断1>:
    <执行1>
elif <条件判断2>:
    <执行2>
elif <条件判断3>:
    <执行3>
else:
    <执行4>
```

`if`语句执行有个特点，*它是从上往下判断，如果在某个判断上是`True`，把该判断对应的语句执行后，就忽略掉剩下的`elif`和`else`*，所以，请测试并解释为什么下面的程序打印的是`teenager`：

```
age = 20
if age >= 6:
    print('teenager')
elif age >= 18:
    print('adult')
else:
    print('kid')
```

`if`判断条件还可以简写，比如写：

```
if x:
    print('True')
```

只要`x`是非零数值、非空字符串、非空list等，就判断为`True`，否则为`False`。

### 再议 input

最后看一个有问题的条件判断。很多同学会用`input()`读取用户的输入，这样可以自己输入，程序运行得更有意思：

```
birth = input('birth: ')
if birth < 2000:
    print('00前')
else:
    print('00后')
```

输入`1982`，结果报错：

```
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unorderable types: str() > int()
```

*这是因为`input()`返回的数据类型是`str`，`str`不能直接和整数比较，必须先把`str`转换成整数。Python提供了`int()`函数来完成这件事情*：

```
s = input('birth: ')
birth = int(s)
if birth < 2000:
    print('00前')
else:
    print('00后')
```

再次运行，就可以得到正确地结果。但是，如果输入`abc`呢？又会得到一个错误信息：

```
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: invalid literal for int() with base 10: 'abc'
```

原来`int()`函数发现一个字符串并不是合法的数字时就会报错，程序就退出了。

如何检查并捕获程序运行期的错误呢？后面的错误和调试会讲到。

## 循环

### for…in

Python的循环有两种，一种是`for...in`循环，依次把list或tuple中的每个元素迭代出来，看例子：

```
names = ['Michael', 'Bob', 'Tracy']
for name in names:
    print(name)
```

执行这段代码，会依次打印`names`的每一个元素：

```
Michael
Bob
Tracy
```

所以`for x in ...`循环就是把每个元素代入变量`x`，然后执行缩进块的语句。

再比如我们想计算1-10的整数之和，可以用一个`sum`变量做累加：

```
sum = 0
for x in [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]:
    sum = sum + x
print(sum)
```

如果要计算1-100的整数之和，从1写到100有点困难，幸好Python提供一个*`range()`函数，可以生成一个整数序列，再通过`list()`函数可以转换为list。*比如*`range(5)`生成的序列是从0开始小于5的整数*：

```
>>> list(range(5))
[0, 1, 2, 3, 4]
```

### while

第二种循环是while循环，只要条件满足，就不断循环，条件不满足时退出循环。比如我们要计算100以内所有奇数之和，可以用while循环实现：

```
sum = 0
n = 99
while n > 0:
    sum = sum + n
    n = n - 2
print(sum)
```

在循环内部变量`n`不断自减，直到变为`-1`时，不再满足while条件，循环退出。

### break

在循环中，`break`语句可以提前退出循环。例如，本来要循环打印1～100的数字：

```
n = 1
while n <= 100:
    print(n)
    n = n + 1
print('END')
```

上面的代码可以打印出1~100。

如果要提前结束循环，可以用`break`语句：

```
n = 1
while n <= 100:
    if n > 10: # 当n = 11时，条件满足，执行break语句
        break # break语句会结束当前循环
    print(n)
    n = n + 1
print('END')
```

执行上面的代码可以看到，打印出1~10后，紧接着打印`END`，程序结束。

可见`break`的作用是提前结束循环。

### continue

在循环过程中，也可以通过`continue`语句，跳过当前的这次循环，直接开始下一次循环。

```
n = 0
while n < 10:
    n = n + 1
    print(n)
```

上面的程序可以打印出1～10。但是，如果我们想只打印奇数，可以用`continue`语句跳过某些循环：

```
n = 0
while n < 10:
    n = n + 1
    if n % 2 == 0: # 如果n是偶数，执行continue语句
        continue # continue语句会直接继续下一轮循环，后续的print()语句不会执行
    print(n)
```

执行上面的代码可以看到，打印的不再是1～10，而是1，3，5，7，9。

可见`continue`的作用是提前结束本轮循环，并直接开始下一轮循环。

## 使用 dict 和 set

### dict

Python内置了字典：dict的支持，dict全称dictionary，在***其他语言中也称为map***，使用键-值（key-value）存储，具有极快的查找速度。

举个例子，假设要根据同学的名字查找对应的成绩，如果用list实现，需要两个list：

```
names = ['Michael', 'Bob', 'Tracy']
scores = [95, 75, 85]
```

给定一个名字，要查找对应的成绩，就先要在names中找到对应的位置，再从scores取出对应的成绩，list越长，耗时越长。

如果用dict实现，只需要一个“名字”-“成绩”的对照表，直接根据名字查找成绩，无论这个表有多大，查找速度都不会变慢。用Python写一个dict如下：

```
>>> d = {'Michael': 95, 'Bob': 75, 'Tracy': 85}
>>> d['Michael']
95
```

为什么dict查找速度这么快？因为dict的实现原理和查字典是一样的。假设字典包含了1万个汉字，我们要查某一个字，一个办法是把字典从第一页往后翻，直到找到我们想要的字为止，这种方法就是在list中查找元素的方法，list越大，查找越慢。

第二种方法是先在字典的索引表里（比如部首表）查这个字对应的页码，然后直接翻到该页，找到这个字。无论找哪个字，这种查找速度都非常快，不会随着字典大小的增加而变慢。

dict就是第二种实现方式，给定一个名字，比如`'Michael'`，dict在内部就可以直接计算出`Michael`对应的存放成绩的“页码”，也就是`95`这个数字存放的内存地址，直接取出来，所以速度非常快。

你可以猜到，这种key-value存储方式，在放进去的时候，必须根据key算出value的存放位置，这样，取的时候才能根据key直接拿到value。

把数据放入dict的方法，除了初始化时指定外，还可以通过key放入：

```
>>> d['Adam'] = 67
>>> d['Adam']
67
```

由于一个key只能对应一个value，所以，多次对一个key放入value，后面的值会把前面的值冲掉：

```
>>> d['Jack'] = 90
>>> d['Jack']
90
>>> d['Jack'] = 88
>>> d['Jack']
88
```

如果key不存在，dict就会报错：

```
>>> d['Thomas']
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'Thomas'
```

要避免key不存在的错误，有两种办法，*一是通过`in`判断key是否存在*：

```
>>> 'Thomas' in d
False
```

二是通过*dict提供的`get()`方法，如果key不存在，可以返回`None`，或者自己指定的value*：

```
>>> d.get('Thomas')
>>> d.get('Thomas', -1)
-1
```

注意：返回`None`的时候Python的交互环境不显示结果。

要*删除一个key，用`pop(key)`方法，对应的value也会从dict中删除*：

```
>>> d.pop('Bob')
75
>>> d
{'Michael': 95, 'Tracy': 85}
```

请务必注意，dict内部存放的顺序和key放入的顺序是没有关系的。

和list比较，dict有以下几个特点：

1. 查找和插入的速度极快，不会随着key的增加而变慢；
2. 需要占用大量的内存，内存浪费多。

而list相反：

1. 查找和插入的时间随着元素的增加而增加；
2. 占用空间小，浪费内存很少。

所以，dict是用空间来换取时间的一种方法。

dict可以用在需要高速查找的很多地方，在Python代码中几乎无处不在，正确使用dict非常重要，需要牢记的第一条就是dict的key必须是**不可变对象**。

这是因为dict根据key来计算value的存储位置，如果每次计算相同的key得出的结果不同，那dict内部就完全混乱了。这个通过key计算位置的算法称为哈希算法（Hash）。

要保证hash的正确性，作为key的对象就不能变。在Python中，字符串、整数等都是不可变的，因此，可以放心地作为key。而list是可变的，就不能作为key：

```
>>> key = [1, 2, 3]
>>> d[key] = 'a list'
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unhashable type: 'list'
```

### set

set和dict类似，也是一组key的集合，但不存储value。由于key不能重复，所以，在set中，没有重复的key。

要创建一个set，需要提供一个list作为输入集合：

```
>>> s = set([1, 2, 3])
>>> s
{1, 2, 3}
```

注意，传入的参数`[1, 2, 3]`是一个list，而显示的`{1, 2, 3}`只是告诉你这个set内部有1，2，3这3个元素，***显示的顺序也不表示set是有序的***！

***重复元素在set中自动被过滤：***

```
>>> s = set([1, 1, 2, 2, 3, 3])
>>> s
{1, 2, 3}
```

*通过`add(key)`方法可以添加元素到set中*，可以重复添加，但不会有效果：

```
>>> s.add(4)
>>> s
{1, 2, 3, 4}
>>> s.add(4)
>>> s
{1, 2, 3, 4}
```

*通过`remove(key)`方法可以删除元素：*

```
>>> s.remove(4)
>>> s
{1, 2, 3}
```

*set可以看成数学意义上的无序和无重复元素的集合，因此，两个set可以做数学意义上的交集、并集等操作：*

```
>>> s1 = set([1, 2, 3])
>>> s2 = set([2, 3, 4])
>>> s1 & s2
{2, 3}
>>> s1 | s2
{1, 2, 3, 4}
```

set和dict的唯一区别仅在于没有存储对应的value，但是，set的原理和dict一样，所以，同样不可以放入可变对象，因为无法判断两个可变对象是否相等，也就无法保证set内部“不会有重复元素”。试试把list放入set，看看是否会报错。

### 再议不可变对象

上面我们讲了，str是不变对象，而list是可变对象。

对于可变对象，比如list，对list进行操作，list内部的内容是会变化的，比如：

```
>>> a = ['c', 'b', 'a']
>>> a.sort()
>>> a
['a', 'b', 'c']
```

而对于不可变对象，比如str，对str进行操作呢：

```
>>> a = 'abc'
>>> a.replace('a', 'A')
'Abc'
>>> a
'abc'
```

虽然字符串有个`replace()`方法，也确实变出了`'Abc'`，但变量`a`最后仍是`'abc'`，应该怎么理解呢？

我们先把代码改成下面这样：

```
>>> a = 'abc'
>>> b = a.replace('a', 'A')
>>> b
'Abc'
>>> a
'abc'
```

要始终牢记的是，`a`是变量，而`'abc'`才是字符串对象！有些时候，*我们经常说，对象`a`的内容是`'abc'`，但其实是指，`a`本身是一个变量，它指向的对象的内容才是`'abc'`：*

```ascii
┌───┐                  ┌───────┐
│ a │─────────────────>│ 'abc' │
└───┘                  └───────┘
```

当我们调用`a.replace('a', 'A')`时，实际上调用方法`replace`是作用在字符串对象`'abc'`上的，而这个方法虽然名字叫`replace`，但却没有改变字符串`'abc'`的内容。相反，`replace`方法创建了一个新字符串`'Abc'`并返回，如果我们用变量`b`指向该新字符串，就容易理解了，变量`a`仍指向原有的字符串`'abc'`，但变量`b`却指向新字符串`'Abc'`了：

```ascii
┌───┐                  ┌───────┐
│ a │─────────────────>│ 'abc' │
└───┘                  └───────┘
┌───┐                  ┌───────┐
│ b │─────────────────>│ 'Abc' │
└───┘                  └───────┘
```

所以，对于不变对象来说，调用对象自身的任意方法，也不会改变该对象自身的内容。相反，这些方法会创建新的对象并返回，这样，就保证了不可变对象本身永远是不可变的。

# 函数

## 函数简介

## 调用函数

要调用一个函数，需要知道函数的名称和参数，比如求绝对值的函数`abs`，只有一个参数。可以直接从Python的官方网站查看文档：

http://docs.python.org/3/library/functions.html#abs

调用函数的时候，如果传入的参数数量不对，会报`TypeError`的错误，并且Python会明确地告诉你：`abs()`有且仅有1个参数，但给出了两个：

```
>>> abs(1, 2)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: abs() takes exactly one argument (2 given)
```

如果传入的参数数量是对的，但参数类型不能被函数所接受，也会报`TypeError`的错误，并且给出错误信息：`str`是错误的参数类型：

```
>>> abs('a')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: bad operand type for abs(): 'str'
```

而`max`函数`max()`可以接收任意多个参数，并返回最大的那个：

```
>>> max(1, 2)
2
>>> max(2, 3, 1, -5)
3
```

### 数据类型转换

Python内置的常用函数还包括数据类型转换函数，比如`int()`函数可以把其他数据类型转换为整数：

```
>>> int('123')
123
>>> int(12.34)
12
>>> float('12.34')
12.34
>>> str(1.23)
'1.23'
>>> str(100)
'100'
>>> bool(1)
True
>>> bool('')
False
```

***函数名其实就是指向一个函数对象的引用，完全可以把函数名赋给一个变量，相当于给这个函数起了一个“别名”：***

```
>>> a = abs # 变量a指向abs函数
>>> a(-1) # 所以也可以通过a调用abs函数
1
```

## 定义函数

在Python中，定义一个函数要使用`def`语句，依次写出函数名、括号、括号中的参数和冒号`:`，然后，在缩进块中编写函数体，函数的返回值用`return`语句返回。

我们以自定义一个求绝对值的`my_abs`函数为例：

```
def my_abs(x):
    if x >= 0:
        return x
    else:
        return -x
   
```

请注意，函数体内部的语句在执行时，一旦执行到`return`时，函数就执行完毕，并将结果返回。因此，函数内部通过条件判断和循环可以实现非常复杂的逻辑。

如果没有`return`语句，函数执行完毕后也会返回结果，只是结果为`None`。`return None`可以简写为`return`。

如果你已经把`my_abs()`的函数定义保存为`abstest.py`文件了，那么，可以在该文件的当前目录下启动Python解释器，***用`from abstest import my_abs`来导入`my_abs()`函数，注意`abstest`是文件名（不含`.py`扩展名）：***

### 空函数

如果想定义一个什么事也不做的空函数，可以用`pass`语句：

```
def nop():
    pass
```

`pass`语句什么都不做，那有什么用？***实际上`pass`可以用来作为占位符，比如现在还没想好怎么写函数的代码，就可以先放一个`pass`，让代码能运行起来。***

`pass`还可以用在其他语句里，比如：

```
if age >= 18:
    pass
```

缺少了`pass`，代码运行就会有语法错误。

### 参数检查

调用函数时，如果参数个数不对，Python解释器会自动检查出来，并抛出`TypeError`：

```
>>> my_abs(1, 2)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: my_abs() takes 1 positional argument but 2 were given
```

但是如果参数类型不对，Python解释器就无法帮我们检查。试试`my_abs`和内置函数`abs`的差别：

```
>>> my_abs('A')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<stdin>", line 2, in my_abs
TypeError: unorderable types: str() >= int()
>>> abs('A')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: bad operand type for abs(): 'str'
```

当传入了不恰当的参数时，内置函数`abs`会检查出参数错误，而我们定义的`my_abs`没有参数检查，会导致`if`语句出错，出错信息和`abs`不一样。所以，这个函数定义不够完善。

让我们修改一下`my_abs`的定义，对参数类型做检查，只允许整数和浮点数类型的参数。数据类型检查可以用内置函数`isinstance()`实现：

```
def my_abs(x):
    if not isinstance(x, (int, float)):
        raise TypeError('bad operand type')
    if x >= 0:
        return x
    else:
        return -x
```

添加了参数检查后，如果传入错误的参数类型，函数就可以抛出一个错误：

```
>>> my_abs('A')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<stdin>", line 3, in my_abs
TypeError: bad operand type
```

错误和异常处理将在后续讲到。

### 返回多个值

***函数可以返回多个值吗？答案是肯定的。***

比如在游戏中经常需要从一个点移动到另一个点，给出坐标、位移和角度，就可以计算出新的坐标：

```
import math

def move(x, y, step, angle=0):
    nx = x + step * math.cos(angle)
    ny = y - step * math.sin(angle)
    return nx, ny
```

`import math`语句表示导入`math`包，并允许后续代码引用`math`包里的`sin`、`cos`等函数。

然后，我们就可以同时获得返回值：

```
>>> x, y = move(100, 100, 60, math.pi / 6)
>>> print(x, y)
151.96152422706632 70.0
```

但其实这只是一种假象，Python函数返回的仍然是单一值：

```
>>> r = move(100, 100, 60, math.pi / 6)
>>> print(r)
(151.96152422706632, 70.0)
```

***原来返回值是一个tuple！***

但是，在语法上，返回一个tuple可以省略括号，而多个变量可以同时接收一个tuple，按位置赋给对应的值，所以，***Python的函数返回多值其实就是返回一个tuple，但写起来更方便。***

## 函数的参数

定义函数的时候，我们把参数的名字和位置确定下来，函数的接口定义就完成了。对于函数的调用者来说，只需要知道如何传递正确的参数，以及函数将返回什么样的值就够了，函数内部的复杂逻辑被封装起来，调用者无需了解。

Python的函数定义非常简单，但灵活度却非常大。除了正常定义的必选参数外，还可以使用默认参数、可变参数和关键字参数，使得函数定义出来的接口，不但能处理复杂的参数，还可以简化调用者的代码。

### 位置参数

我们先写一个计算x2的函数：

```
def power(x):
    return x * x
```

对于`power(x)`函数，参数`x`就是一个位置参数。

当我们调用`power`函数时，必须传入有且仅有的一个参数`x`：

```
>>> power(5)
25
>>> power(15)
225
```

现在，如果我们要计算x3怎么办？可以再定义一个`power3`函数，但是如果要计算x4、x5……怎么办？我们不可能定义无限多个函数。

你也许想到了，可以把`power(x)`修改为`power(x, n)`，用来计算xn，说干就干：

```
def power(x, n):
    s = 1
    while n > 0:
        n = n - 1
        s = s * x
    return s
```

对于这个修改后的`power(x, n)`函数，可以计算任意n次方：

```
>>> power(5, 2)
25
>>> power(5, 3)
125
```

修改后的`power(x, n)`函数有两个参数：`x`和`n`，这两个参数都是位置参数，调用函数时，传入的两个值按照位置顺序依次赋给参数`x`和`n`。

### 默认参数

新的`power(x, n)`函数定义没有问题，但是，旧的调用代码失败了，原因是我们增加了一个参数，导致旧的代码因为缺少一个参数而无法正常调用：

```
>>> power(5)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: power() missing 1 required positional argument: 'n'
```

Python的错误信息很明确：调用函数`power()`缺少了一个位置参数`n`。

这个时候，默认参数就排上用场了。由于我们经常计算x2，所以，完全可以把第二个参数n的默认值设定为2：

```
def power(x, n=2):
    s = 1
    while n > 0:
        n = n - 1
        s = s * x
    return s
```

这样，当我们调用`power(5)`时，相当于调用`power(5, 2)`：

```
>>> power(5)
25
>>> power(5, 2)
25
```

而对于`n > 2`的其他情况，就必须明确地传入n，比如`power(5, 3)`。

从上面的例子可以看出，***默认参数可以简化函数的调用***。设置默认参数时，有几点要注意：

一是***必选参数在前，默认参数在后***，否则Python的解释器会报错（思考一下为什么默认参数不能放在必选参数前面）；

二是如何设置默认参数。

***当函数有多个参数时，把变化大的参数放前面，变化小的参数放后面。变化小的参数就可以作为默认参数。***

使用默认参数有什么好处？最大的好处是能降低调用函数的难度。

举个例子，我们写个一年级小学生注册的函数，需要传入`name`和`gender`两个参数：

```
def enroll(name, gender):
    print('name:', name)
    print('gender:', gender)
```

这样，调用`enroll()`函数只需要传入两个参数：

```
>>> enroll('Sarah', 'F')
name: Sarah
gender: F
```

如果要继续传入年龄、城市等信息怎么办？这样会使得调用函数的复杂度大大增加。

我们可以把年龄和城市设为默认参数：

```
def enroll(name, gender, age=6, city='Beijing'):
    print('name:', name)
    print('gender:', gender)
    print('age:', age)
    print('city:', city)
```

这样，大多数学生注册时不需要提供年龄和城市，只提供必须的两个参数：

```
>>> enroll('Sarah', 'F')
name: Sarah
gender: F
age: 6
city: Beijing
```

只有与默认参数不符的学生才需要提供额外的信息：

```
enroll('Bob', 'M', 7)
enroll('Adam', 'M', city='Tianjin')
```

可见，默认参数降低了函数调用的难度，而一旦需要更复杂的调用时，又可以传递更多的参数来实现。无论是简单调用还是复杂调用，函数只需要定义一个。

***有多个默认参数时，调用的时候，既可以按顺序提供默认参数***，比如调用`enroll('Bob', 'M', 7)`，意思是，除了`name`，`gender`这两个参数外，最后1个参数应用在参数`age`上，`city`参数由于没有提供，仍然使用默认值。

***也可以不按顺序提供部分默认参数。当不按顺序提供部分默认参数时，需要把参数名写上。***比如调用`enroll('Adam', 'M', city='Tianjin')`，意思是，`city`参数用传进去的值，其他默认参数继续使用默认值。

默认参数很有用，但使用不当，也会掉坑里。默认参数有个最大的坑，演示如下：

先定义一个函数，传入一个list，添加一个`END`再返回：

```
def add_end(L=[]):
    L.append('END')
    return L
```

当你正常调用时，结果似乎不错：

```
>>> add_end([1, 2, 3])
[1, 2, 3, 'END']
>>> add_end(['x', 'y', 'z'])
['x', 'y', 'z', 'END']
```

当你使用默认参数调用时，一开始结果也是对的：

```
>>> add_end()
['END']
```

但是，再次调用`add_end()`时，结果就不对了：

```
>>> add_end()
['END', 'END']
>>> add_end()
['END', 'END', 'END']
```

很多初学者很疑惑，默认参数是`[]`，但是函数似乎每次都“记住了”上次添加了`'END'`后的list。

原因解释如下：

> <u>Python函数在定义的时候，默认参数`L`的值就被计算出来了，即`[]`，因为默认参数`L`也是一个变量，它指向对象`[]`，每次调用该函数，如果改变了`L`的内容，则下次调用时，默认参数的内容就变了，不再是函数定义时的`[]`了。</u>

 ***定义默认参数要牢记一点：默认参数必须指向不变对象！***

要修改上面的例子，我们可以用`None`这个不变对象来实现：

```
def add_end(L=None):
    if L is None:
        L = []
    L.append('END')
    return L
```

现在，无论调用多少次，都不会有问题：

```
>>> add_end()
['END']
>>> add_end()
['END']
```

为什么要设计`str`、`None`这样的不变对象呢？

> <u>**因为不变对象一旦创建，对象内部的数据就不能修改，这样就减少了由于修改数据导致的错误。此外，由于对象不变，多任务环境下同时读取对象不需要加锁，同时读一点问题都没有。我们在编写程序时，如果可以设计一个不变对象，那就尽量设计成不变对象。**</u>

### 可变参数

在Python函数中，还可以定义可变参数。顾名思义，可变参数就是传入的参数个数是可变的，可以是1个、2个到任意个，还可以是0个。

我们以数学题为例子，给定一组数字a，b，c……，请计算a2 + b2 + c2 + ……。

要定义出这个函数，我们必须确定输入的参数。由于参数个数不确定，我们首先想到可以把a，b，c……作为一个list或tuple传进来，这样，函数可以定义如下：

```
def calc(numbers):
    sum = 0
    for n in numbers:
        sum = sum + n * n
    return sum
```

但是调用的时候，需要先组装出一个list或tuple：

```
>>> calc([1, 2, 3])
14
>>> calc((1, 3, 5, 7))
84
```

如果利用可变参数，调用函数的方式可以简化成这样：

```
>>> calc(1, 2, 3)
14
>>> calc(1, 3, 5, 7)
84
```

所以，我们把函数的参数改为可变参数：

```
def calc(*numbers):
    sum = 0
    for n in numbers:
        sum = sum + n * n
    return sum
```

> 定义可变参数和定义一个list或tuple参数相比，*仅仅在参数前面加了一个`*`号。在函数内部，参数`numbers`接收到的是一个tuple，因此，函数代码完全不变。*

但是，调用该函数时，可以传入任意个参数，包括0个参数：

```
>>> calc(1, 2)
5
>>> calc()
0
```

如果已经有一个list或者tuple，要调用一个可变参数怎么办？可以这样做：

```
>>> nums = [1, 2, 3]
>>> calc(nums[0], nums[1], nums[2])
14
```

这种写法当然是可行的，问题是太繁琐，***所以Python允许你在list或tuple前面加一个`*`号，把list或tuple的元素变成可变参数传进去：***

```
>>> nums = [1, 2, 3]
>>> calc(*nums)
14
```

> `*nums`表示把`nums`这个list的所有元素作为可变参数传进去。这种写法相当有用，而且很常见。

### 关键字参数

可变参数允许你传入0个或任意个参数，这些可变参数在函数调用时自动组装为一个tuple。

***而关键字参数允许你传入0个或任意个含参数名的参数，这些关键字参数在函数内部自动组装为一个dict。***

请看示例：

```
def person(name, age, **kw):
    print('name:', name, 'age:', age, 'other:', kw)
```

函数`person`除了必选参数`name`和`age`外，还接受关键字参数`kw`。在调用该函数时，可以只传入必选参数：

```
>>> person('Michael', 30)
name: Michael age: 30 other: {}
```

也可以传入任意个数的关键字参数：

```
>>> person('Bob', 35, city='Beijing')
name: Bob age: 35 other: {'city': 'Beijing'}
>>> person('Adam', 45, gender='M', job='Engineer')
name: Adam age: 45 other: {'gender': 'M', 'job': 'Engineer'}
```

关键字参数有什么用？它可以扩展函数的功能。比如，在`person`函数里，我们保证能接收到`name`和`age`这两个参数，但是，如果调用者愿意提供更多的参数，我们也能收到。试想你正在做一个用户注册的功能，除了用户名和年龄是必填项外，其他都是可选项，利用关键字参数来定义这个函数就能满足注册的需求。

和可变参数类似，也可以先组装出一个dict，然后，把该dict转换为关键字参数传进去：

```
>>> extra = {'city': 'Beijing', 'job': 'Engineer'}
>>> person('Jack', 24, city=extra['city'], job=extra['job'])
name: Jack age: 24 other: {'city': 'Beijing', 'job': 'Engineer'}
```

当然，上面复杂的调用可以用简化的写法：

```
>>> extra = {'city': 'Beijing', 'job': 'Engineer'}
>>> person('Jack', 24, **extra)
name: Jack age: 24 other: {'city': 'Beijing', 'job': 'Engineer'}
```

> `**extra`表示把`extra`这个dict的所有key-value用关键字参数传入到函数的`**kw`参数，`kw`将获得一个dict，注意`kw`获得的dict是`extra`的一份拷贝，对`kw`的改动不会影响到函数外的`extra`。

### 命名关键字参数

对于关键字参数，函数的调用者可以传入任意不受限制的关键字参数。至于到底传入了哪些，就需要在函数内部通过`kw`检查。

仍以`person()`函数为例，我们希望检查是否有`city`和`job`参数：

```
def person(name, age, **kw):
    if 'city' in kw:
        # 有city参数
        pass
    if 'job' in kw:
        # 有job参数
        pass
    print('name:', name, 'age:', age, 'other:', kw)
```

但是调用者仍可以传入不受限制的关键字参数：

```
>>> person('Jack', 24, city='Beijing', addr='Chaoyang', zipcode=123456)
```

如果要限制关键字参数的名字，就可以用命名关键字参数，例如，只接收`city`和`job`作为关键字参数。这种方式定义的函数如下：

```
def person(name, age, *, city, job):
    print(name, age, city, job)
```

***和关键字参数`**kw`不同，命名关键字参数需要一个特殊分隔符`*`，`*`后面的参数被视为命名关键字参数。***

调用方式如下：

```
>>> person('Jack', 24, city='Beijing', job='Engineer')
Jack 24 Beijing Engineer
```

***如果函数定义中已经有了一个可变参数，后面跟着的命名关键字参数就不再需要一个特殊分隔符`*`了：***

```
def person(name, age, *args, city, job):
    print(name, age, args, city, job)
```

***命名关键字参数必须传入参数名，这和位置参数不同。如果没有传入参数名，调用将报错***：

```
>>> person('Jack', 24, 'Beijing', 'Engineer')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: person() missing 2 required keyword-only arguments: 'city' and 'job'
```

由于调用时缺少参数名`city`和`job`，Python解释器把前两个参数视为位置参数，后两个参数传给`*args`，但缺少命名关键字参数导致报错。

*命名关键字参数可以有缺省值，从而简化调用*：

```
def person(name, age, *, city='Beijing', job):
    print(name, age, city, job)
```

由于命名关键字参数`city`具有默认值，调用时，可不传入`city`参数：

```
>>> person('Jack', 24, job='Engineer')
Jack 24 Beijing Engineer
```

*使用命名关键字参数时，要特别注意，如果没有可变参数，就必须加一个`*`作为特殊分隔符。如果缺少`*`，Python解释器将无法识别位置参数和命名关键字参数：*

```
def person(name, age, city, job):
    # 缺少 *，city和job被视为位置参数
    pass
```

### 参数组合

在Python中定义函数，可以用必选参数、默认参数、可变参数、关键字参数和命名关键字参数，这5种参数都可以组合使用。但是请注意，***参数定义的顺序必须是：必选参数、默认参数、可变参数、命名关键字参数和关键字参数。***

比如定义一个函数，包含上述若干种参数：

```
def f1(a, b, c=0, *args, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'args =', args, 'kw =', kw)

def f2(a, b, c=0, *, d, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'd =', d, 'kw =', kw)
```

在函数调用的时候，Python解释器自动按照参数位置和参数名把对应的参数传进去。

```
>>> f1(1, 2)
a = 1 b = 2 c = 0 args = () kw = {}
>>> f1(1, 2, c=3)
a = 1 b = 2 c = 3 args = () kw = {}
>>> f1(1, 2, 3, 'a', 'b')
a = 1 b = 2 c = 3 args = ('a', 'b') kw = {}
>>> f1(1, 2, 3, 'a', 'b', x=99)
a = 1 b = 2 c = 3 args = ('a', 'b') kw = {'x': 99}
>>> f2(1, 2, d=99, ext=None)
a = 1 b = 2 c = 0 d = 99 kw = {'ext': None}
```

最神奇的是通过一个tuple和dict，你也可以调用上述函数：

```
>>> args = (1, 2, 3, 4)
>>> kw = {'d': 99, 'x': '#'}
>>> f1(*args, **kw)
a = 1 b = 2 c = 3 args = (4,) kw = {'d': 99, 'x': '#'}
>>> args = (1, 2, 3)
>>> kw = {'d': 88, 'x': '#'}
>>> f2(*args, **kw)
a = 1 b = 2 c = 3 d = 88 kw = {'x': '#'}
```

所以，对于任意函数，都可以通过类似`func(*args, **kw)`的形式调用它，无论它的参数是如何定义的。

 虽然可以组合多达5种参数，但不要同时使用太多的组合，否则函数接口的可理解性很差。

## 递归函数

于是，`fact(n)`用递归的方式写出来就是：

```
def fact(n):
    if n==1:
        return 1
    return n * fact(n - 1)
```

递归函数的优点是定义简单，逻辑清晰。理论上，所有的递归函数都可以写成循环的方式，但循环的逻辑不如递归清晰。

*使用递归函数需要注意防止栈溢出。*

> 在计算机中，函数调用是通过栈（stack）这种数据结构实现的，每当进入一个函数调用，栈就会加一层栈帧，每当函数返回，栈就会减一层栈帧。由于栈的大小不是无限的，所以，递归调用的次数过多，会导致栈溢出。

可以试试`fact(1000)`：

```
>>> fact(1000)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<stdin>", line 4, in fact
  ...
  File "<stdin>", line 4, in fact
RuntimeError: maximum recursion depth exceeded in comparison
```

***解决递归调用栈溢出的方法是通过尾递归优化，事实上尾递归和循环的效果是一样的，所以，把循环看成是一种特殊的尾递归函数也是可以的。***

> 尾递归是指，在函数返回的时候，调用自身本身，并且，return语句不能包含表达式。这样，编译器或者解释器就可以把尾递归做优化，使递归本身无论调用多少次，都只占用一个栈帧，不会出现栈溢出的情况。

上面的`fact(n)`函数由于`return n * fact(n - 1)`引入了乘法表达式，所以就不是尾递归了。***要改成尾递归方式，需要多一点代码，主要是要把每一步的乘积传入到递归函数中***：

```
def fact(n):
    return fact_iter(n, 1)

def fact_iter(num, product):
    if num == 1:
        return product
    return fact_iter(num - 1, num * product)
```

可以看到，`return fact_iter(num - 1, num * product)`仅返回递归函数本身，`num - 1`和`num * product`在函数调用前就会被计算，不影响函数调用。

`fact(5)`对应的`fact_iter(5, 1)`的调用如下：

```
===> fact_iter(5, 1)
===> fact_iter(4, 5)
===> fact_iter(3, 20)
===> fact_iter(2, 60)
===> fact_iter(1, 120)
===> 120
```

尾递归调用时，如果做了优化，栈不会增长，因此，无论多少次调用也不会导致栈溢出。

遗憾的是，大多数编程语言没有针对尾递归做优化，Python解释器也没有做优化，所以，即使把上面的`fact(n)`函数改成尾递归方式，也会导致栈溢出。

# 高级特性

## 切片

取一个list或tuple的部分元素是非常常见的操作。比如，一个list如下：

```
>>> L = ['Michael', 'Sarah', 'Tracy', 'Bob', 'Jack']
```

取前3个元素，应该怎么做？

笨办法：

```
>>> [L[0], L[1], L[2]]
['Michael', 'Sarah', 'Tracy']
```

之所以是笨办法是因为扩展一下，取前N个元素就没辙了。

取前N个元素，也就是索引为0-(N-1)的元素，可以用循环：

```
>>> r = []
>>> n = 3
>>> for i in range(n):
...     r.append(L[i])
... 
>>> r
['Michael', 'Sarah', 'Tracy']
```

*对这种经常取指定索引范围的操作，用循环十分繁琐，因此，Python提供了切片（Slice）操作符，能大大简化这种操作。*

对应上面的问题，取前3个元素，用一行代码就可以完成切片：

```
>>> L[0:3]
['Michael', 'Sarah', 'Tracy']
```

> *`L[0:3]`表示，从索引`0`开始取，直到索引`3`为止，但不包括索引`3`。即索引`0`，`1`，`2`，正好是3个元素。*

*如果第一个索引是`0`，还可以省略：*

```
>>> L[:3]
['Michael', 'Sarah', 'Tracy']
```

也可以从索引1开始，取出2个元素出来：

```
>>> L[1:3]
['Sarah', 'Tracy']
```

类似的，*既然Python支持`L[-1]`取倒数第一个元素，那么它同样支持倒数切片*，试试：

```
>>> L[-2:]
['Bob', 'Jack']
>>> L[-2:-1]
['Bob']
```

***记住倒数第一个元素的索引是`-1`。***

切片操作十分有用。我们先创建一个0-99的数列：

```
>>> L = list(range(100))
>>> L
[0, 1, 2, 3, ..., 99]
```

可以通过切片轻松取出某一段数列。比如前10个数：

```
>>> L[:10]
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```

后10个数：

```
>>> L[-10:]
[90, 91, 92, 93, 94, 95, 96, 97, 98, 99]
```

前11-20个数：

```
>>> L[10:20]
[10, 11, 12, 13, 14, 15, 16, 17, 18, 19]
```

前10个数，每两个取一个：

```
>>> L[:10:2]
[0, 2, 4, 6, 8]
```

所有数，每5个取一个：

```
>>> L[::5]
[0, 5, 10, 15, 20, 25, 30, 35, 40, 45, 50, 55, 60, 65, 70, 75, 80, 85, 90, 95]
```

*甚至什么都不写，只写`[:]`就可以原样复制一个list：*

```
>>> L[:]
[0, 1, 2, 3, ..., 99]
```

> tuple也是一种list，唯一区别是tuple不可变。因此，tuple也可以用切片操作，只是操作的结果仍是tuple：

```
>>> (0, 1, 2, 3, 4, 5)[:3]
(0, 1, 2)
```

> 字符串`'xxx'`也可以看成是一种list，每个元素就是一个字符。因此，字符串也可以用切片操作，只是操作结果仍是字符串：

```
>>> 'ABCDEFG'[:3]
'ABC'
>>> 'ABCDEFG'[::2]
'ACEG'
```

在很多编程语言中，针对字符串提供了很多各种截取函数（例如，substring），其实目的就是对字符串切片。Python没有针对字符串的截取函数，只需要切片一个操作就可以完成，非常简单。

## 迭代

如果给定一个`list`或`tuple`，我们可以通过`for`循环来遍历这个`list`或`tuple`，这种遍历我们称为*迭代（Iteration）*。

在Python中，迭代是通过`for ... in`来完成的，而很多语言比如C语言，迭代`list`是通过下标完成的，比如C代码：

```
for (i=0; i<length; i++) {
    n = list[i];
}
```

可以看出，Python的`for`循环抽象程度要高于C的`for`循环，*因为Python的`for`循环不仅可以用在`list`或`tuple`上，还可以作用在其他可迭代对象上。*

`list`这种数据类型虽然有下标，但很多其他数据类型是没有下标的，但是，***只要是可迭代对象，无论有无下标，都可以迭代，***比如`dict`就可以迭代：

```
>>> d = {'a': 1, 'b': 2, 'c': 3}
>>> for key in d:
...     print(key)
...
a
c
b
```

因为`dict`的存储不是按照`list`的方式顺序排列，所以，迭代出的结果顺序很可能不一样。

> 默认情况下，`dict`迭代的是key。如果要迭代value，可以用`for value in d.values()`，如果要同时迭代key和value，可以用`for k, v in d.items()`。

由于字符串也是可迭代对象，因此，也可以作用于`for`循环：

```
>>> for ch in 'ABC':
...     print(ch)
...
A
B
C
```

所以，当我们使用`for`循环时，只要作用于一个可迭代对象，`for`循环就可以正常运行，而我们不太关心该对象究竟是`list`还是其他数据类型。

***那么，如何判断一个对象是可迭代对象呢？方法是通过`collections.abc`模块的`Iterable`类型判断：***

```
>>> from collections.abc import Iterable
>>> isinstance('abc', Iterable) # str是否可迭代
True
>>> isinstance([1,2,3], Iterable) # list是否可迭代
True
>>> isinstance(123, Iterable) # 整数是否可迭代
False
```

*最后一个小问题，如果要对`list`实现类似Java那样的下标循环怎么办？Python内置的`enumerate`函数可以把一个`list`变成索引-元素对，这样就可以在`for`循环中同时迭代索引和元素本身：*

```
>>> for i, value in enumerate(['A', 'B', 'C']):
...     print(i, value)
...
0 A
1 B
2 C
```

上面的`for`循环里，同时引用了两个变量，在Python里是很常见的，比如下面的代码：

```
>>> for x, y in [(1, 1), (2, 4), (3, 9)]:
...     print(x, y)
...
1 1
2 4
3 9
```

## 列表生成式

列表生成式即*List Comprehensions*，是Python内置的非常简单却强大的可以用来*创建list的生成式*。

举个例子，要生成list `[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]`可以用`list(range(1, 11))`：

```
>>> list(range(1, 11))
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

但如果要生成`[1x1, 2x2, 3x3, ..., 10x10]`怎么做？方法一是循环：

```
>>> L = []
>>> for x in range(1, 11):
...    L.append(x * x)
...
>>> L
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

但是循环太繁琐，而列表生成式则可以用一行语句代替循环生成上面的list：

```
>>> [x * x for x in range(1, 11)]
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

> 写列表生成式时，把要生成的元素`x * x`放到前面，后面跟`for`循环，就可以把list创建出来，十分有用，多写几次，很快就可以熟悉这种语法。

for循环后面还可以加上if判断，这样我们就可以筛选出仅偶数的平方：

```
>>> [x * x for x in range(1, 11) if x % 2 == 0]
[4, 16, 36, 64, 100]
```

还可以使用两层循环，可以生成全排列：

```
>>> [m + n for m in 'ABC' for n in 'XYZ']
['AX', 'AY', 'AZ', 'BX', 'BY', 'BZ', 'CX', 'CY', 'CZ']
```

三层和三层以上的循环就很少用到了。

运用列表生成式，可以写出非常简洁的代码。例如，列出当前目录下的所有文件和目录名，可以通过一行代码实现：

```
>>> import os # 导入os模块，模块的概念后面讲到
>>> [d for d in os.listdir('.')] # os.listdir可以列出文件和目录
['.emacs.d', '.ssh', '.Trash', 'Adlm', 'Applications', 'Desktop', 'Documents', 'Downloads', 'Library', 'Movies', 'Music', 'Pictures', 'Public', 'VirtualBox VMs', 'Workspace', 'XCode']
```

`for`循环其实可以同时使用两个甚至多个变量，比如`dict`的`items()`可以同时迭代key和value：

```
>>> d = {'x': 'A', 'y': 'B', 'z': 'C' }
>>> for k, v in d.items():
...     print(k, '=', v)
...
y = B
x = A
z = C
```

因此，列表生成式也可以使用两个变量来生成list：

```
>>> d = {'x': 'A', 'y': 'B', 'z': 'C' }
>>> [k + '=' + v for k, v in d.items()]
['y=B', 'x=A', 'z=C']
```

最后把一个list中所有的字符串变成小写：

```
>>> L = ['Hello', 'World', 'IBM', 'Apple']
>>> [s.lower() for s in L]
['hello', 'world', 'ibm', 'apple']
```

### if ... else

使用列表生成式的时候，有些童鞋经常搞不清楚`if...else`的用法。

例如，以下代码正常输出偶数：

```
>>> [x for x in range(1, 11) if x % 2 == 0]
[2, 4, 6, 8, 10]
```

但是，我们不能在最后的`if`加上`else`：

```
>>> [x for x in range(1, 11) if x % 2 == 0 else 0]
  File "<stdin>", line 1
    [x for x in range(1, 11) if x % 2 == 0 else 0]
                                              ^
SyntaxError: invalid syntax
```

这是因为跟在`for`后面的`if`是一个筛选条件，不能带`else`，否则如何筛选？

另一些童鞋发现把`if`写在`for`前面必须加`else`，否则报错：

```
>>> [x if x % 2 == 0 for x in range(1, 11)]
  File "<stdin>", line 1
    [x if x % 2 == 0 for x in range(1, 11)]
                       ^
SyntaxError: invalid syntax
```

这是因为`for`前面的部分是一个表达式，它必须根据`x`计算出一个结果。因此，考察表达式：`x if x % 2 == 0`，它无法根据`x`计算出结果，因为缺少`else`，必须加上`else`：

```
>>> [x if x % 2 == 0 else -x for x in range(1, 11)]
[-1, 2, -3, 4, -5, 6, -7, 8, -9, 10]
```

上述`for`前面的表达式`x if x % 2 == 0 else -x`才能根据`x`计算出确定的结果。

***可见，在一个列表生成式中，`for`前面的`if ... else`是表达式，而`for`后面的`if`是过滤条件，不能带`else`。***

## 生成器

通过列表生成式，我们可以直接创建一个列表。但是，受到内存限制，列表容量肯定是有限的。而且，创建一个包含100万个元素的列表，不仅占用很大的存储空间，如果我们仅仅需要访问前面几个元素，那后面绝大多数元素占用的空间都白白浪费了。

所以，如果列表元素可以按照某种算法推算出来，那我们是否可以在循环的过程中不断推算出后续的元素呢？这样就不必创建完整的list，从而节省大量的空间。***在Python中，这种一边循环一边计算的机制，称为生成器：generator。***

*要创建一个generator，有很多种方法。第一种方法很简单，只要把一个列表生成式的`[]`改成`()`，就创建了一个generator：*

```
>>> L = [x * x for x in range(10)]
>>> L
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
>>> g = (x * x for x in range(10))
>>> g
<generator object <genexpr> at 0x1022ef630>
```

创建`L`和`g`的区别仅在于最外层的`[]`和`()`，`L`是一个list，而`g`是一个generator。

我们可以直接打印出list的每一个元素，但我们怎么打印出generator的每一个元素呢？

如果要一个一个打印出来，可以通过`next()`函数获得generator的下一个返回值：

```
>>> next(g)
0
>>> next(g)
1
>>> next(g)
4
>>> next(g)
9
>>> next(g)
16
>>> next(g)
25
>>> next(g)
36
>>> next(g)
49
>>> next(g)
64
>>> next(g)
81
>>> next(g)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
```

我们讲过，generator保存的是算法，每次调用`next(g)`，就计算出`g`的下一个元素的值，直到计算到最后一个元素，没有更多的元素时，抛出`StopIteration`的错误。

当然，上面这种不断调用`next(g)`实在是太变态了，正确的方法是使用`for`循环，因为generator也是可迭代对象：

```
>>> g = (x * x for x in range(10))
>>> for n in g:
...     print(n)
... 
0
1
4
9
16
25
36
49
64
81
```

所以，我们创建了一个generator后，基本上永远不会调用`next()`，而是通过`for`循环来迭代它，并且不需要关心`StopIteration`的错误。

generator非常强大。如果推算的算法比较复杂，用类似列表生成式的`for`循环无法实现的时候，还可以用函数来实现。

比如，著名的斐波拉契数列（Fibonacci），除第一个和第二个数外，任意一个数都可由前两个数相加得到：

1, 1, 2, 3, 5, 8, 13, 21, 34, ...

斐波拉契数列用列表生成式写不出来，但是，用函数把它打印出来却很容易：

```
def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        print(b)
        a, b = b, a + b
        n = n + 1
    return 'done'
```

*注意*，赋值语句：

```
a, b = b, a + b
```

相当于：

```
t = (b, a + b) # t是一个tuple
a = t[0]
b = t[1]
```

但不必显式写出临时变量t就可以赋值。

上面的函数可以输出斐波那契数列的前N个数：

```
>>> fib(6)
1
1
2
3
5
8
'done'
```

仔细观察，可以看出，`fib`函数实际上是定义了斐波拉契数列的推算规则，可以从第一个元素开始，推算出后续任意的元素，这种逻辑其实非常类似generator。

也就是说，上面的函数和generator仅一步之遥。要把`fib`函数变成generator函数，只需要把`print(b)`改为`yield b`就可以了：

```
def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        yield b
        a, b = b, a + b
        n = n + 1
    return 'done'
```

这就是定义generator的另一种方法。*如果一个函数定义中包含`yield`关键字，那么这个函数就不再是一个普通函数，而是一个generator函数，调用一个generator函数将返回一个generator：*

```
>>> f = fib(6)
>>> f
<generator object fib at 0x104feaaa0>
```

这里，最难理解的就是generator函数和普通函数的执行流程不一样。普通函数是顺序执行，遇到`return`语句或者最后一行函数语句就返回。而变成generator的函数，在每次调用`next()`的时候执行，遇到`yield`语句返回，再次执行时从上次返回的`yield`语句处继续执行。

举个简单的例子，定义一个generator函数，依次返回数字1，3，5：

```
def odd():
    print('step 1')
    yield 1
    print('step 2')
    yield(3)
    print('step 3')
    yield(5)
```

调用该generator函数时，首先要生成一个generator对象，然后用`next()`函数不断获得下一个返回值：

```
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
>>> next(o)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
```

可以看到，`odd`不是普通函数，而是generator函数，在执行过程中，遇到`yield`就中断，下次又继续执行。执行3次`yield`后，已经没有`yield`可以执行了，所以，第4次调用`next(o)`就报错。

 请务必注意：调用generator函数会创建一个generator对象，多次调用generator函数会创建多个相互独立的generator。

有的童鞋会发现这样调用`next()`每次都返回1：

```
>>> next(odd())
step 1
1
>>> next(odd())
step 1
1
>>> next(odd())
step 1
1
```

原因在于`odd()`会创建一个新的generator对象，上述代码实际上创建了3个完全独立的generator，对3个generator分别调用`next()`当然每个都会返回第一个值。

*正确的写法是创建一个generator对象，然后不断对这一个generator对象调用`next()`：*

```
>>> g = odd()
>>> next(g)
step 1
1
>>> next(g)
step 2
3
>>> next(g)
step 3
5
```

回到`fib`的例子，我们在循环过程中不断调用`yield`，就会不断中断。当然要给循环设置一个条件来退出循环，不然就会产生一个无限数列出来。

同样的，把函数改成generator函数后，我们基本上从来不会用`next()`来获取下一个返回值，而是直接使用`for`循环来迭代：

```
>>> for n in fib(6):
...     print(n)
...
1
1
2
3
5
8
```

但是用`for`循环调用generator时，发现拿不到generator的`return`语句的返回值。如果想要拿到返回值，必须捕获`StopIteration`错误，返回值包含在`StopIteration`的`value`中：

```
>>> g = fib(6)
>>> while True:
...     try:
...         x = next(g)
...         print('g:', x)
...     except StopIteration as e:
...         print('Generator return value:', e.value)
...         break
...
g: 1
g: 1
g: 2
g: 3
g: 5
g: 8
Generator return value: done
```

关于如何捕获错误，后面的错误处理还会详细讲解。

## 迭代器

我们已经知道，可以直接作用于`for`循环的数据类型有以下几种：

一类是集合数据类型，如`list`、`tuple`、`dict`、`set`、`str`等；

一类是`generator`，包括生成器和带`yield`的generator function。

这些可以直接作用于`for`循环的对象统称为可迭代对象：`Iterable`。

可以使用`isinstance()`判断一个对象是否是`Iterable`对象：

```
>>> from collections.abc import Iterable
>>> isinstance([], Iterable)
True
>>> isinstance({}, Iterable)
True
>>> isinstance('abc', Iterable)
True
>>> isinstance((x for x in range(10)), Iterable)
True
>>> isinstance(100, Iterable)
False
```

而生成器不但可以作用于`for`循环，还可以被`next()`函数不断调用并返回下一个值，直到最后抛出`StopIteration`错误表示无法继续返回下一个值了。

*可以被`next()`函数调用并不断返回下一个值的对象称为迭代器：`Iterator`。*

可以使用`isinstance()`判断一个对象是否是`Iterator`对象：

```
>>> from collections.abc import Iterator
>>> isinstance((x for x in range(10)), Iterator)
True
>>> isinstance([], Iterator)
False
>>> isinstance({}, Iterator)
False
>>> isinstance('abc', Iterator)
False
```

生成器都是`Iterator`对象，但`list`、`dict`、`str`虽然是`Iterable`，却不是`Iterator`。

把`list`、`dict`、`str`等`Iterable`变成`Iterator`可以使用`iter()`函数：

```
>>> isinstance(iter([]), Iterator)
True
>>> isinstance(iter('abc'), Iterator)
True
```

你可能会问，为什么`list`、`dict`、`str`等数据类型不是`Iterator`？

这是因为Python的`Iterator`对象表示的是一个数据流，Iterator对象可以被`next()`函数调用并不断返回下一个数据，直到没有数据时抛出`StopIteration`错误。可以把这个数据流看做是一个有序序列，但我们却不能提前知道序列的长度，只能不断通过`next()`函数实现按需计算下一个数据，所以`Iterator`的计算是惰性的，只有在需要返回下一个数据时它才会计算。

`Iterator`甚至可以表示一个无限大的数据流，例如全体自然数。而使用list是永远不可能存储全体自然数的。

# 函数式编程

函数是Python内建支持的一种封装，我们通过把大段代码拆成函数，通过一层一层的函数调用，就可以把复杂任务分解成简单的任务，这种分解可以称之为面向过程的程序设计。函数就是面向过程的程序设计的基本单元。

而函数式编程（请注意多了一个“式”字）——Functional Programming，虽然也可以归结到面向过程的程序设计，但其思想更接近数学计算。

我们首先要搞明白计算机（Computer）和计算（Compute）的概念。

在计算机的层次上，CPU执行的是加减乘除的指令代码，以及各种条件判断和跳转指令，所以，汇编语言是最贴近计算机的语言。

而计算则指数学意义上的计算，越是抽象的计算，离计算机硬件越远。

对应到编程语言，就是越低级的语言，越贴近计算机，抽象程度低，执行效率高，比如C语言；越高级的语言，越贴近计算，抽象程度高，执行效率低，比如Lisp语言。

函数式编程就是一种抽象程度很高的编程范式，纯粹的函数式编程语言编写的函数没有变量，因此，任意一个函数，只要输入是确定的，输出就是确定的，这种纯函数我们称之为没有副作用。而允许使用变量的程序设计语言，由于函数内部的变量状态不确定，同样的输入，可能得到不同的输出，因此，这种函数是有副作用的。

函数式编程的一个特点就是，允许把函数本身作为参数传入另一个函数，还允许返回一个函数！

Python对函数式编程提供部分支持。由于Python允许使用变量，因此，Python不是纯函数式编程语言。

## 高阶函数

高阶函数英文叫Higher-order function。什么是高阶函数？我们以实际代码为例子，一步一步深入概念。

### 变量可以指向函数

以Python内置的求绝对值的函数`abs()`为例，调用该函数用以下代码：

```
>>> abs(-10)
10
```

但是，如果只写`abs`呢？

```
>>> abs
<built-in function abs>
```

可见，`abs(-10)`是函数调用，而`abs`是函数本身。

要获得函数调用结果，我们可以把结果赋值给变量：

```
>>> x = abs(-10)
>>> x
10
```

但是，如果把函数本身赋值给变量呢？

```
>>> f = abs
>>> f
<built-in function abs>
```

***结论：函数本身也可以赋值给变量，即：变量可以指向函数。***

如果一个变量指向了一个函数，那么，可否通过该变量来调用这个函数？用代码验证一下：

```
>>> f = abs
>>> f(-10)
10
```

成功！说明变量`f`现在已经指向了`abs`函数本身。直接调用`abs()`函数和调用变量`f()`完全相同。

### 函数名也是变量

那么函数名是什么呢？***函数名其实就是指向函数的变量！对于`abs()`这个函数，完全可以把函数名`abs`看成变量，它指向一个可以计算绝对值的函数！***

如果把`abs`指向其他对象，会有什么情况发生？

```
>>> abs = 10
>>> abs(-10)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'int' object is not callable
```

> 把`abs`指向`10`后，就无法通过`abs(-10)`调用该函数了！因为`abs`这个变量已经不指向求绝对值函数而是指向一个整数`10`！

当然实际代码绝对不能这么写，这里是为了说明函数名也是变量。要恢复`abs`函数，请重启Python交互环境。

注：由于`abs`函数实际上是定义在`import builtins`模块中的，所以要让修改`abs`变量的指向在其它模块也生效，要用`import builtins; builtins.abs = 10`。

### 传入函数

> 既然变量可以指向函数，函数的参数能接收变量，那么一个函数就可以接收另一个函数作为参数，这种函数就称之为***高阶函数***。

一个最简单的高阶函数：

```
def add(x, y, f):
    return f(x) + f(y)
```

当我们调用`add(-5, 6, abs)`时，参数`x`，`y`和`f`分别接收`-5`，`6`和`abs`，根据函数定义，我们可以推导计算过程为：

```
x = -5
y = 6
f = abs
f(x) + f(y) ==> abs(-5) + abs(6) ==> 11
return 11
```

用代码验证一下：

```
# -*- coding: utf-8 -*-
def add(x, y, f):
    return f(x) + f(y)

print(add(-5, 6, abs))
```

# IO 运算符 语句

## 输入和输出

### 格式化输出

### 格式化符号

| 格式符号 | 转换                 |
| -------- | -------------------- |
| %s       | 字符串               |
| %d       | 有符号的十进制整数   |
| %f       | 浮点数               |
| %c       | 字符                 |
| %u       | 无符号十进制整数     |
| %o       | 八进制整数           |
| %x       | 十六进制整数小写(ox) |
| %X       | 十六进制整数大写(OX) |
| %e       | 科学计数法小写 e     |
| %E       | 科学计数法大写 E     |
| %g       | %f和%e的简写         |
| %G       | %f和%E的简写         |

技巧

- %06d，表示输出的整数显示位数，不⾜以0补全，超出当前位数则原样输出

- %.2f，表示⼩数点后显示的⼩数位数。
- 格式化字符串除了%s，还可以写为 `f'{表达式}'`，f-格式化字符串是Python3.6中新增的格式化⽅法，该⽅法更简单易读

```
print(f'我的名字是{name}, 明年{age + 1}岁了')
```

### 转义字符

\n ：换⾏。

\t ：制表符，⼀个tab键（4个空格）的距离



### 结束符

想⼀想，为什么两个print会换⾏输出？

```
print('输出的内容', end="\n") 
```

在Python中，*print() 默认⾃带 end="\n" 这个换⾏结束符*，所以导致每两个 print 直接会换⾏展示，⽤户可以按需求更改结束符

## 输入

### 输入的语法

```
input("提示信息")
```

- 在Python中， input 会把接收到的任意⽤户输⼊的数据都当做字符串处理。

```
password = input('请输⼊您的密码：')
print(f'您输⼊的密码是{password}')
# <class 'str'>
print(type(password))
```



## 运算符

### 算数运算符

| 运算符 | 描述   | 实例                                   |
| ------ | ------ | -------------------------------------- |
| +      |        |                                        |
| -      |        |                                        |
| *      |        |                                        |
| /      |        |                                        |
| //     | *整除* |                                        |
| %      |        |                                        |
| **     | *指数* | 2 ** 4 输出结果为 16，即 2 * 2 * 2 * 2 |

*混合运算优先级顺序：* () ⾼于 ** ⾼于 *  /  //  % ⾼于 + -

### 赋值运算符

多个变量赋值：

```
num1, float1, str1 = 10, 0.5, 'hello'
print(num1)
print(float1)
print(str1)

```

### 复合赋值运算符

+= -=

### 比较运算符

### 逻辑运算符

| 运算符 | 表达式  | 描述   |
| ------ | ------- | ------ |
| and    | x and y | 布尔与 |
| or     | x or y  | 布尔或 |
| not    | not x   | 布尔非 |

### 三目运算符

三⽬运算符也叫三元运算符或三元表达式。

语法如下：

```
条件成⽴执⾏的表达式 if 条件 else 条件不成⽴执⾏的表达式
```

```
a = 1
b = 2
c = a if a > b else b
print(c)

```



## 语句

### 条件语句if

#### 语法

```
if 条件:
 条件成⽴执⾏的代码1
 条件成⽴执⾏的代码2
 ......
 else:
 条件不成⽴执⾏的代码1
 条件不成⽴执⾏的代码2
 ......
```

#### 多重判断 elif

```
if 条件1:
 条件1成⽴执⾏的代码1
 条件1成⽴执⾏的代码2
 ......
elif 条件2：
 条件2成⽴执⾏的代码1
 条件2成⽴执⾏的代码2
 ......
......
else:
 以上条件都不成⽴执⾏执⾏的代码

```

实例：

```
age = int(input('your age:'))
if age < 18:
    print(f'you are {age},NMSL')
elif 18 <= age <= 60:
    print(f'you are {age},NMSL too')
elif age > 60:
    print(f'you are {age},you are senior,welcome')

```

`18 <= age <= 60`简化链式比较

#### if 嵌套

```
if 条件1：
 条件1成⽴执⾏的代码
 条件1成⽴执⾏的代码
 
 		if 条件2：
 		条件2成⽴执⾏的代码
 		条件2成⽴执⾏的代码
 

```





### while 循环

```
while 条件:
 条件成⽴重复执⾏的代码1
 条件成⽴重复执⾏的代码2
 ......

```

#### while嵌套

```
while 条件1:
 条件1成⽴执⾏的代码
 ......
 while 条件2:
 条件2成⽴执⾏的代码
 ......
```



### break and continue

情况⼀：如果吃的过程中，吃完第三个吃饱了，则不需要再吃第4个和第五个苹果，即是吃苹果的动作停⽌，这⾥就是break控制循环流程，即终⽌此循环。

```
i = 1
while i <= 5:
 if i == 4:
 print(f'吃饱了不吃了')
 break
```

情况⼆：如果吃的过程中，吃到第三个吃出⼀个⼤⾍⼦...,是不是这个苹果就不吃了，开始吃第四个苹果，这⾥就是continue控制循环流程，即退出当前⼀次循环继⽽执⾏下⼀次循环代码。

```
i = 1
while i <= 5:
 if i == 3:
 print(f'⼤⾍⼦，第{i}个不吃了')
 # 在continue之前⼀定要修改计数器，否则会陷⼊死循环
 i += 1
 continue
```



### for 循环

```
for 临时变量 in 序列:
 重复执⾏的代码1
 重复执⾏的代码2
 ......
```

### else

循环可以和else配合使⽤，*else下⽅缩进的代码指的是当循环正常结束之后要执⾏的代码。*

#### while…else

```
while 条件:
 条件成⽴重复执⾏的代码
else:
 循环正常结束之后要执⾏的代码
```

```
i = 1
while i <= 5:
    print(f'这是第 {i} 次循环')
    i += 1
else:
    print('这是循环正常结束后执行的语句')

```

### 退出循环的方式

#### 使用 break

```
i = 1
while i <= 5:
    if i == 3:
        print(f'第 {i} 次循环说得不真诚')
        break
    print(f'这是第 {i} 次循环')
    i += 1
else:
    print('这是循环正常结束后执行的语句')
```

输出结果：

```
这是第 1 次循环
这是第 2 次循环
第 3 次循环说得不真诚
```

所谓else指的是*循环正常结束之后*要执⾏的代码，即*如果是break终⽌循环的情况，else下⽅缩进的代码将不执⾏。*



#### continue

```
i = 1
while i <= 5:
    if i == 3:
        print(f'第 {i} 次循环说得不真诚')
        i += 1
        continue
    print(f'这是第 {i} 次循环')
    i += 1
else:
    print('这是循环正常结束后执行的语句')

```

```
这是第 1 次循环
这是第 2 次循环
第 3 次循环说得不真诚
这是第 4 次循环
这是第 5 次循环
这是循环正常结束后执行的语句
```

因为continue是退出当前⼀次循环，继续下⼀次循环，*所以该循环在continue控制下是可以正常结束的*，当循环结束后，则执⾏了else缩进的代码。

### For…else

```
for 临时变量 in 序列:
 重复执⾏的代码
 ...
else:
 循环正常结束之后要执⾏的代码
```

# 列表 元组 集合 字典

## 列表

### 列表是什么

**列表**由一系列按特定顺序排列的元素组成。你可以创建包含字母表中所有字母、数字0～9或所有家庭成员姓名的列表；也可以将任何东西加入列表中，其中的**元素之间可以没有任何关系**。

在Python中，用**方括号（ [ ] ）表示列表，并用逗号分隔其中的元素**。

下面是一个简单的列表示例，其中包含几种自行车：

```
[数据1, 数据2, 数据3, 数据4......]
bicycles = ['trek', 'cannodale', 'redline', 'specialized']
print(bicycles)
```

*如果让Python将列表打印出来，Python将打印列表的内部表示，包括方括号*：

```
['trek', 'cannodale', 'redline', 'specialized']
```

### 查找

#### 下标

列表是有序集合，因此要访问列表的任意元素，只需将该元素的位置（索引 ）告诉Python即可。要**访问列表元素，可指出列表的名称，再指出元素的索引，并将后者放在方括号内**。

```
bicycles = ['trek', 'cannondale', 'redline', 'specialized']
print(bicycles[0])
```

当你请求获取列表元素时，Python只返回该元素，而不包括方括号。

```
trek
```

#### 函数

`index()`：返回指定数据所在位置的下标，如果*查找的数据不存在则报错*

```
列表序列.index(数据, 开始位置下标, 结束位置下标)
```

`count()`：统计指定数据在当前列表中出现的次数

```
name_list = ['Tom', 'Lily', 'Rose']
print(name_list.count('Lily')) # 1
```

`len()`：访问列表⻓度，即列表中数据的个数。

#### 判断是否存在

`in`：判断指定数据在某个列表序列，如果在返回True，否则返回False

`not in`：判断指定数据不在某个列表序列，如果不在返回True，否则返回False

```
name_list = ['Tom', 'Lily', 'Rose']
# 结果：True
print('Lily' in name_list)
# 结果：False
print('Lilys' in name_list)
```

### 增加

作⽤：增加指定数据到列表中。

`append()`：列表结尾追加数据。

```
列表序列.append(数据)
```

列表追加数据的时候，直接在原列表⾥⾯追加了指定数据，即*修改了原列表，故列表为可变类型数据。*

如果append()追加的数据是⼀个序列，则追加整个序列到列表。



`extend()`：列表结尾追加数据，如果数据是⼀个*序列*，则将这个序列的数据*逐⼀*添加到列表。

```
列表序列.extend(数据) 
name_list = ['Tom', 'Lily', 'Rose']
name_list.extend('xiaoming')
# 结果：['Tom', 'Lily', 'Rose', 'x', 'i', 'a', 'o', 'm', 'i', 'n', 'g']

// 注意这里的列表中括号
name_list.extend(['xiaoming', 'xiaohong'])
# 结果：['Tom', 'Lily', 'Rose', 'xiaoming', 'xiaohong']
```



`insert()`：指定位置新增数据。

```
列表序列.insert(位置下标, 数据)
```

### 删除

`del`: `del 目标`

```
// 删除列表
name_list = ['Tom', 'Lily', 'Rose']
# 结果：报错提示：name 'name_list' is not defined
del name_list
print(name_list)

// 删除指定数据
name_list = ['Tom', 'Lily', 'Rose']
del name_list[0]
# 结果：['Lily', 'Rose']
print(name_list)
```



`pop()`：删除指定下标的数据(默认为最后⼀个)，并返回该数据。

```
列表序列.pop(下标)
```

`remove()`：移除列表中某个数据的第⼀个匹配项。

```
列表序列.remove(数据) 
```

`clear()`：清空列表

```
name_list = ['Tom', 'Lily', 'Rose']
name_list.clear()
print(name_list) # 结果： []
```

### 修改

直接修改指定下标数据：

```
name_list = ['Tom', 'Lily', 'Rose']
name_list[0] = 'aaa'
# 结果：['aaa', 'Lily', 'Rose']
print(name_list)
```

`reverse()`：逆置

`sort()`：排序

```
列表序列.sort( key=None, reverse=False)
//reverse = True 降序， reverse = False 升序（默认）
```



### 复制

`copy()`：复制

```
name_list = ['Tom', 'Lily', 'Rose']
name_li2 = name_list.copy()
# 结果：['Tom', 'Lily', 'Rose']
print(name_li2)
```

### 列表的遍历

#### while

```
nameList = ['Tom', 'Lily', 'Rose']

i = 0
while i < len(nameList):
    print(nameList[i])
    i += 1
```

#### for

```
nameList = ['Tom', 'Lily', 'Rose']

for i in nameList:
    print(i)
```

### 列表嵌套

应⽤场景：要存储班级⼀、⼆、三三个班级学⽣姓名，且每个班级的学⽣姓名在⼀个列表。

```
name_list = [['⼩明', '⼩红', '⼩绿'], ['Tom', 'Lily', 'Rose'], ['张三', '李四', '王五']]

# 第⼆步：从李四所在的列表⾥⾯，再按下标找到数据李四
print(name_list[2][1])
```

## 元组

Python将不能修改的值称为不可变的 ，*不可变的列表被称为元组。* 

### 定义元组

定义元组使⽤*⼩括号*，且*逗号隔开各个数据*，数据可以是不同的数据类型。

```
# 多个数据元组
t1 = (10, 20, 30)

# 单个数据元组
t2 = (10,)
```

**注意** 　严格地说，**元组是由逗号标识的**，圆括号只是让元组看起来更整洁、更清晰。如果你要定义只包含一个元素的元组，必须在这个元素后面加上逗号。*否则数据类型为唯⼀的这个数据的数据类型*

```
t2 = (10,)
print(type(t2)) # tuple
t3 = (20)
print(type(t3)) # int
t4 = ('hello')
print(type(t4)) # str
```

### 元组的常见操作

元组数据不⽀持修改，只⽀持查找，具体如下：

- 按下标查找数据
- `index()`：查找某个数据，如果数据存在返回对应的下标，否则报错，语法和列表、字符串的index⽅法相同。
- `count()`：统计某个数据在当前元组出现的次数。
- `len()`：统计元组中数据的个数。

*注意：*元组内的直接数据如果修改则⽴即报错，但是如果元组⾥⾯有列表，修改列表⾥⾯的数据则是⽀持的，故⾃觉很重要。

```
tuple2 = (10, 20, ['aa', 'bb', 'cc'], 50, 30)
print(tuple2[2]) # 访问到列表
# 结果：(10, 20, ['aaaaa', 'bb', 'cc'], 50, 30)

tuple2[2][0] = 'aaaaa'
print(tuple2)
```



#### 修改元组变量

虽然不能修改元组的元素，但可以给存储元组的变量赋值。因此，如果要修改前述矩形的尺寸，可重新定义整个元组：

```
❶ dimensions = (200, 50)
 print("Original dimensions:")
 for dimension in dimensions:
 print(dimension)
❷ dimensions = (400, 100)
❸ print("\nModified dimensions:")
 for dimension in dimensions:
 print(dimension)
```



## 集合

创建集合使⽤ `{}` 或 `set()` ， 但是如果要*创建空集合*只能使⽤ `set()` ，因为 `{}` ⽤来创建空字典。

```
s1 = {10, 20, 30, 40, 50}
print(s1)

s2 = {10, 30, 20, 10, 30, 40, 30, 50}
print(s2)

s3 = set('abcdefg')
print(s3)	
# {'d', 'e', 'g', 'c', 'a', 'b', 'f'} 无序！

s4 = set()
print(type(s4)) # set

s5 = {}
print(type(s5)) # dict
```

特点：

1. 集合可以去掉重复数据；
2. *集合数据是⽆序的，故不⽀持下标*



## 常见操作方法

### 增加数据

`add()`：因为集合有去重功能，所以，当向集合内追加的数据是当前集合已有数据的话，则不进⾏任何操作。

`update()`：追加的数据是序列。

```
s1 = {10, 20}
# s1.update(100) # 报错
s1.update([100, 200])
s1.update('abc')
print(s1)
# {100, 200, 10, 'a', 20, 'c', 'b'}
```

### 删除数据

`remove()`：删除集合中的指定数据，如果*数据不存在则报错*

`discard()`：删除集合中的指定数据，如果数据*不存在不会报错*

`pop()`，随机删除集合中的某个数据，并返回这个数据。

### 查找数据

`in`：判断数据在集合序列

`not in`：判断数据不在集合序列







## 字典dict

字典⾥⾯的数据是*以键值对形式出现，字典数据和数据顺序没有关系，即字典不⽀持下标，后期⽆论数据如何变化，只需要按照对应的键的名字查找数据即可*。

字典特点：

- 符号为⼤括号

- 数据为键值对形式出现

- 各个键值对之间⽤逗号隔开

```
# 有数据字典
dict1 = {'name': 'Tom', 'age': 20, 'gender': '男'}
# 空字典
dict2 = {}
dict3 = dict()
```

### 字典常用操作

##### insert

字典序列[key] = 值

注意：如果key存在则修改这个key对应的值；如果key不存在则新增此键值对。

##### 删除

`del() / del`：删除字典或删除字典中指定键值对。

`clear()`：清空字典

##### 修改

##### 查找

`get()`：`字典序列.get(key, 默认值)` 。如果当前查找的key不存在则返回第⼆个参数(默认值)，如果省略第⼆个参数，则返回None

`keys()`：

```
dict1 = {'name': 'Tom', 'age': 20, 'gender': '男'}
print(dict1.keys()) # dict_keys(['name', 'age', 'gender'])
```

`values()`

```
dict1 = {'name': 'Tom', 'age': 20, 'gender': '男'}
print(dict1.values()) # dict_values(['Tom', 20, '男'])
```

` items()`

```
dict1 = {'name': 'Tom', 'age': 20, 'gender': '男'}
print(dict1.items()) # dict_items([('name', 'Tom'), ('age', 20), ('gender',
'男')])
```

##### 遍历字典的 value

```
dict1 = {'name': 'Tom', 'age': 20, 'gender': '男'}
for value in dict1.values():
 print(value)
```

##### 遍历字典的元素

```
dict1 = {'name': 'Tom', 'age': 20, 'gender': '男'}
for item in dict1.items():
 print(item)
```

##### 遍历字典的键值对

```
dict1 = {'name': 'Tom', 'age': 20, 'gender': '男'}
for key, value in dict1.items():
 print(f'{key} = {value}')
```



# 公共操作

## 运算符

| 运算符  | 描述           | 支持的容器类型           |
| ------- | -------------- | ------------------------ |
| +       | 合并           | 字符串、列表、元组       |
| *       | 复制           | 字符串、列表、元组       |
| in      | 元素是否存在   | 字符串、列表、元组、字典 |
| not int | 元素是否不存在 | 字符串、列表、元组、字典 |

### +

```
# 1. 字符串
str1 = 'aa'
str2 = 'bb'
str3 = str1 + str2
print(str3)  # aabb
# 2. 列表
list1 = [1, 2]
list2 = [10, 20]
list3 = list1 + list2
print(list3)  # [1, 2, 10, 20]
# 3. 元组
t1 = (1, 2)
t2 = (10, 20)
t3 = t1 + t2
print(t3)  # (10, 20, 100, 200)

```



### *

```
# 1. 字符串
print('-' * 10)  # ----------
# 2. 列表
list1 = ['hello']
print(list1 * 4)  # ['hello', 'hello', 'hello', 'hello']
# 3. 元组
t1 = ('world',)
print(t1 * 4)  # ('world', 'world', 'world', 'world')

```



### in / not in

```
# 1. 字符串
print('a' in 'abcd')  # True
print('a' not in 'abcd')  # False
# 2. 列表
list1 = ['a', 'b', 'c', 'd']
print('a' in list1)  # True
print('a' not in list1)  # False
# 3. 元组
t1 = ('a', 'b', 'c', 'd')
print('aa' in t1)  # False
print('aa' not in t1)  # True

```



## 公共方法

| 函数                   | 描述                                                         |
| ---------------------- | ------------------------------------------------------------ |
| len()                  | 计算容器中元素个数                                           |
| del 或 del()           | 删除                                                         |
| max()                  | 返回容器中元素最⼤值                                         |
| min()                  | 返回容器中元素最⼩值                                         |
| range(start,end, step) | ⽣成从start到end的数字，步⻓为 step，供for循环使⽤           |
| enumerate()            | 函数⽤于将⼀个可遍历的数据对象(如列表、元组或字符串)组合为⼀个索引序 |

### del 或 del()

```
# 1. 字符串
str1 = 'abcdefg'
del str1
print(str1)
# 2. 列表
list1 = [10, 20, 30, 40]
del (list1[0])
print(list1)  # [20, 30, 40]

```

### range()

```
# 0 1 2 3 4 5 6 7 8 9
for i in range(10):
 print(i)
```

### enumerate()

`enumerate(可遍历对象, start=0)`：start参数⽤来设置遍历数据的下标的起始值，默认为0。

```
list1 = ['a', 'b', 'c', 'd', 'e']
for i in enumerate(list1):
    print(i)
for index, char in enumerate(list1, start=1):
    print(f'下标是{index}, 对应的字符是{char}')
    
#
(0, 'a')
(1, 'b')
(2, 'c')
(3, 'd')
(4, 'e')
下标是1, 对应的字符是a
下标是2, 对应的字符是b
下标是3, 对应的字符是c
下标是4, 对应的字符是d
下标是5, 对应的字符是e

```

## 容器类型转换

### tuple()

作⽤：将某个序列转换成元组

```
list1 = [10, 20, 30, 40, 50, 20]
s1 = {100, 200, 300, 400, 500}
print(tuple(list1))
print(tuple(s1))
```

### list()

