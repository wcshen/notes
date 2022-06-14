### 1.`new`的用法

起初刚学C++时，很不习惯用`new`，后来看老外的程序，发现几乎都是使用new，想一想区别也不是太大，但是在大一点的项目设计中，有时候不使用new的确会带来很多问题。

当然这都是跟new的用法有关的。`new`创建类对象，使用完后需使用`delete`删除，跟申请内存类似。

所以，`new`有时候又不太适合，比如在频繁调用场合，使用局部`new`类对象就不是个好选择，使用全局类对象或一个经过初始化的全局类指针似乎更加高效。

#### 一、new创建类对象与不new区别 

下面是自己总结的一些关于new创建类对象特点：

- new创建类对象需要指针接收，一处初始化，多处使用
- new创建类对象使用完,需delete销毁
- new创建对象直接使用堆空间，而局部不用new定义类对象则使用栈空间
- new对象指针用途广泛，比如作为函数返回值、函数参数等
- 频繁调用场合并不适合new，就像new申请和释放内存一样

#### 二、new创建类对象实例 

##### 1、new创建类对象例子： 

```C++
CTest* pTest = new CTest();
delete pTest;
```

`pTest`用来接收类对象指针。

不用new，直接使用类定义申明：

`CTest mTest;`

此种创建方式，使用完后不需要手动释放，该类析构函数会自动执行。而new申请的对象，则只有调用到delete时再会执行析构函数，如果程序退出而没有执行delete则会造成内存泄漏。

##### 2、只定义类指针 

这跟不用new申明对象有很大区别，类指针可以先行定义，但类指针只是个通用指针，在new之前并为该类对象分配任何内存空间。比如：

`CTest* pTest = NULL;`

但使用普通方式创建的类对象，在创建之初就已经分配了内存空间。而类指针，如果未经过对象初始化，则不需要delete释放。

##### 3、new对象指针作为函数参数和返回值 

下面是天缘随手写一个例子，不太严谨。主要示意一下类指针对象作为返回值和参数使用。

```cpp
class CTest {  public:   int a;  };    
class CBest {  public:   int b;  };    
CTest* fun(CBest* pBest) {  
                           CTest* pTest = new CTest();   
                           pTest->a = pBest->b;   return pTest;  
}    
int main() {  
             CBest* pBest = new CBest();   
             CTest* pRes= fun(pBest);      
             if(pBest!=NULL)    
             delete pBest;   
             if(pRes!=NULL)    
             delete pRes ;   
             return 0;  
}
```

**C++对象实例化**

```cpp
JAVA：
A a = new A();
为A对象创建了一个实例，但在内存中开辟了两块空间：一块空间在堆区，存放new A（）这个对象；
另一块空间在堆栈，也就是栈，存放a，a的值为new A（）这个对象的内存地址。因为java在JVM中运行，
所以a 描述的内存地址不一定是这个对象真实内存的地址。
 
Object o; // 这是声明一个引用，它的类型是Object，他的值为null，还没有指向任何对象，该引用放在内存的栈区域中。
 
o = new Object(); // new Object()句，实例化了一个对象，就是在堆中申请了一块连续空间用来存放该对象。
= // 运算符，将引向o指向了对象。也就是说将栈中表示引用o的内存地址的内容改写成了Object对象在堆中的地址。
 
C++：
C++ 如果直接定义类，如classA a; a 存在栈上（也意味着复制了对象a在栈中），如果classA a = new classA就存在堆中。
```





最后再说明一下:

**C++中:**

> Student student(20) ; //这里student是引用 对象分配在 栈空间中,这里只是我的理解
>
> Student *student = new Student(20);  //这里student是指针,new Student(20)是分配在堆内存空间的

**但是在Java中**

> Student student(20) ;  //注意:java中没有这样实例化对象的, 要想得到一个对象 必须要new出来.

> Student student ; //这个只是定义了一个引用 ,没有指向任何对象

> Student student = new Student(20);  //定义了一个引用,指向堆内存中的student对象

### 2.memset(),memcp()

memcpy是memory copy的缩写，意为内存复制，在写C语言程序的时候，我们常常会用到它。它的函原型如下：

```c
void *memcpy(void *dest, const void *src, size_t n);
```

