### 1.格式化输出f-string

**简介**
f-string，亦称为格式化字符串常量（formatted string literals），是Python3.6新引入的一种字符串格式化方法，该方法源于PEP 498 – Literal String Interpolation，主要目的是使格式化字符串的操作更加简便。f-string在形式上是以 f 或 F 修饰符引领的字符串（f'xxx' 或 F'xxx'），以大括号 {} 标明被替换的字段；f-string在本质上并不是字符串常量，而是一个在运行时运算求值的表达式：

```
While other string literals always have a constant value, formatted strings are really expressions evaluated at run time.
（与具有恒定值的其它字符串常量不同，格式化字符串实际上是运行时运算求值的表达式。）
—— Python Documentation
```

f-string在功能方面不逊于传统的%-formatting语句和str.format()函数，同时性能又优于二者，且使用起来也更加简洁明了，因此对于Python3.6及以后的版本，推荐使用f-string进行字符串格式化。

用法

#### 1.1简单使用

f-string用大括号 {} 表示被替换字段，其中直接填入替换内容：

```python
 name = 'Eric'
 f'Hello, my name is {name}'
 'Hello, my name is Eric'

 number = 7
 f'My lucky number is {number}'
 'My lucky number is 7'

 price = 19.99
 f'The price of this book is {price}'
 'The price of this book is 19.99'
```
####  1.2 表达式求值与函数调用

 f-string的大括号 {} 可以填入表达式或调用函数，Python会求出其结果并填入返回的字符串内：

```python
 f'A total number of {24 * 8 + 4}'
 'A total number of 196'

 f'Complex number {(2 + 2j) / (2 - 3j)}'
 'Complex number (-0.15384615384615388+0.7692307692307692j)'

 name = 'ERIC'
 f'My name is {name.lower()}'
 'My name is eric'

 import math
 f'The answer is {math.log(math.pi)}'
 'The answer is 1.1447298858494002'
```

####  1.3 引号、大括号与反斜杠

 f-string大括号内所用的引号不能和大括号外的引号定界符冲突，可根据情况灵活切换 ' 和 "：

```python
 f'I am {"Eric"}'
 'I am Eric'
 f'I am {'Eric'}'
 File "<stdin>", line 1
 f'I am {'Eric'}'
    ^
 SyntaxError: invalid syntax
```


 若 ' 和 " 不足以满足要求，还可以使用 ''' 和 """：

```python
 f"He said {"I'm Eric"}"
 File "<stdin>", line 1
 f"He said {"I'm Eric"}"
    ^
 SyntaxError: invalid syntax

 f'He said {"I'm Eric"}'
 File "<stdin>", line 1
 f'He said {"I'm Eric"}'
      ^
 SyntaxError: invalid syntax

 f"""He said {"I'm Eric"}"""
 "He said I'm Eric"
 f'''He said {"I'm Eric"}'''
 "He said I'm Eric"
```


 大括号外的引号还可以使用 \ 转义，但大括号内不能使用 \ 转义：

```python
 f'''He\'ll say {"I'm Eric"}'''
 "He'll say I'm Eric"
 f'''He'll say {"I\'m Eric"}'''
 File "<stdin>", line 1
 SyntaxError: f-string expression part cannot include a backslash
```


 f-string大括号外如果需要显示大括号，则应输入连续两个大括号 {{ 和 }}：

```python
 f'5 {"{stars}"}'
 '5 {stars}'
 f'{{5}} {"stars"}'
 '{5} stars'
```


 上面提到，f-string大括号内不能使用 \ 转义，事实上不仅如此，f-string大括号内根本就不允许出现 \。如果确实需要 \，则应首先将包含 \ 的内容用一个变量表示，再在f-string大括号内填入变量名：

```python
 f"newline: {ord('\n')}"
 File "<stdin>", line 1
 SyntaxError: f-string expression part cannot include a backslash

 newline = ord('\n')
 f'newline: {newline}'
 'newline: 10'
```



####  1.4多行f-string

 f-string还可用于多行字符串： 

```python
name = 'Eric'
 age = 27
 f"Hello!" \
 ... f"I'm {name}." \
 ... f"I'm {age}."
 "Hello!I'm Eric.I'm 27."
 f"""Hello!
 ...     I'm {name}.
 ...     I'm {age}."""
 "Hello!\n    I'm Eric.\n    I'm 27."
