https://www.cnblogs.com/Allen-rg/p/13922965.html

# while(cin)说明

下面谈到的输入问题其实都建议用==while(cin)==的形式，最主要的原因是由于牛客网在线编程和实际考试的系统中测试代码用的都是==多组测试用例==，如果不用while(cin)的形式或许某些情况下会正确，但是用的话基本上都能保证测试的正确性。

其实这样也是很正常的，因为代码已经编译运行过了一次，直接在该次运行中进行多组测试即可，无需多次重复编译运行。

# 输入

几乎所有的输入都是数字或者字符串，根据数字和字符串可以将输入分为几种情况：

## 0.基础知识cin的用法

### **1. cin 简介**

cin 是 C++ 标准输入流对象，即 istream 类的对象。cin 主要用于从标准输入读取数据，这里的标准输入指终端键盘。

此外，cout 是标准输出流对象，即 ostream 类的对象。cerr 是标准错误输出流对象，也是 ostream 类的对象。

这里的**标准输入指终端键盘，标准错误输出指终端屏幕**。

在理解 cin 功能时，不得不提**标准输入缓冲区**。

当我们从键盘输入字符串的时候**需要敲一下回车键才能够将这个字符串送入到缓冲区中**，那么敲入的这个回车键（\r）会被转换为一个换行符（\n），这个换行符也会被存储在 cin 的缓冲区中并且被当成一个字符来计算！

比如我们在键盘上敲下了 123456 这个字符串，然后敲一下回车键（\r）将这个字符串送入了缓冲区中，那么此时缓冲区中的字节个数是 7 ，而不是 6。

**cin 读取数据也是从缓冲区中获取数据**，缓冲区为空时，cin 的成员函数会阻塞等待数据的到来，一旦缓冲区中有数据，就触发 cin 的成员函数去读取数据。

### 2. cin 常用输入方法

使用 cin 从标准输入读取数据时，通常用到的方法有 **cin>>**、**cin.get()**，**cin.getline()**。

#### 2.1 cin>> 的用法

cin 可以连续从键盘读取想要的数据，**以空格、tab 或换行作为分隔符**。实例如下。

```C++
#include <iostream>
using namespace std;

int main() {
	char a;
	int b;
	float c;
	cin>>a>>b>>c;
	cout<<a<<" "<<b<<" "<<c<<" "<<endl;
	return 0;
}
```

在屏幕中一次输入：a[回车]11[回车]5.56[回车]，程序将输出如下结果：

```
a
11
5.56

a 11 5.56
```

（1）cin>> 等价于 cin.operator>>()，即调用成员函数 operator>>() 进行读取数据。
（2）当 cin>> 从缓冲区中读取数据时，若缓冲区中第一个字符是空格、tab或换行这些分隔符时，cin>> 会将其忽略并清除，继续读取下一个字符，若缓冲区为空，则继续等待。但是如果读取成功，字符后面的分隔符是残留在缓冲区的，cin>> 不做处理。
（3）不想略过空白字符，那就使用 noskipws 流控制。比如 cin>>noskipws>>input;

验证程序如下：

```C++
#include <string> 
#include <iostream>

using namespace std;

int main() {
	char a;
	int b;
	float c;
	string str;
	cin>>a>>b>>c>>str;
	cout<<a<<" "<<b<<" "<<c<<" "<<str<<endl;

	string test;
	getline(cin,test);	//不阻塞
	cout<<"test:"<<test<<endl;
	return 0;

}
```


从键盘输入:[回车][回车][回车]a[回车]5[回车]2.33[回车]hello[回车]，输出结果是：

![这里写图片描述](DSA_输入输出.assets/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTUwOTA0MjEzNTU0MzM4)

从结果可以看出，cin>> 对缓冲区中的第一个换行符视而不见，采取的措施是忽略清除，继续阻塞等待缓冲区有效数据的到来。但是，getline() 读取数据时，并非像 cin>> 那样忽略第一个换行符，getline() 发现 cin 的缓冲区中有一个残留的换行符，不阻塞请求键盘输入，直接读取，送入目标字符串后，因为读取的内容为空，所以程序中的变量 test 为空串。

#### 2.2 cin.get() 的用法