它的功能是从src的开始位置拷贝n个字节的数据到dest。如果dest存在数据，将会被覆盖。memcpy函数的返回值是dest的指针。memcpy函数定义在string.h头文件里。

#### 例子

1.将一个字符串数据复制到一块内存。

```c
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#define N 10
int main(void)
{
    char* target=(char*)malloc(sizeof(char)*N);
    memcpy(target,"0123456789",sizeof(char)*N);
    puts(target);
    free(target);
    return 0;
}
```

编译，运行，将输出：`0123456789`
2.将一个字符串数据复制到一块内存的指定位置。

```c
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#define N 10
int main(void)
{
    char* target=(char*)malloc(sizeof(char));
    for(int i=0;i<N;i++){
        memcpy(target+i,"a",sizeof(char));
    }
    puts(target);
    free(target);
    return 0;
}
```

编译，运行，将输出：`aaaaaaaaaa`
3.数据覆盖

```c
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#define N 10
int main(void)
{
    char* target=(char*)malloc(sizeof(char)*N);
    memcpy(target,"0123456789",sizeof(char)*N);
    puts(target);
    memcpy(target,"aaaaa",sizeof(char)*(N-5));
    puts(target);
    free(target);
    return 0;
}
```

编译，运行，将输出：

```
0123456789
aaaaa56789
```

### 3. 获得程序运行时间

```C++
#include <iostream>
#include <ctime>//计时
#include <chrono>

using namespace std;
using namespace chrono;

void time_test()
{
    /*获得程序运行时间:*/
 	//方法一 ctmie
    clock_t startTime,endTime;
    startTime = clock();         //计时开始
    endTime = clock();          //计时结束
    cout << "The run time is " << (double)(endTime - startTime) / CLOCKS_PER_SEC * 1000 << "ms" << endl;

    //方法二 chrono(C++11)
    //这种方法可以计时waitkey(100)这种时间
    auto start = system_clock::now();
    auto end = system_clock::now();
    auto duration = duration_cast<microseconds>(end - start);  //<microseconds> 精确到微秒级别
    cout << "The run time is " << double(duration.count())     //time.count()表示duration里面有多少个微秒
            * microseconds::period::num / microseconds::period::den *1000  << "ms"<< endl;
            * //  double(duration.count())* microseconds::period::num / microseconds::period::den 把微秒转换成秒
}

```

### 4.固定数组大小

```C++
	std::deque<int> v1;
    for(int i = 0;i<10;i++)
    {
        // 固定长度是5,capacity也是5(占用5的内存)
        if(v1.size() > 4)
        {
            v1.pop_front();
            v1.shrink_to_fit();
        }

        v1.push_back(i);
    }

    cout << "size:" << v1.size() << endl;
    for(int j = 0;j < 20;j++)
    {
        cout << v1[j] << " " << j << endl;
//        v1.pop_back();
    }
```

### 5.读取文件

#### 1.sprintf函数

**描述**

C 库函数 **int sprintf(char \*str, const char \*format, ...)** 发送格式化输出到 **str** 所指向的字符串。

**声明**

下面是 sprintf() 函数的声明。

```
int sprintf(char *str, const char *format, ...)
```

**参数**

+ **str** -- 这是指向一个字符数组的指针，该数组存储了 C 字符串。
+ **format** -- 这是字符串，包含了要被写入到字符串 str 的文本。它可以包含嵌入的 format 标签，format 标签可被随后的附加参数中指定的值替换，并按需求进行格式化。format 标签属性是 **%[flags][width][.precision][length]specifier**，具体讲解如下：

| specifier（说明符） | 输出                                      |
| :------------------ | :---------------------------------------- |
| c                   | 字符                                      |
| d 或 i              | 有符号十进制整数                          |
| e                   | 使用 e 字符的科学科学记数法（尾数和指数） |
| E                   | 使用 E 字符的科学科学记数法（尾数和指数） |
| f                   | 十进制浮点数                              |
| g                   | 自动选择 %e 或 %f 中合适的表示法          |
| G                   | 自动选择 %E 或 %f 中合适的表示法          |
| o                   | 有符号八进制                              |
| s                   | 字符的字符串                              |
| u                   | 无符号十进制整数                          |
| x                   | 无符号十六进制整数                        |
| X                   | 无符号十六进制整数（大写字母）            |
| p                   | 指针地址                                  |
| n                   | 无输出                                    |
| %                   | 字符                                      |