```

#### 1.5 自定义格式：对齐、宽度、符号、补零、精度、进制等

 f-string采用 {content:format} 设置字符串格式，其中 content 是替换并填入字符串的内容，可以是变量、表达式或函数等，format 是格式描述符。采用默认格式时不必指定 {:format}，如上面例子所示只写 {content} 即可。

关于格式描述符的详细语法及含义可查阅Python官方文档，这里按使用时的先后顺序简要介绍常用格式描述符的含义与作用：

**对齐相关格式描述符**

| 格式描述符 |          含义与作用          |
| :--------: | :--------------------------: |
|     <      | 左对齐（字符串默认对齐方式） |
|     >      |  右对齐（数值默认对齐方式）  |
|     ^      |             居中             |

**数字符号相关格式描述符**

| 格式描述符  |                  含义与作用                   |
| :---------: | :-------------------------------------------: |
|      +      |     负数前加负号（-），正数前加正号（+）      |
|      -      | 负数前加负号（-），正数前不加任何符号（默认） |
| ` `（空格） |      负数前加负号（-），正数前加一个空格      |

注：仅适用于数值类型。

**数字显示方式相关格式描述符**

| 格式描述符 | 含义与作用       |
| ---------- | ---------------- |
| #          | 切换数字显示方式 |

注1：仅适用于数值类型。
注2：# 对不同数值类型的作用效果不同，详见下表：

数值类型	不加#（默认）	加#	区别
二进制整数	'1111011'	'0b1111011'	开头是否显示 0b
八进制整数	'173'	'0o173'	开头是否显示 0o
十进制整数	'123'	'123'	无区别
十六进制整数（小写字母）	'7b'	'0x7b'	开头是否显示 0x
十六进制整数（大写字母）	'7B'	'0X7B'	开头是否显示 0X
宽度与精度相关格式描述符
格式描述符	含义与作用
width	整数 width 指定宽度
0width	整数 width 指定宽度，开头的 0 指定高位用 0 补足宽度
width.precision	整数 width 指定宽度，整数 precision 指定显示精度
注1：0width 不可用于复数类型和非数值类型，width.precision 不可用于整数类型。
注2：width.precision 用于不同格式类型的浮点数、复数时的含义也不同：用于 f、F、e、E 和 % 时 precision 指定的是小数点后的位数，用于 g 和 G 时 precision 指定的是有效数字位数（小数点前位数+小数点后位数）。
注3：width.precision 除浮点数、复数外还可用于字符串，此时 precision 含义是只使用字符串中前 precision 位字符。

示例：

```python
 a = 123.456
 f'a is {a:8.2f}'
 'a is   123.46'
 f'a is {a:08.2f}'
 'a is 00123.46'
 f'a is {a:8.2e}'
 'a is 1.23e+02'
 f'a is {a:8.2%}'
 'a is 12345.60%'
 f'a is {a:8.2g}'
 'a is  1.2e+02'

 s = 'hello'
 f's is {s:8s}'
 's is hello   '
 f's is {s:8.3s}'
 's is hel     '