该函数有多种重载形式，分为四种格式：无参，一参数，二参数，三个参数。常用的的函数原型如下：

```C++
int get();
istream& get(char& var);
istream& get( char* s, streamsize n );
istream& get( char* s,  streamsize  n, char delim);
```


其中 streamsize 在 VC++ 中被定义为 long long 型。另外，还有两个重载形式不怎么使用，就不详述了，函数原型如下：

istream& get (streambuf& sb);
istream& get (streambuf& sb, char delim);


##### 2.2.1 cin.get() 读取一个字符

读取一个字符，可以使用 cin.get() 或者 cin.get(var)，示例代码如下：

```C++
#include <iostream>

using namespace std;

int main() {
	char a;
	char b;
	a=cin.get();
	cin.get(b);
	cout << a << b <<endl;
	return 0;
}
```


输入：e[回车]，输出：

![这里写图片描述](DSA_输入输出.assets/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTUwOTA0MjEzNzAwMzA2)

（1）从结果可以看出，cin.get() 从输入缓冲区读取单个字符时不忽略分隔符，直接将其读取，就出现了如上情况，将换行符读入变量 b，输出时换行两次，一次是变量 b，一次是 endl。
（2）cin.get() 的返回值是 int 类型，成功则返回读取字符的 ASCII 码值。
（3）cin.get(char var) 如果成功返回的是 cin 对象，因此支持链式操作，如cin.get(b).get(c)。

##### 2.2.2 cin.get() 读取一行

读取一行可以使用：

```C++
istream& get(char* s, streamsize n)
istream& get(char* s, size_t n, streamsize  delim)
```


二者的区别是前者默认以换行符结束，后者可指定行结束符。n 表示目标空间的大小。

二者的区别是前者默认以换行符结束，后者可指定行结束符。n 表示目标空间的大小。

示例代码如下：

```C++
#include <iostream>

using namespace std;

int main() {
	char a;
	char array[20]={NULL}; 
	cin.get(array,20);
	cin.get(a);
	cout<<array<<" "<<(int)a<<endl;
	return 0;
}
```


输入：123456789[回车]，输出：

```
123456789
123456789 10
```


（1）从结果可以看出，cin.get(array,20);读取一行时，遇到换行符时结束读取，但是不对换行符进行处理，换行符仍然残留在输入缓冲区。第二次由 cin.get() 将换行符读入变量 b，打印输入换行符的 ASCII 码值 10。这也是 cin.get() 读取一行与使用 cin.getline 读取一行的区别所在。cin.getline 读取一行字符时，默认遇到 ‘\n’ 时终止，并且将 ‘\n’ 直接从输入缓冲区中删除掉，不会影响下面的输入处理。

（1）从结果可以看出，cin.get(array,20);读取一行时，遇到换行符时结束读取，但是不对换行符进行处理，换行符仍然残留在输入缓冲区。第二次由 cin.get() 将换行符读入变量 b，打印输入换行符的 ASCII 码值 10。这也是 cin.get() 读取一行与使用 cin.getline 读取一行的区别所在。cin.getline 读取一行字符时，默认遇到 ‘\n’ 时终止，并且将 ‘\n’ 直接从输入缓冲区中删除掉，不会影响下面的输入处理。

（2）cin.get(str,size); 读取一行时，只能将字符串读入 C 风格的字符串中，即 char*，但是 cin.getline() 函数可以将字符串读入C++ 风格的字符串 string中。鉴于 cin.getline() 较 cin.get() 的这两种优点，建议使用 cin.getline() 读取行。

#### 2.3 cin.getline() 读取一行

函数作用：从标准输入设备键盘读取一串字符串，并以指定的结束符结束。
函数原型有两个：

```C++
istream& getline(char* s, streamsize count); //默认以换行符结束
istream& getline(char* s, streamsize count, char delim);
```


使用示例：

```C++
#include <iostream>

using namespace std;

int main() {
	char array[20]={NULL};
	cin.getline(array,20); //或者指定结束符，使用下面一行
	//cin.getline(array,20,'\n');
	cout<<array<<endl;
	return 0;
}
```