| flags（标识） | 描述                                                         |
| :------------ | :----------------------------------------------------------- |
| -             | 在给定的字段宽度内左对齐，默认是右对齐（参见 width 子说明符）。 |
| +             | 强制在结果之前显示加号或减号（+ 或 -），即正数前面会显示 + 号。默认情况下，只有负数前面会显示一个 - 号。 |
| (space)       | 如果没有写入任何符号，则在该值前面插入一个空格。             |
| #             | 与 o、x 或 X 说明符一起使用时，非零值前面会分别显示 0、0x 或 0X。 与 e、E 和 f 一起使用时，会强制输出包含一个小数点，即使后边没有数字时也会显示小数点。默认情况下，如果后边没有数字时候，不会显示显示小数点。 与 g 或 G 一起使用时，结果与使用 e 或 E 时相同，但是尾部的零不会被移除。 |
| 0             | 在指定填充 padding 的数字左边放置零（0），而不是空格（参见 width 子说明符）。 |



| width（宽度） | 描述                                                         |
| :------------ | :----------------------------------------------------------- |
| (number)      | 要输出的字符的最小数目。如果输出的值短于该数，结果会用空格填充。如果输出的值长于该数，结果不会被截断。 |
| *             | 宽度在 format 字符串中未指定，但是会作为附加整数值参数放置于要被格式化的参数之前。 |



| .precision（精度） | 描述                                                         |
| :----------------- | :----------------------------------------------------------- |
| .number            | 对于整数说明符（d、i、o、u、x、X）：precision 指定了要写入的数字的最小位数。如果写入的值短于该数，结果会用前导零来填充。如果写入的值长于该数，结果不会被截断。精度为 0 意味着不写入任何字符。 对于 e、E 和 f 说明符：要在小数点后输出的小数位数。 对于 g 和 G 说明符：要输出的最大有效位数。 对于 s: 要输出的最大字符数。默认情况下，所有字符都会被输出，直到遇到末尾的空字符。 对于 c 类型：没有任何影响。 当未指定任何精度时，默认为 1。如果指定时不带有一个显式值，则假定为 0。 |
| .*                 | 精度在 format 字符串中未指定，但是会作为附加整数值参数放置于要被格式化的参数之前。 |



| length（长度） | 描述                                                         |
| :------------- | :----------------------------------------------------------- |
| h              | 参数被解释为短整型或无符号短整型（仅适用于整数说明符：i、d、o、u、x 和 X）。 |
| l              | 参数被解释为长整型或无符号长整型，适用于整数说明符（i、d、o、u、x 和 X）及说明符 c（表示一个宽字符）和 s（表示宽字符字符串）。 |
| L              | 参数被解释为长双精度型（仅适用于浮点数说明符：e、E、f、g 和 G）。 |

+ **附加参数** -- 根据不同的 format 字符串，函数可能需要一系列的附加参数，每个参数包含了一个要被插入的值，替换了 format 参数中指定的每个 % 标签。参数的个数应与 % 标签的个数相同。

**返回值**

如果成功，则返回写入的字符总数，不包括字符串追加在字符串末尾的空字符。如果失败，则返回一个负数。

**实例**

下面的实例演示了 sprintf() 函数的用法。

```
#include <stdio.h>
#include <math.h>

int main()
{
   char str[80];

   sprintf(str, "Pi 的值 = %f", M_PI);
   puts(str);
   
   return(0);
}
```

让我们编译并运行上面的程序，这将产生以下结果：

```
Pi 的值 = 3.141593
```

#### 2.fread()

函数原型：

size_t  fread(  void  *buffer,  size_t  size,  size_t  count,  FILE  *stream  ) 

【参数】buffer为接收数据的地址，size为一个单元的大小，count为单元个数，stream为文件流。

fread()函数每次从stream中最多读取count个单元，每个单元大小为size个字节，将读取的数据放到buffer；文件流的位置指针后移 size * count 字节。