```

 千位分隔符相关格式描述符
 格式描述符	含义与作用
 ,	使用,作为千位分隔符
 _	使用_作为千位分隔符
 注1：若不指定 , 或 _，则f-string不使用任何千位分隔符，此为默认设置。
 注2：, 仅适用于浮点数、复数与十进制整数：对于浮点数和复数，, 只分隔小数点前的数位。
 注3：_ 适用于浮点数、复数与二、八、十、十六进制整数：对于浮点数和复数，_ 只分隔小数点前的数位；对于二、八、十六进制整数，固定从低位到高位每隔四位插入一个 _（十进制整数是每隔三位插入一个 _）。

示例：

 a = 1234567890.098765
 f'a is {a:f}'
 'a is 1234567890.098765'
 f'a is {a:,f}'
 'a is 1,234,567,890.098765'
 f'a is {a:_f}'
 'a is 1_234_567_890.098765'

 b = 1234567890
 f'b is {b:_b}'
 'b is 100_1001_1001_0110_0000_0010_1101_0010'
 f'b is {b:_o}'
 'b is 111_4540_1322'
 f'b is {b:_d}'
 'b is 1_234_567_890'
 f'b is {b:_x}'
 'b is 4996_02d2'
 1
 2
 3
 4
 5
 6
 7
 8
 9
 10
 11
 12
 13
 14
 15
 16
 17
 格式类型相关格式描述符
 基本格式类型

格式描述符	含义与作用	适用变量类型
s	普通字符串格式	字符串
b	二进制整数格式	整数
c	字符格式，按unicode编码将整数转换为对应字符	整数
d	十进制整数格式	整数
o	八进制整数格式	整数
x	十六进制整数格式（小写字母）	整数
X	十六进制整数格式（大写字母）	整数
e	科学计数格式，以 e 表示 ×10^	浮点数、复数、整数（自动转换为浮点数）
E	与 e 等价，但以 E 表示 ×10^	浮点数、复数、整数（自动转换为浮点数）
f	定点数格式，默认精度（precision）是6	浮点数、复数、整数（自动转换为浮点数）
F	与 f 等价，但将 nan 和 inf 换成 NAN 和 INF	浮点数、复数、整数（自动转换为浮点数）
g	通用格式，小数用 f，大数用 e	浮点数、复数、整数（自动转换为浮点数）
G	与 G 等价，但小数用 F，大数用 E	浮点数、复数、整数（自动转换为浮点数）
%	百分比格式，数字自动乘上100后按 f 格式排版，并加 % 后缀	浮点数、整数（自动转换为浮点数）
常用的特殊格式类型：标准库 datetime 给定的用于排版时间信息的格式类型，适用于 date、datetime 和 time 对象

格式描述符	含义	显示样例
%a	星期几（缩写）	'Sun'
%A	星期几（全名）	'Sunday'
%w	星期几（数字，0 是周日，6 是周六）	'0'
%u	星期几（数字，1 是周一，7 是周日）	'7'
%d	日（数字，以 0 补足两位）	'07'
%b	月（缩写）	'Aug'
%B	月（全名）	'August'
%m	月（数字，以 0 补足两位）	'08'
%y	年（后两位数字，以 0 补足两位）	'14'
%Y	年（完整数字，不补零）	'2014'
%H	小时（24小时制，以 0 补足两位）	'23'
%I	小时（12小时制，以 0 补足两位）	'11'
%p	上午/下午	'PM'
%M	分钟（以 0 补足两位）	'23'
%S	秒钟（以 0 补足两位）	'56'
%f	微秒（以 0 补足六位）	'553777'
%z	UTC偏移量（格式是 ±HHMM[SS]，未指定时区则返回空字符串）	'+1030'
%Z	时区名（未指定时区则返回空字符串）	'EST'
%j	一年中的第几天（以 0 补足三位）	'195'
%U	一年中的第几周（以全年首个周日后的星期为第0周，以 0 补足两位）	'27'
%w	一年中的第几周（以全年首个周一后的星期为第0周，以 0 补足两位）	'28'
%V	一年中的第几周（以全年首个包含1月4日的星期为第1周，以 0 补足两位）	'28'
综合示例
 a = 1234
 f'a is {a:^#10X}'      # 居中，宽度10位，十六进制整数（大写字母），显示0X前缀
 'a is   0X4D2   '

 b = 1234.5678
 f'b is {b:<+10.2f}'    # 左对齐，宽度10位，显示正号（+），定点数格式，2位小数
 'b is +1234.57  '

 c = 12345678
 f'c is {c:015,d}'      # 高位补零，宽度15位，十进制整数，使用,作为千分分割位
 'c is 000,012,345,678'

 d = 0.5 + 2.5j
 f'd is {d:30.3e}'      # 宽度30位，科学计数法，3位小数
 'd is           5.000e-01+2.500e+00j'

 import datetime
 e = datetime.datetime.today()
 f'the time is {e:%Y-%m-%d (%a) %H:%M:%S}'   # datetime时间格式
 'the time is 2018-07-14 (Sat) 20:46:02'
 1
 2
 3
 4
 5
 6
 7
 8
 9
 10
 11
 12
 13
 14
 15
 16
 17
 18
 19
 20
 lambda表达式
 f-string大括号内也可填入lambda表达式，但lambda表达式的 : 会被f-string误认为是表达式与格式描述符之间的分隔符，为避免歧义，需要将lambda表达式置于括号 () 内：

 f'result is {lambda x: x ** 2 + 1 (2)}'
 File "<fstring>", line 1
 (lambda x)
 ^
 SyntaxError: unexpected EOF while parsing

 f'result is {(lambda x: x ** 2 + 1) (2)}'
 'result is 5'
 f'result is {(lambda x: x ** 2 + 1) (2):<+7.2f}'
 'result is +5.00  '
 ————————————————
 版权声明：本文为CSDN博主「sunxb10」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
 原文链接：https://blog.csdn.net/sunxb10/article/details/81036693



### 2.文件操作

```python
# os.walk(top[, topdown=True[, onerror=None[, followlinks=False]]])
# top -- 是你所要遍历的目录的地址, 返回的是一个三元组(root,dirs,files)。
# topdown --可选，为 True，则优先遍历 top 目录，否则优先遍历 top 的子目录(默认为开启)。
#               如果 topdown 参数为 True，walk 会遍历top文件夹，与top 文件夹中每一个子目录。
# root 所指的是当前正在遍历的这个文件夹的本身的地址
# dirs 是一个 list ，内容是该文件夹中所有的目录的名字(不包括子目录)
# files 同样是 list , 内容是该文件夹中所有的文件(不包括子目录)
```



### 3.argparse模块

作者：_海角_
链接：https://www.jianshu.com/p/ea52fdfaa4ad
来源：简书

#### 3.1 简介

`argparse`是python用于解析命令行参数和选项的标准模块，用于代替已经过时的optparse模块。`argparse`模块的作用是用于解析命令行参数。

#### 3.2 使用步骤

```python
import argparse