注意，cin.getline() 与 cin.get() 的区别是，cin.getline() 不会将行结束符（如换行符）残留在输入缓冲区中。

### 3.cin 的条件状态

使用 cin 读取键盘输入时，难免发生错误，一旦出错，cin 将设置条件状态(condition state)。条件状态位有：

goodbit(0x0)：无错误
eofbit(0x1)：已到达文件尾
failbit(0x2)：非致命的输入/输出错误，可挽回
badbit(0x4)：致命的输入/输出错误，无法挽回

与这些条件状态对应的就是设置、读取和判断条件状态的流对象的成员函数。它们主要有：

s.eof()：若流 s 的 eofbit 置位，则返回 true
s.fail()：若流 s 的 failbit 置位，则返回 true
s.bad()：若流 s 的 badbit 置位，则返回 true
s.good()：若流 s 的 goodbit 置位，则返回 true
s.clear(flags)：清空当前状态, 然后把状态设置为 flags，返回 void
s.setstate(flags)：不清空当前状态，设置给定的状态 flags，返回 void
s.rdstate()：返回流 s 的当前条件状态，返回值类型为 ios_base::iostate

了解以上关于输入流的条件状态与相关操作函数，下面看一个因输入缓冲区未读取完造成的状态位 failbit 被置位，再通过 clear()复位的例子。

```C++
#include <iostream>

using namespace std;

int main() {
	char ch, str[20]; 
	cin.getline(str, 5);
	cout<<"goodbit:"<<cin.good()<<endl;    // 查看goodbit状态，即是否有异常
	cin.clear();                         // 清除错误标志
	cout<<"goodbit:"<<cin.good()<<endl;    // 清除标志后再查看异常状态
	cin>>ch; 
	cout<<"str:"<<str<<endl;
	cout<<"ch:"<<ch<<endl;
	return 0;
}
```


输入：12345[回车]，输出结果为：

输入：12345[回车]，输出结果为：

12345
goodbit:0
goodbit:1
str:1234
ch:5

可以看出，因输入缓冲区未读取完造成输入异常，通过 clear() 可以清除输入流对象cin的异常状态。，不影响后面的cin>>ch从输入缓冲区读取数据。因为cin.getline读取之后，输入缓冲区中残留的字符串是：5[换行]，所以 cin>>ch 将 5 读取并存入 ch，打印输入并输出 5。

如果将 clear() 注释，cin>>ch; 将读取失败，ch 为空。cin.clear() 等同于 cin.clear(ios::goodbit); 因为 cin.clear() 的默认参数是 ios::goodbit，所以不需显示传递，故而你最常看到的就是 cin.clear()。

### 4.cin 清空输入缓冲区

从上文中可以看出，上一次的输入操作很有可能是输入缓冲区中残留数据，影响下一次输入。那么如何解决这个问题呢？自然而然，我们想到了在进行输入时，对输入缓冲区进行清空和状态条件的复位。条件状态的复位使用 clear()，清空输入缓冲区应该使用 cin.ignore()。

函数原型：

```
istream &ignore(streamsize num=1, int delim=EOF);
```


函数作用：跳过输入流中 n 个字符，或在遇到指定的终止字符时提前结束（此时跳过包括终止字符在内的若干字符）。

使用示例如下：

```C++
#include <iostream>

using namespace std;

int main() {
	char str1[20] = {NULL}, str2[20] = {NULL};
    cin.getline(str1,5);
    cin.clear();  // 清除错误标志
	cin.ignore(numeric_limits<std::streamsize>::max(),'\n'); // 清除缓冲区的当前行
	cin.getline(str2,20);
	cout << "str1:" << str1 << endl;
	cout << "str2:" << str2 << endl;
	return 0;
}
```


程序输入：12345[回车]success[回车]，程序输出：

12345
success
str1:1234
str2:success
1
2
3
4
（1）程序中使用 cin.ignore 清空了输入缓冲区的当前行，使上次的输入残留下的数据没有影响到下一次的输入，这就是 ignore() 函数的主要作用。其中，numeric_limits<std::streamsize>::max()是<limits>头文件定义的流使用的最大值，你也可以用一个足够大的整数代替它。如果想清空输入缓冲区的所有内容，去掉换行符即可：