【返回值】返回实际读取的单元个数。如果小于count，则可能文件结束或读取出错；可以用[ferror()](http://c.biancheng.net/cpp/html/2507.html)检测是否读取出错，用[feof()](http://c.biancheng.net/cpp/html/2514.html)函数检测是否到达文件结尾。如果size或count为0，则返回0。

与fread()相对应的函数为[fwrite()](http://c.biancheng.net/cpp/html/2517.html)，fread() 和 fwrite() 一般用于二进制文件的输入输出

 buffer  是读取的数据存放的内存的指针（可以是数组，也可以是新开辟的空间，buffer就是一个索引）  
  size    是每次读取的字节数  
 count   是读取次数  
 strean  是要读取的文件的指针  
 例如  从文件fp里读取100个字节  可用以下语句 

 fread(buffer,100,1,fp)  
 fread(buffer,50,2,fp)  
 fread(buffer,1,100,fp)  
**************************************************************************************
对读出的二进制流是不能用strlen()或者sizeof()求其长度和大小的。

**************************************************************************************

fread可以读二进制文件，有时用字符方式去读文件不能读完整个文件，但是二进制方式就可以 。

这就是因为字符方式用特定的标记结尾的，读取时只要碰到该标记就自动结束

函数fread()读取[*num*]个对象(每个对象大小为*size*(大小)指定的字节数),并把它们替换到由*buffer*(缓冲区)指定的数组. 数据来自给出的输入流. 函数的返回值是读取的内容数量...

使用feof()或ferror()判断到底发生哪个错误. 

上一段代码：



```C++
void HelpMassage()
{
	FILE *fp;
	int size = 0;
	char *ar ;

	//二进制方式打开文件
	fp = fopen("lining.txt","rb");
	if(NULL == fp)
	{
		printf("Error:Open input.c file fail!\n");
		return;
	}

	//求得文件的大小
	fseek(fp, 0, SEEK_END);
	size = ftell(fp);
	rewind(fp);

	//申请一块能装下整个文件的空间
	ar = (char*)malloc(sizeof(char)*size);

	//读文件
	fread(ar,1,size,fp);//每次读一个，共读size次

	printf("%s",ar);
	fclose(fp);
	free(ar);

	printf("按任意键继续");
	getchar();
	getchar();
}
```



### 6.`左值 右值 std::move std::forward`

#### 6.1 左值,右值,左右引用

**1、左值和右值的概念**

​     左值是可以放在赋值号左边可以被赋值的值；左值必须要在内存中有实体；
​     右值当在赋值号右边取出值赋给其他变量的值；右值可以在内存也可以在CPU寄存器。
​     一个对象被用作右值时，使用的是它的内容(值)，被当作左值时，使用的是它的地址。

左值就是有名字的变量（对象），可以被赋值，可以在多条语句中使用，

而右值呢，就是临时变量（对象），没有名字，只能在一条语句中出现，不能被赋值,

int a = b+c, a 就是左值，其有变量名为a，通过&a可以获取该变量的地址；表达式b+c、函数int func()的返回值是右值，在其被赋值给某一变量前，我们不能通过变量名找到它，＆(b+c)这样的操作则不会通过编译。

C++98中右值是纯右值，纯右值指的是临时变量值、不跟对象关联的字面量值。临时变量指的是非引用返回的函数返回值、表达式等，例如函数int func()的返回值，表达式a+b；不跟对象关联的字面量值，例如true，2，”C”等。

C++11对C++98中的右值进行了扩充。在C++11中右值又分为纯右值（prvalue，Pure Rvalue）和将亡值（xvalue，eXpiring Value）。其中纯右值的概念等同于我们在C++98标准中右值的概念，指的是临时变量和不跟对象关联的字面量值；

将亡值则是C++11新增的跟==右值引用==相关的表达式，这样表达式通常是将要被移动的对象（移为他用），比如返回右值引用T&&的函数返回值、std::move的返回值，或者转换为T&&的类型转换函数的返回值。

**2、引用**

​    引用是C++语法做的优化，引用的本质还是靠指针来实现的。引用相当于变量的别名。

​    引用可以改变指针的指向，还可以改变指针所指向的值。

​    引用的基本规则：

1. 声明引用的时候必须初始化，且一旦绑定，不可把引用绑定到其他对象；即引用必须初始化，不能对引用重定义**；**
2. 对引用的一切操作，就相当于对原对象的操作。

**3、左值引用和右值引用**

  3.1 左值引用
     左值引用的基本语法：type &引用名 = 左值表达式；

  3.2 右值引用

​    右值引用的基本语法type &&引用名 = 右值表达式；

​    右值引用在企业开发人员在代码优化方面会经常用到。

​    右值引用的“&&”中间不可以有空格。

左值引用就是对一个左值进行引用的类型。右值引用就是对一个右值进行引用的类型，**事实上，由于右值通常不具有名字，我们也只能通过引用的方式找到它的存在。**

右值引用和左值引用都是属于引用类型。无论是声明一个左值引用还是右值引用，**都必须立即进行初始化**。而其原因可以理解为是引用类型本身自己并不拥有所绑定对象的内存，只是该对象的一个别名。左值引用是具名变量值的别名，而右值引用则是不具名（匿名）变量的别名。

左值引用通常也不能绑定到右值，但**常量左值引用**是个“万能”的引用类型。它可以接受非常量左值、常量左值、右值对其进行初始化。不过常量左值所引用的右值在它的“余生”中只能是只读的。相对地，非常量左值只能接受非常量左值对其进行初始化。

```C++
int &a = 2;       # 左值引用绑定到右值，编译失败

int b = 2;        # 非常量左值
const int &c = b; # 常量左值引用绑定到非常量左值，编译通过
const int d = 2;  # 常量左值
const int &e = c; # 常量左值引用绑定到常量左值，编译通过
const int &b =2;  # 常量左值引用绑定到右值，编程通过
```

右值引用通常不能绑定到任何的左值，要想绑定一个左值到右值引用，通常需要std::move()将左值强制转换为右值，例如：

```C++
int a;
int &&r1 = c;             # 编译失败
int &&r2 = std::move(a);  # 编译通过
```

下表列出了在C++11中各种引用类型可以引用的值的类型。值得注意的是，只要能够绑定右值的引用类型，都能够延长右值的生命期。

![引用类型](https://img-blog.csdn.net/20160727131907698)

```C++
#include <iostream>

void process_value(int& i) 
{ 
  std::cout << "LValue processed: " << i << std::endl; 
} 

void process_value(int&& i) 
{ 
  std::cout << "RValue processed: " << i << std::endl; 
} 

int main() 
{ 
  int a = 0; 
  process_value(a);
  process_value(1); 
}
```

**4.右值引用的意义**

直观意义：为临时变量续命，也就是为右值续命，因为右值在表达式结束后就消亡了，如果想继续使用右值，那就会动用昂贵的拷贝构造函数。（关于这部分，推荐一本书《深入理解C++11》）
右值引用是用来支持**转移语义**的。转移语义可以将资源 ( 堆，系统对象等 ) 从一个对象转移到另一个对象，这样能够减少不必要的临时对象的创建、拷贝以及销毁，能够大幅度提高 C++ 应用程序的性能。**临时对象的维护 ( 创建和销毁 ) 对性能有严重影响**。
转移语义是和拷贝语义相对的，可以类比文件的剪切与拷贝，当我们将文件从一个目录拷贝到另一个目录时，速度比剪切慢很多。
通过转移语义，临时对象中的资源能够转移其它的对象里。
在现有的 C++ 机制中，我们可以定义拷贝构造函数和赋值函数。要实现转移语义，需要定义转移构造函数，还可以定义转移赋值操作符。对于右值的拷贝和赋值会调用转移构造函数和转移赋值操作符。如果转移构造函数和转移拷贝操作符没有定义，那么就遵循现有的机制，拷贝构造函数和赋值操作符会被调用。
普通的函数和操作符也可以利用右值引用操作符实现转移语义。

**5.move forward**

std::move无条件的将其参数转换为右值，而std::forward只在必要情况下进行这个转换



从实现上讲，std::move基本等同于一个类型转换：static_cast<T&&>(lvalue);

### 7.const

#### 1、函数前后const

函数前const：普通函数或成员函数（非静态成员函数）前均可加const修饰，表示函数的返回值为const，不可修改。格式为：

```C++
const returnType functionName(param list)
```

函数后加const：只有类的非静态成员函数后可以加const修饰，表示该类的this指针为const类型，不能改变类的成员变量的值，即成员变量为read only（例外情况见2），任何改变成员变量的行为均为非法。此类型的函数可称为**只读成员函数**，格式为：

```C++
returnType functionName(param list) const
```

说明：类中const（函数后面加）与static不能同时修饰成员函数，原因有以下两点①C++编译器在实现const的成员函数时，为了确保该函数不能修改类的实例状态，会在函数中添加一个隐式的参数const this*。但当一个成员为static的时候，该函数是没有this指针的，也就是说此时const的用法和static是冲突的；
②两者的语意是矛盾的。static的作用是表示该函数只作用在类型的静态变量上，与类的实例没有关系；而const的作用是确保函数不能修改类的实例的状态，与类型的静态变量没有关系，因此不能同时用它们。

#### 2、const与mutable的区别

从字面意思知道，mutalbe是“可变的，易变的”，跟constant（既C++中的const）是反义词。在C++中，mutable也是为了突破const的限制而设置的。被mutable修饰的变量（成员变量）将永远处于可变的状态，即使在一个const函数中。因此，后const成员函数中可以改变类的mutable类型的成员变量。

```C++
#include <iostream>
using namespace std;

class A{
private:
	int m_a;//int前加mutable关键字修饰即可编译通过
public:
	A():m_a(0){}
	int setA(int a) const
	{
		this->m_a = a;//error: l-value specifies const object
	}

	int setA(int a)
	{
		this->m_a = a;
	}
};

int main()
{
	A a1;
	
	return 0;
```

编译错误：**error C2166: l-value specifies const object**，左值为const，即const修饰后成员函数中的this指针为const，它所指向的成员变量不能被修改，将成员变量用mutable修饰后编译通过。

#### 3、const成员函数与const对象

const成员函数还有另外一项作用，即常量对象相关。对于内置的数据类型，我们可以定义它们的常量，对用户自定义的类类型也是一样，可以定义它们的常量对象。有如下规则：
①、const对象只能调用后const成员函数；

```C++
#include <iostream>
using namespace std;

class A{
private:
	int m_a;
public:
	A():m_a(0){}
	int getA() const
	{
		return m_a;
	}
	int GetA() //非const成员函数，若在后面加上const修饰则编译通过
	{
		return m_a;
	}
	int setA(int a)
	{
		this->m_a = a;
	}
};

int main()
{
	const A a2;//const对象
	int t;
	t = a2.getA();
	t = a2.GetA();//error:const object call non-const member function,only non-const object can call


	return 0;
}
```

错误为：**error C2662: ‘GetA’ : cannot convert ‘this’ pointer from ‘const class A’ to 'class A &'**
②、非const对象既可以调用const成员函数，又可以调用非const成员函数。

```C++
#include <iostream>
using namespace std;

class A{
private:
	int m_a;
public:
	A():m_a(0){}
	int getA() const
	{
		return m_a;
	}
	int GetA()
	{
		return m_a;
	}
	int setA(int a)
	{
		this->m_a = a;
	}
};

int main()
{
	A a1;//非const对象
	int t;
	t = a1.getA();//调用const成员函数，正确
	t = a1.GetA();//调用非const成员函数，正确

	return 0;
}
```

### 8.explicit

C++中， 一个参数的构造函数(或者除了第一个参数外其余参数都有默认值的多参构造函数)， 承担了两个角色。 

1 是个构造；2 是个默认且隐含的类型转换操作符。

所以， 有时候在我们写下如 AAA = XXX， 这样的代码， 且恰好XXX的类型正好是AAA单参数构造器的参数类型， 这时候编译器就自动调用这个构造器， 创建一个AAA的对象。

这样看起来好象很酷， 很方便。 但在某些情况下， 却违背了程序员的本意。 这时候就要在这个构造器前面加上explicit修饰， 指定这个构造器只能被明确的调用/使用， 不能作为类型转换操作符被隐含的使用;

在C++中，我们有时可以将构造函数用作自动类型转换函数。但这种自动特性并非总是合乎要求的，有时会导致意外的类型转换，因此，C++新增了关键字explicit，用于关闭这种自动特性。即被explicit关键字修饰的类构造函数，不能进行自动地隐式类型转换，只能显式地进行类型转换。

注意：只有一个参数的构造函数，或者构造函数有n个参数，但有n-1个参数提供了默认值，这样的情况才能进行类型转换。

下面通过一段代码演示具体应用（无explicit情形）：

```C++
/* 示例代码1 */
class Demo
{
   public:
    Demo();    　　　　　　　　　　　　　　   /* 构造函数1 */
    Demo(double a);　　　　　　　　　　　　  /* 示例代码2 */
    Demo(int a,double b);　　　　　　　　   /* 示例代码3 */
    Demo(int a,int b=10,double c=1.6);　　/* 示例代码4 */
    ~Demo();
    void Func(void);

    private:
    int value1;
    int value2;
};
```

上述四种构造函数：

构造函数1没有参数，无法进行类型转换！

构造函数2有一个参数，可以进行类型转换，如：Demo test; test = 12.2;这样的调用就相当于把12.2隐式转换为Demo类型。

构造函数3有两个参数，且无默认值，故无法使用类型转换！

构造函数4有3个参数，其中两个参数有默认值，故可以进行隐式转换，如：Demo test;test = 10; 。

 

下面讲述使用了关键字explicit的情况：

```C++
1 /* 示例代码2 */
 2 class Demo
 3 {
 4    public:
 5     Demo();    　　　　　　　　　　　　　　   /* 构造函数1 */
 6     explicit Demo(double a);　　　　　　　 /* 示例代码2 */
 7     Demo(int a,double b);　　　　　　　　   /* 示例代码3 */
 8
 9     ~Demo();
10     void Func(void);
11
12     private:
13     int value1;
14     int value2;
15 };
```

在上述构造函数2中，由于使用了explicit关键字，则无法进行隐式转换。即：Demo test;test = 12.2;是无效的！但是我们可以进行显示类型转换，如：

Demo test;

test = Demo(12.2); 或者

test = (Demo)12.2;

### 9.string和char*

**一、定义**

string：string可以被看成是以字符为元素的一种容器。字符构成序列（字符串）。有时候在字符序列中进行遍历，标准的string类提供了STL容器接口。具有一些成员函数比如begin()、end()，迭代器可以根据他们进行定位。与char*不同的是，string不一定以NULL('\0')结束。string长度可以根据length()得到，string可以根据下标访问。所以，不能将string直接赋值给char*。

char*: char *是一个指针，可以指向一个字符串数组，至于这个数组可以在栈上分配，也可以在堆上分配，堆得话就要你手动释放了。

**二、区别主要**
string的内存管理是由系统处理，除非系统内存池用完，不然不会出现这种内存问题。
char *的内存管理由用户自己处理，很容易出现内存不足的问题。

当我们要存一个串，但是不知道其他需要多少内存时， 用string来处理就最好不过了。
当你知道了存储的内存的时候，可以用char *，但是不如用string的好，用指针总会有
隐患。

用string还可以使用各种成员函数来处理串的每一个字符，方便处理。
用char *处理串，就不如string的方便了，没有相应的函数来直接调用，而是要自己编
写函数来完成串的处理，而且处理过程中用指针还很容易出现内存问题。

char *s="string"的内容是不可以改的；char s[10]="string"的内容是可以改的

**三、相互转化**

1、string 转换成 char *

如果要将string直接转换成const char *类型。string有2个函数可以运用。

一个是.c_str()，一个是data成员函数。

例子如下：

string s1 = "abcdeg";

const char *k = s1.c_str();
const char *t = s1.data();

printf("%s%s",k,t);
cout<<k<<t<<endl;

如上，都可以输出。内容是一样的。但是只能转换成const char*，如果去掉const编译不能通过。

那么，如果要转换成char*，可以用string的一个成员函数copy实现。

string s1 = "abcdefg";

char *data;
int len = s1.length();

data = (char *)malloc((len+1)*sizeof(char));
s1.copy(data,len,0);

printf("%s",data);
cout<<data;

2、char *转换成string

可以直接赋值。

string s;

char *p = "adghrtyh";

s = p;

不过这个是会出现问题的。

有一种情况我要说明一下。当我们定义了一个string类型之后，用printf("%s",s1);输出是会出问题的。这是因为“%s”要求后面的对象的首地址。但是string不是这样的一个类型。所以肯定出错。

用cout输出是没有问题的，若一定要printf输出。那么可以这样：

printf("%s",s1.c_str())

3、char[] 转换成string
这个也可以直接赋值。但是也会出现上面的问题。需要同样的处理。



4、string转换成char[]
这个由于我们知道string的长度，可以根据length()函数得到，又可以根据下标直接访问，所以用一个循环就可以赋值了。

这样的转换不可直接赋值。

string pp = "dagah";

char p[8];
int i;

for( i=0;i<pp.length();i++)
p[i] = pp[i];

p[i] = '\0';
printf("%s\n",p);

cout<<p;

### 4.读取文件

#### 4.1 fscanf()

C标准库:`#include <stdio.h>`

**描述**

C 库函数 **int fscanf(FILE \*stream, const char \*format, ...)** 从流 stream 读取格式化输入。

**声明**

下面是 fscanf() 函数的声明。

```
int fscanf(FILE *stream, const char *format, ...)
```

**参数**

+ **stream** -- 这是指向 FILE 对象的指针，该 FILE 对象标识了流。
+ **format** -- 这是 C 字符串，包含了以下各项中的一个或多个：*空格字符、非空格字符* 和 *format 说明符*。
  format 说明符形式为 **[=%[\*][width][modifiers]type=]**，具体讲解如下：

| 参数      | 描述                                                         |
| :-------- | :----------------------------------------------------------- |
| *         | 这是一个可选的星号，表示数据是从流 stream 中读取的，但是可以被忽视，即它不存储在对应的参数中。 |
| width     | 这指定了在当前读取操作中读取的最大字符数。                   |
| modifiers | 为对应的附加参数所指向的数据指定一个不同于整型（针对 d、i 和 n）、无符号整型（针对 o、u 和 x）或浮点型（针对 e、f 和 g）的大小： h ：短整型（针对 d、i 和 n），或无符号短整型（针对 o、u 和 x） l ：长整型（针对 d、i 和 n），或无符号长整型（针对 o、u 和 x），或双精度型（针对 e、f 和 g） L ：长双精度型（针对 e、f 和 g） |
| type      | 一个字符，指定了要被读取的数据类型以及数据读取方式。具体参见下一个表格。 |

**fscanf 类型说明符：**

| 类型      | 合格的输入                                                   | 参数的类型     |
| :-------- | :----------------------------------------------------------- | :------------- |
| c         | 单个字符：读取下一个字符。如果指定了一个不为 1 的宽度 width，函数会读取 width 个字符，并通过参数传递，把它们存储在数组中连续位置。在末尾不会追加空字符。 | char *         |
| d         | 十进制整数：数字前面的 + 或 - 号是可选的。                   | int *          |
| e,E,f,g,G | 浮点数：包含了一个小数点、一个可选的前置符号 + 或 -、一个可选的后置字符 e 或 E，以及一个十进制数字。两个有效的实例 -732.103 和 7.12e4 | float *        |
| o         | 八进制整数。                                                 | int *          |
| s         | 字符串。这将读取连续字符，直到遇到一个空格字符（空格字符可以是空白、换行和制表符）。 | char *         |
| u         | 无符号的十进制整数。                                         | unsigned int * |
| x,X       | 十六进制整数。                                               | int *          |

+ **附加参数** -- 根据不同的 format 字符串，函数可能需要一系列的附加参数，每个参数包含了一个要被插入的值，替换了 format 参数中指定的每个 % 标签。参数的个数应与 % 标签的个数相同。

#### 4.2返回值

如果成功，该函数返回成功匹配和赋值的个数。如果到达文件末尾或发生读错误，则返回 EOF。

**实例**

下面的实例演示了 fscanf() 函数的用法。

```
#include <stdio.h>
#include <stdlib.h>


int main()
{
   char str1[10], str2[10], str3[10];
   int year;
   FILE * fp;

   fp = fopen ("file.txt", "w+");
   fputs("We are in 2014", fp);
   
   rewind(fp);
   fscanf(fp, "%s %s %s %d", str1, str2, str3, &year);
   
   printf("Read String1 |%s|\n", str1 );
   printf("Read String2 |%s|\n", str2 );
   printf("Read String3 |%s|\n", str3 );
   printf("Read Integer |%d|\n", year );

   fclose(fp);
   
   return(0);
}
```

让我们编译并运行上面的程序，这将产生以下结果：

```
Read String1 |We|
Read String2 |are|
Read String3 |in|
Read Integer |2014|
```

### 5.`extern`的用法

在A.h中有一个全局变量

int a;

如果在B.cpp中想要用它,有两种方法：

1. 在B.cpp中`#include A.h`
2. 在B.cpp中 `extern in a;`