parser = argparse.ArgumentParser()

parser.add_argument()

parser.parse_args()
```

解释：

1. 首先导入该模块；
2.  然后创建一个解析对象；
3.  然后向该对象中添加你要关注的命令行参数和选项，每一个add_argument方法对应一个你要关注的参数或选项；
4.  最后调用parse_args()方法进行解析；
5.  解析成功之后即可使用。

#### 3.3 创建一个解析对象

```python
ArgumentParser(prog=None, 
               usage=None,
               description=None, 
               epilog=None, 
               parents=[],
               formatter_class=argparse.HelpFormatter, 
               prefix_chars='-',
               fromfile_prefix_chars=None, 
               argument_default=None,
               conflict_handler='error',
               add_help=True
              )
```

**prog**：程序的名字，默认为sys.argv[0]，用来在help信息中描述程序的名称。
**usage**：描述程序用途的字符串
**description**：help信息前的文字。
**epilog**：help信息之后的信息

```python
import argparse
parse = argparse.ArgumentParser(prog = 'argparseDemo',
                                prefix_chars= '+',
                                description='the message info before help info',
								epilog="the message info after help info")
parse.print_help()
```

输出:

```sh
$ python argparseDemo.py 
usage: argparseDemo [+h]

the message info before help info

optional arguments:
  +h, ++help  show this help message and exit

the message info after help info
```

**add_help**：设为False时，help信息里面不再显示-h --help信息。
**prefix_chars**：参数前缀，默认为'-'

```python
import argparse
parse = argparse.ArgumentParser(prog = 'argparseDemo',prefix_chars= '+')
parse.add_argument('+f')
parse.add_argument('++bar')
print(parse.parse_args())
```

输出:

```sh
 $python argparseDemo.py ++bar 123 +f 123
Namespace(bar='123', f='123')
```

**fromfile_prefix_chars**：前缀字符，放在文件名之前
**argument_default**：参数的全局默认值。
**conflict_handler**：对冲突的处理方式，默认为返回错误“error”。还有“resolve”，智能解决冲突。

当用户给程序添加了两个一样的命令参数时，“error”就直接报错，提醒用户。而“resolve”则会去掉第一次出现的命令参数重复的部分或者全部（可能是短命令冲突或者全都冲突）。

#### 3.4 add_argument()方法，用来指定程序需要接受的命令参数

```python
add_argument(name or flags...
             [, action]
             [, nargs]
             [, const]
             [, default]
             [, type]
             [, choices]
             [, required]
             [, help]
             [, metavar]
             [, dest])
```

**name or flags**：参数有两种，可选参数和位置参数。

parse_args()运行时，会用'-'来认证可选参数，剩下的即为位置参数。位置参数必选，可选参数可选。
**添加可选参数：**

```python
 parser.add_argument('-f', '--foo')
```

**添加位置参数：**

```python
 parser.add_argument('bar')