cin.ignore(numeric_limits< std::streamsize>::max());
1
这里要注意的是，如果缓冲区中没有 EOF（-1），cin.ignore() 会阻塞等待。如果在命令行，我们可以使用 Ctrl+Z 然后回车（Windows 命令行）或直接 Ctrl+D（Linux 命令行）输入 EOF。

（2）cin.ignore()；当输入缓冲区没有数据时，也会阻塞等待数据的到来。

（3）请不要使用 cin.sync() 来清空输入缓冲区，本人测试了一下，VC++ 和 GNU C++ 都不行，请使用 cin.ignore()。

### 5.从标准输入读取一行字符串的其它方法

5.1 getline() 读取一行
C++ 中定义了一个在 std 名字空间的全局函数 getline()，因为这个 getline() 函数的参数使用了 string 字符串，所以声明在了<string>头文件中了。

getline() 利用 cin 可以从标准输入设备键盘读取一行，当遇到如下三种情况会结束读操作：
（1）文件结束；
（2）遇到行分隔符；
（3）输入达到最大限度。

函数两个重载形式：

istream& getline (istream& is, string& str);						//默认以换行符\n分隔行
istream& getline (istream& is, string& str, char delim);
1
2
使用示例：

```C++
#include <string> 

#include <iostream>

using namespace std;

int main() {
	string str;
	getline(cin,str);
	cout << str << endl;
	return 0;
}
```


输入：hello world[回车]，输出：

hello world
1
注意，getline() 遇到结束符时，会将结束符一并读入指定的 string 中，再将结束符替换为空字符。因此，进行从键盘读取一行字符时，建议使用 getline，较为安全。但是，最好还是要进行标准输入的安全检查，提高程序容错能力。

cin.getline() 与 getline() 类似，但是因为 cin.getline() 的输出是char*，getline() 的输出是 string，所以 cin.getline() 属于 istream 流，而 getline() 属于 string 流，二者是不一样的函数。

5.2 gets() 读取一行
gets() 是 C 中的库函数，在头文件 <stdio.h> 申明，从标准输入设备读字符串，可以无限读取，不会判断上限，以回车或者文件结束符 EOF（ 即 -1） 结束，所以程序员应该确保 buffer 的空间足够大，以便在执行读操作时不发生溢出。Windows 下命令行输入文件结束符 EOF 的方式为 Ctrl+z，Linux 为 Ctrl+d。

函数原型：

char *gets(char *buffer);
1
使用示例：

```C++
#include <iostream>

using namespace std;
int main() {
	char array[20]={NULL};
	gets(array);
	cout << array << endl;
	return 0;
}
```


输入：I am lvlv[回车]，输出：

I am lvlv
1
由于该函数是 C 的库函数，所以不建议使用，既然是 C++ 程序，就尽量使用 C++ 的库函数吧。

另外，由于 gets() 实现不够安全，容易出现缓冲越界，用 fgets() 和 getline() 替代更好。gets() 在C++11中极不推荐使用，并在 C++14 中丢弃了该方法，参考链接：here。

### 6.小结

从标准输入读取内容，方法多样，在不同场景下按需取用。如果是 C++ 程序，建议尽量使用 C++ 的库函数，在使用上会更加简便安全。

## 1.数字

通常给定一组数，或者给定一个数组

### 1.1 直接输入一个数字

直接输入一个数字，对该数字进行一些操作，例如判断是否是素数，立方根等，这种类型只需要输入一个数即可，有以下几种输入：

```C++
int N; // 先定义一个输入变量用于接收系统输入的数字

// method 1
cin >> N;
// method 2
while(cin >> N) {
    // 方式二，将输入放在while后面，这种方式推荐大家用，
    // 因为C++的输入是流的方式，因此用while来判断接收是比较常用的方法，
    // 不容易出错（一些情况下只能用这种输入，所以推荐用这种，包括后面的字符串）
}
```

### 1.2 给定一个数，表示有多少组数（可能是字符和数字的组合）