```

```dart
import argparse
parse = argparse.ArgumentParser()
parse.add_argument('-f', '--foo')
parse.add_argument('bar')
parse.parse_args(['baffr'])
```

输出，可见缺少位置参数'bar'时候，程序报错

```sh
Namespace(bar='baffr', foo=None)
>>> parse.parse_args(['123','-f','56'])
Namespace(bar='123', foo='56')
>>> parse.parse_args(['-f','56'])
usage: [-h] [-f FOO] bar
: error: too few arguments
shiqqdeMacBook-Pro:Python Demo sqq$ 
```

### 4.类装饰器

#### 1.@staticmethod与@classmethod的区别

一般来说，要使用某个类的方法，需要先实例化一个对象再调用方法。
而使用@staticmethod或@classmethod，就可以不需要实例化，直接类名.方法名()来调用。
这有利于组织代码，把某些应该属于某个类的函数给放到那个类里去，同时有利于命名空间的整洁。

既然@staticmethod和@classmethod都可以直接类名.方法名()来调用，那他们有什么区别呢
从它们的使用上来看,
@staticmethod**不需要表示自身对象的self和自身类的cls参数，就跟使用函数一样。**
@classmethod**也不需要self参数，但第一个参数需要是表示自身类的cls参数**。
如果在@staticmethod中要调用到这个类的一些属性方法，只能直接类名.属性名或类名.方法名。
而@classmethod因为持有cls参数，可以来调用类的属性，类的方法，实例化对象等，避免硬编码。
下面上代码。

```python
class A(object):  
    bar = 1  
    def foo(self):  
        print 'foo'  
 
    @staticmethod  
    def static_foo():  
        print 'static_foo'  
        print A.bar  
 
    @classmethod  
    def class_foo(cls):  	#这里用了cls参数，即A这个类本身，后面要使用类.属性或类.方法时就可以用cls.属性或cls.方法，避免硬编码
        print 'class_foo'  
        print cls.bar  
        cls().foo()  	#类.方法的调用，没有使用类的名字(A)，避免硬编码
  
A.static_foo()  
A.class_foo()  
输出
static_foo
1
class_foo
1
foo
```

#### 2.@property

在绑定属性时，如果我们直接把属性暴露出去，虽然写起来很简单，但是，没办法检查参数，导致可以把成绩随便改：

```
s = Student()
s.score = 9999
```

这显然不合逻辑。为了限制score的范围，可以通过一个`set_score()`方法来设置成绩，再通过一个`get_score()`来获取成绩，这样，在`set_score()`方法里，就可以检查参数：

```
class Student(object):

    def get_score(self):
        return self._score

    def set_score(self, value):
        if not isinstance(value, int):
            raise ValueError('score must be an integer!')
        if value < 0 or value > 100:
            raise ValueError('score must between 0 ~ 100!')
        self._score = value
```

现在，对任意的Student实例进行操作，就不能随心所欲地设置score了：

```
>>> s = Student()
>>> s.set_score(60) # ok!
>>> s.get_score()
60
>>> s.set_score(9999)
Traceback (most recent call last):
  ...
ValueError: score must between 0 ~ 100!
```

但是，上面的调用方法又略显复杂，没有直接用属性这么直接简单。

有没有既能检查参数，又可以用类似属性这样简单的方式来访问类的变量呢？对于追求完美的Python程序员来说，这是必须要做到的！

还记得装饰器（decorator）可以给函数动态加上功能吗？对于类的方法，装饰器一样起作用。Python内置的`@property`装饰器就是==**负责把一个方法变成属性调用**==的：

```
class Student(object):

    @property
    def score(self):
        return self._score

    @score.setter
    def score(self, value):
        if not isinstance(value, int):
            raise ValueError('score must be an integer!')
        if value < 0 or value > 100:
            raise ValueError('score must between 0 ~ 100!')
        self._score = value
```

`@property`的实现比较复杂，我们先考察如何使用。把一个getter方法变成属性，只需要加上`@property`就可以了，此时，`@property`本身又创建了另一个装饰器`@score.setter`，负责把一个setter方法变成属性赋值，于是，我们就拥有一个可控的属性操作：

```
>>> s = Student()
>>> s.score = 60 # OK，实际转化为s.set_score(60)
>>> s.score # OK，实际转化为s.get_score()
60
>>> s.score = 9999
Traceback (most recent call last):
  ...
ValueError: score must between 0 ~ 100!
```

注意到这个神奇的`@property`，我们在对实例属性操作的时候，就知道该属性很可能不是直接暴露的，而是通过getter和setter方法来实现的。

还可以定义只读属性，只定义getter方法，不定义setter方法就是一个只读属性：

```
class Student(object):

    @property
    def birth(self):
        return self._birth

    @birth.setter
    def birth(self, value):
        self._birth = value

    @property
    def age(self):
        return 2014 - self._birth
```

上面的`birth`是可读写属性，而`age`就是一个**只读**属性，因为`age`可以根据`birth`和当前时间计算出来。

**小结**

`@property`广泛应用在类的定义中，可以让调用者写出简短的代码，同时保证对参数进行必要的检查，这样，程序运行时就减少了出错的可能性。