```C++
// 举个例子，输入一个数N表示有多少个学生，然后输入每个学生的姓名和学号，要求按学号降序打印每个学生的信息，例子如下：
// 输入：3
// liming,1410
// zhangsan,1562
// lisi,1355
// 输出：
// zhangsan,1562
// liming,1410
// lisi,1355
// 对于上例，输入一般采用以下的方法
int N;//学生总数
while(cin>>N){//while里面输入总数，然后在该循环里面处理
    for(int i=0;i<N;i++){//用for循环输入N组数据
        cin>>stu[i].name>>stu[i].num;//输入姓名和学号
    }
}
//也可以不用while循环（不推荐，除非用while不好处理或者处理不了）
int N;
cin>>N;
for(int i=0;i<N;i++){//用for循环输入N组数据
    cin>>stu[i].name>>stu[i].num;//输入姓名和学号
}
// 这种方式看似很直观易懂，但这种方式对于系统里的测试用例来说可能不太友好，
// 一般C++的输入都强调流的概念，这种方式只能某些情况下可以使用，
// 大家可以自己多刷一刷，就会发现这种方式经常会遇到莫名其妙的错误
```

### 1.3 可能直接就是要求输入一组数，并不告知具体的数量

**可能直接就是要求输入一组数，并不告知具体的数量，以（2）的例子为例，如果不告诉你多少个学生，你就无法根据学生数量用for循环输入了，这时候用while循环就可以很好地处理（流的处理模式）**

```C++
// 输入:
// liming,1410
// zhangsan,1562
// lisi,1355
string name;//定义姓名变量
int num;//定义学号变量
while(cin>>name>>num){//输入一组，处理一组
    student s = {name,num};
    Input.push_back(s);//用一个结构体数组来接收输入的学生信息即可
    //....
}
```

## 2.字符串

### 2.1 给定字符串，进行相关处理 

```C++
//给定字符
char ch;
cin>>ch;//方式一

while(cin.get(ch)) {
	//方式二
}
//给定字符串
string input;
getline(cin,input);//方式一
cin>>input;//方式二

while(方式一/方式二)//方式三
//还是推荐用方式三的输入方式，不容易出错，字符串的一些题目用方式一和方式二可能会出错（由于输入格式的问题）
while(cin>> ch) {}
while(cin>>input) {}
```

### 2.2 给定不止一组字符串,告知大小

```C++
//举个例子，先输入一个数表示有多少个字符串，再输入每个字符串，根据字符串长度排序
int N;//定义数量
string temp;//字符串变量
while(cin>>N){//输入数量
    vector<string> input;//存储所有的字符串
    for(int i=0;i<N;i++){
        cin>>temp;//输入字符串
        input.push_back(temp);//保存
    }
}
//下面是另一种不安全的写法
int N;//定义数量
cin>>N;//输入数量
string temp;//字符串变量
vector<string> input;//存储所有的字符串
for(int i=0;i<N;i++){
    cin>>temp;//输入字符串
    input.push_back(temp);//保存
}
//以该例题为例，可能在本地IDE能得到正确答案，但如果是牛客网系统，由于输入格式的问题，这样写编译会通过，就是得不到正确答案
```

### 2.3 给定不止一组字符串,不告知大小

**输入一组字符串，不告知大小，仍然以上个例子举例，不告诉你有多少字符串，这样就不能根据数量来用for循环做了，但可以用while循环处理**

```C++
string temp;//字符串变量
vector<string> input;//存储所有的字符串
while(cin>>temp){//输入数量
    input.push_back(temp);//保存
    //...
}
```

# 输出

C++的输出cout与输入cin一样都是用流来控制的，cin和cout都在iostream这个头文件中，命名空间为std，因此使用的时候都要加上头文件和命名空间。

输出相对来说简单一些，不会出现输入的一些问题，相反，cout输出有时候还可以帮助你解决题目，一下列举一些常见输出语句：

```C++
//输入一个数字再输出
int num;//定义
cin>>num;//输入
cout<<num<<endl;//输出并且换行
 
//输入一个字符串再输出
string input;//定义
cin>>input;//输入
cout<<input<<endl;//输出并且换行
 
//输入输出多个数据
int num1,num2;
string s1,s2;
cin>>num1>>s1;
cin>>num2>>s2;
cout<<num1<<s1<<' '<<num2<<s2<<endl;

```

