[TOC]

# OpenCV_Base

## 0. 安装opencv



 OpenCV 中的一些基础操作
 1.相关数据结构,Mat,Scalar,Vec
 2.Mat操作
 3.ROI操作,取某行某列之类...
 4.获得程序运行时间,生成随机数
 5.矩阵运算

```C++
#include <iostream>
#include "opencv2/opencv.hpp"
#include "opencv2/highgui.hpp"

using namespace cv;
```

## 1.OpenCV基本数据结构

### 1.0 type

#### 1.0.1

float: 4字节，6-7位有效数字 -3.4E-38 到 3.4E38  
double: 8字节，15~16位有效数字 -1.7E-308 到 1.7E308

  在OpenCV里面，许多数据结构为了达到內存使用的最优化，通常都会用它最小上限的空间来分配变量，有的数据结构也会因为图像文件格式的关系而给予适当的变量，因此需要知道它们声明的空间大小来配置适当的变量。一 般标准的图片，为RGB格式它们的大小为8bits格式，范围为0~255,对一个int空间的类型来说实在是太小，整整浪费了24bits的空间,假设有个640*480的BMP文件空间存储內存,那整整浪费了640*480*3*(32-8)bits的內存空间,总共浪费了2.6MB!，也就是那 2.6MB内什么东西都没存储，如果今天以8bits的格式来存储则只使用到0.6MB的內存而已(640*480*3*(8)+54 bits)，因此，对于文件格式的对应是一件很重要的事。
  在这边除了要考虑bits的空间大小外，还要考虑使用类型的正负号的问题，一般的图像文件是不存在负号的，如果今天即使选则正确的空间大小，可是出现的结果却是负的，那就功亏一篑了。这里除了Float及double类型，char，int,short int都是用二的补数表示法,它们不具正负号bit,而Float,double则是用IEEE 754,在第32bit,64bit上有一个正负号bit.



1.**Unsigned 8bits**(一般的图像文件格式使用的大小)
CvMat数据结构参数：CV_8UC1，CV_8UC2，CV_8UC3，CV_8UC4

| 变量类型      | 空间大小 | 范围  | 其他                               |
| :------------ | :------- | :---- | :--------------------------------- |
| uchar         | 8bits    | 0~255 | (OpenCV缺省变量,同等unsigned char) |
| unsigned char | 8bits    | 0~255 |                                    |



2.**Signed 8bits**
CvMat数据结构参数：CV_8SC1，CV_8SC2，CV_8SC3，CV_8SC4

| 变量类型 | 空间大小 | 范围     | 其他 |
| :------- | :------- | :------- | :--- |
| char     | 8bits    | -128~127 |      |



3.**Unsigned 16bits**
IplImage数据结构参数：IPL_DEPTH_16U
CvMat数据结构参数：CV_16UC1，CV_16UC2，CV_16UC3，CV_16UC4

| 变量类型           | 空间大小 | 范围    | 其他                                    |
| :----------------- | :------- | :------ | :-------------------------------------- |
| ushort             | 16bits   | 0~65535 | (OpenCV缺省变量,同等unsigned short int) |
| unsigned short int | 16bits   | 0~65535 | (unsigned short)                        |



4.**Signed 16bits**
CvMat数据结构参数：CV_16SC1，CV_16SC2，CV_16SC3，CV_16SC4

| 变量类型  | 空间大小 | 范围         | 其他    |
| :-------- | :------- | :----------- | :------ |
| short int | 16bits   | -32768~32767 | (short) |

5.**Signed 32bits**
CvMat数据结构参数：CV_32SC1，CV_32SC2，CV_32SC3，CV_32SC4

| 变量类型 | 空间大小 | 范围                   | 其他   |
| :------- | :------- | :--------------------- | :----- |
| int      | 32bits   | -2147483648~2147483647 | (long) |

6.**Float 32bits**

IplImage数据结构参数：IPL_DEPTH_32F
CvMat数据结构参数：CV_32FC1，CV_32FC2，CV_32FC3，CV_32FC4

| 变量类型 | 空间大小 | 范围                 | 其他 |
| :------- | :------- | :------------------- | :--- |
| float    | 32bits   | 1.18*10-38~3.40*1038 |      |

7.**Double 64bits**

CvMat数据结构参数：CV_64FC1，CV_64FC2，CV_64FC3，CV_64FC4

| 变量类型 | 空间大小 | 范围                   | 其他 |
| :------- | :------- | :--------------------- | :--- |
| double   | 64bits   | 2.23*10-308~1.79*10308 |      |

8.**Unsigned 1bit**

IplImage数据结构参数：IPL_DEPTH_1U

| 变量类型 | 空间大小 | 范围 | 其他 |
| :------- | :------- | :--- | :--- |
| bool     | 1bit     | 0~1  |      |

#### 1.0.2

**数据类型及其取值范围**

| 数值   | 具体类型        | 取值范围                          |
| ------ | --------------- | --------------------------------- |
| CV_8U  | 8 位无符号整数  | （0…..255）                       |
| CV_8S  | 8 位符号整数    | （-128…..127）                    |
| CV_16U | 16 位无符号整数 | （0……65535）                      |
| CV_16S | 16 位符号整数   | （-32768…..32767）                |
| CV_32S | 32 位符号整数   | （-2147483648……2147483647）       |
| CV_32F | 32 位浮点数     | （-FLT_MAX ………FLT_MAX，INF，NAN)  |
| CV_64F | 64 位浮点数     | （-DBL_MAX ……….DBL_MAX，INF，NAN) |



在以下两个场景中使用  OpenCV 时，我们必须事先知道矩阵元素的数据类型：

- 使用 `at` 方法访问数据元素的时候要指明数据类型
- 做数值运算的时候，比如究竟是整数除法还是浮点数除法。

但面对一大堆代码，我们有时并不清楚当前的矩阵元素究竟是什么类型，这篇文章就是以 `cv::Mat` 类为例来解决这个问题。

cv::Mat 类的对象有一个成员函数 `type()` 用来返回矩阵元素的数据类型，返回值是 `int` 类型，不同的返回值代表不同的类型。OpenCV Reference Manual 中对 `type()` 的解释如下所示：

> **Mat::type**
>  `C++: int Mat::type() const`
>  The method returns a matrix element type. This is an identifier compatible with the CvMat type system, like CV_16SC3 or 16-bit signed 3-channel array, and so on.

实际的代码如下所示：

```cpp
cv::Mat haha = cv::Mat::zeros(3,3,CV_64F);
int hahaType = haha.type();
std::cout<<"hahaType = "<<hahaType<<std::endl;
```

至此，知道了 `type()` 函数，下一步更关键的就是返回值和具体类型之间的对应关系了。文章《[LIST OF MAT TYPE IN OPENCV][LIST OF MAT TYPE IN OPENCV]》对此整理得非常清楚，具体如下表所示：

|        | C1   | C2   | C3   | C4   |
| ------ | ---- | ---- | ---- | ---- |
| CV_8U  | 0    | 8    | 16   | 24   |
| CV_8S  | 1    | 9    | 17   | 25   |
| CV_16U | 2    | 10   | 18   | 26   |
| CV_16S | 3    | 11   | 19   | 27   |
| CV_32S | 4    | 12   | 20   | 28   |
| CV_32F | 5    | 13   | 21   | 29   |
| CV_64F | 6    | 14   | 22   | 30   |

表头的 C1, C2, C3, C4 指的是通道(Channel)数，比如灰度图像只有 1 个通道，是 C1；JPEG格式 的 RGB 彩色图像就是 3 个通道，是 C3；PNG 格式的彩色图像除了 RGB 3个通道外，还有一个透明度通道，所以是 C4。大家还会发现 7 怎么没有被定义类型，这个可以看 [OpenCV 源码](https://link.jianshu.com?t=[https://github.com/Itseez/opencv/blob/master/modules/core/include/opencv2/core/cvdef.h](https://github.com/Itseez/opencv/blob/master/modules/core/include/opencv2/core/cvdef.h))，有如下所示的一行，说明 7 是用来给用户自定义的：



```cpp
#define CV_USRTYPE1 7
```

如果仅仅是为了在数值计算前明确数据类型，那么看到这里就可以了；如果是要使用 `at` 方法访问数据元素，那么还需要下面一步。因为以单通道为例，`at` 方法接受的是 `uchar` 这样的数据类型，而非 `CV_8U`。在已知通道数和每个通道数据类型的情况下，指定给 `at` 方法的数据类型如下表所示：

|        | C1       | C2          | C3          | C4          | C6          |
| ------ | -------- | ----------- | ----------- | ----------- | ----------- |
| uchar  | `uchar`  | `cv::Vec2b` | `cv::Vec3b` | `cv::Vec4b` |             |
| short  | `short`  | `cv::Vec2s` | `cv::Vec3s` | `cv::Vec4s` |             |
| int    | `int`    | `cv::Vec2i` | `cv::Vec3i` | `cv::Vec4i` |             |
| float  | `float`  | `cv::Vec2f` | `cv::Vec3f` | `cv::Vec4f` | `cv::Vec6f` |
| double | `double` | `cv::Vec2d` | `cv::Vec3d` | `cv::Vec4d` | `cv::Vec6d` |

### 1.1 Scalar类

**Scalar类**   

short类型的向量
Scalar()表示有四个元素的数组，通常若只用到三个参数如， Scalar(a,b,c)，那第四个可以不写出来，
三个参数的顺序是BGR顺序，通常用来Mat类的初始化  CV_8UC3 里面的3表示三通道,就是BGR,
Scalar(a,b,c)也可以给CV_8UC1类型的Mat赋值,只有a传进去
Scalar::all(a):全a

### 1.2 Size类

**Size类**
Size(5,5)表示宽度和高度都是5, Size(4,6)指宽 4,高 6,即 6行4列
用来作为一些需要设置高度宽度的函数作为参数

**openCV关于宽高,xy容易混淆的地方:**

row == heigh == Point.y

col == width == Point.x

Mat::at(Point(x, y)) == Mat::at(y,x)

### 1.3 Vec类

```C++
/*Vec类型 小型向量  定义了不同类型的向量*/
//最常用的是Vec3b,三位无符号整数(0-255)
typedef Vec<uchar, 2> Vec2b;
typedef Vec<uchar, 3> Vec3b;
typedef Vec<uchar, 4> Vec4b;
typedef Vec<short, 2> Vec2s;
typedef Vec<short, 3> Vec3s;
typedef Vec<short, 4> Vec4s;
typedef Vec<int, 2> Vec2i;
typedef Vec<int, 3> Vec3i;
typedef Vec<int, 4> Vec4i;
typedef Vec<float, 2> Vec2f;
typedef Vec<float, 3> Vec3f;
typedef Vec<float, 4> Vec4f;
typedef Vec<float, 6> Vec6f;
typedef Vec<double, 2> Vec2d;
typedef Vec<double, 3> Vec3d;
typedef Vec<double, 4> Vec4d;
typedef Vec<double, 6> Vec6d;
```

```C++
void vec_test()
{
    Vec3b vec ={1,2,3};
    Vec3b color;
    color[0] = 255;
    color[1] = 0;
    color[2] = 0;

    cout << "vec3b..." << vec << endl; //[1, 2, 3]
}
```

### 1.4 Mat类

Mat类是OpenCV提供的矩阵类,可以进行很多矩阵操作

#### 1.4.1 Mat的创建

```C++
void mat_test()
{
    /**
     * type可以是 CV_8UC1， CV_16SC1， …， CV_64FC4 等。里面的 8U 表示 8 位无符号整数，
     * 16S 表示16 位有符号整数， 64F表示 64 位浮点数（即 double 类型),CV_32F也可表示浮点数(float)
     * C 后面的数表示通道数,例如 C1 表示一个通道的图像， C4 表示 4 个通道的图像，以此类推。
     * 如果需要更多的通道数，需要用宏 CV_8UC(n)
     */
    //Mat有很多种初始化方法 20种
    //Mat::Mat()
    //Mat(int rows, int cols, int type)
    /*
     * Mat::Mat(int rows, int cols, int type, const Scalar& s)
    创建行数为 rows，列数为 col，类型为 type 的图像，并将所有元素初
    始化为值 s；
    */
    //Mat::Mat(Size size, int type,const Scalar& s)
    //Mat::Mat(const Mat& m)   m 和新对象共用图像数据；
    //要想互不影响,使用Mat M2 = M1.clone();或者 Mat M2;  M1.copyto(M2);
    namedWindow("test",WINDOW_NORMAL);
    Mat M0();
    Vec3b vec ={1,2,3};
    Mat M1(6,4,CV_8UC3,vec);//Vec3b 也可以给Mat赋值
    Mat M2(6,4,CV_8UC1,Scalar(125));
    Mat M4(M2);
    uchar i = 1.4;//最后i=1,只能是整数,四舍五入
    /*
     *  typedef Size_<int> Size2i;
        typedef Size_<int64> Size2l;
        typedef Size_<float> Size2f;
        typedef Size_<double> Size2d;
        typedef Size2i Size;
    */
    Size size(40,60);//size(4,6)指宽 4,高 6,即 6行4列
    Mat M3(size,CV_8UC1,i);//直接uchar i = 0;也可以赋值//灰度图像可以这么赋值,还是用Scalar()方便一点
    //zeros()全0、 ones()全1、 eye()单位矩阵
    Mat M5=Mat::eye(4, 4, CV_8UC1);
    Mat M6(6,4,CV_32FC3,Scalar::all(1.5));//Scalar::all(1.5)全部设为1.5

    ///小型矩阵 直接输入 使用Mat_()类
    Mat small_mat = (Mat_<double>(3,3) << 0,-1,0,-1,5,6,1,4,7.1);

    cout << "M1 = \n" << M1 << endl;
    cout << "===========================Mat相关操作============================" << endl;
    //使用矩阵变量的at()方法来实现对一个像素点数据的读取和赋值
    float value1 = M6.at<float>(0,71);
    int value2 = (int)M2.at<uchar>(0,0);//想把uchar打印出来,要先转换成int
    cout << "M6.at<float>(0.0) = " << value1 << endl
         << "M2.at<uchar>(0,0) = " << value2 << endl;

    //imshow("test",M2);
    //waitKey(0);
    cout << "M6 = \n" << M6 << endl;
    M6.at<float>(0,0) = 100;
    M6.at<float>(1,12) = 100;//(1,12)第2行第一个开始数,往后第12个元素//按行存储?
    cout << "M6 = \n" << M6 << endl;
    //读取三通道矩阵  at<Vec3>
    cout << M6.at<Vec3f>(0,0)[0] << endl;
    cout << M6.at<Vec3f>(0,0)[1] << endl;
    cout << M6.at<Vec3f>(0,0)[2] << endl;
    //获取行 列 数目
    int m_rows = M6.rows;//6
    int m_cols = M6.cols;//4
    cout << "m_rows = " << m_rows << endl
         << "m_cols = " << m_cols << endl;
}
```

#### 1.4.2 Mat遍历方法

**Mat访问方法:**
*方法一:* 

`Mat.at<type>(row,col);       //type:  uchar,Vec3b等`

*方法二:*

指针,把每一行当做 一个数组
`type* p = Mat.ptr<type>(i)`  

返回Mat第i行 首像素 指针   p[j]  即为第i行第j列

*方法三:迭代器*

```C++
MatIterator_<type> it = Mat.begin<type>();
MatIterator_<type> it_end = Mat.end<type>();
for(int i = 0;it != it_end;it ++)
    *it
```

```C++
/*三种遍历方法*/
void mat_search()
{
    //at()
#if 1
    Mat grayim1(600,800,CV_8UC1);
    Mat colorim1(600,800,CV_8UC3);
    clock_t startTime,endTime;
    startTime = clock();         //计时开始
    auto start1 = system_clock::now();

    //遍历所有像素，并设置像素值
    for (int i = 0;i < grayim1.rows;i++)
        for(int j = 0;j < grayim1.cols;j++)
        {
            grayim1.at<uchar>(i,j) = (i + j) % 255;
        }
    //遍历所有像素，并设置像素值
    for (int i = 0;i < colorim1.rows;i++)
        for(int j = 0;j < colorim1.cols;j++)
        {
            Vec3b pixel;
            pixel[0] = i % 255;         //B
            pixel[1] = j % 255;         //G
            pixel[2] = (i+j)%255;       //R
            colorim1.at<Vec3b>(i,j) = pixel;
        }
    endTime = clock();          //计时结束
    auto end1 = system_clock::now();
    auto time1 = duration_cast<microseconds>(end1 - start1);
    cout << "The run time is " << (double)(endTime - startTime) / CLOCKS_PER_SEC * 1000 << "ms" << endl;
    cout << "The run time is " << double(time1.count())
            * microseconds::period::num / microseconds::period::den *1000  << "ms"<< endl;

    imshow("gratim1",grayim1);
    imshow("colorim1",colorim1);
    waitKey(0);
#endif
//使用迭代器遍历矩阵
#if 1
    Mat grayim2(600,800,CV_8UC1);
    Mat colorim2(600,800,CV_8UC3);
    //遍历所有像素，并设置像素值
    auto start2 = system_clock::now();
    MatIterator_<uchar> grayit,grayend;
    for(grayit = grayim2.begin<uchar>(),grayend = grayim2.end<uchar>();grayend!=grayit;grayit++)
    {
        *grayit = rand()%255;
    }
    //遍历所有像素，并设置像素值
    MatIterator_<Vec3b> colorit,colorend;
    for(colorit = colorim2.begin<Vec3b>(),colorend = colorim2.end<Vec3b>();colorend!=colorit;colorit++)
    {
        Vec3b color;
        color[0] = rand()%255;
        color[1] = rand()%255;
        color[2] = rand()%255;
        *colorit = color;
    }
    auto end2 = system_clock::now();
    auto duration2 = duration_cast<microseconds>(end2 - start2);
    double run_time2 = double(duration2.count()) / 1000;
    cout << "run time = " << run_time2 << endl;
    imshow("grayim2",grayim2);
    imshow("colorim2",colorim2);
    waitKey(0);
#endif
//使用指针来遍历
#if 1
    Mat grayim3(600,800,CV_8UC1);
    Mat colorim3(600,800,CV_8UC3);
    auto start3 = system_clock::now();
    for (int i = 0;i < grayim3.rows;i++)
    {
        uchar* p = grayim3.ptr<uchar>(i);
        for (int j = 0;j < grayim3.cols;j++)
        {
            p[j] = (i+j) % 255;
        }
    }
    for(int i = 0;i < colorim3.rows;i++)
    {
        Vec3b* p = colorim3.ptr<Vec3b>(i);
        for (int j = 0;j < colorim3.cols;j++)
        {
            p[j][0] = i % 255;
            p[j][1] = j % 255;
            p[j][2] = 0;
        }
    }
    auto end3 = system_clock::now();
    auto duration3 = duration_cast<microseconds>(end3 - start3);
    double run_time3 = double(duration3.count()) / 1000;
    cout << "run time = " << run_time3 << endl;
#endif
}
```

#### 1.4.3 Mat ROI选择

Mat类提供了很多选择局部区域的方法，

但是它们都是指向同一片数据区的，因此就算把局部区域赋值给新的Mat对象，其实还是指向同一个数据区,对这个ROI的操作会反应到原矩阵.

```C++
void mat_roi()
{
    Mat m1(4,4,CV_8UC1);
    Mat m2(4,4,CV_8UC3);
    for (int i = 0;i < m1.rows;i++)
    {
        uchar* p = m1.ptr<uchar>(i);
        for(int j = 0;j < m1.cols;j++)
        {
            p[j] = m1.cols * i + j;
        }
    }
    for (int i = 0;i < m2.rows;i++)
    {
        Vec3b* p = m2.ptr<Vec3b>(i);
        for (int j = 0;j < m2.cols;j++)
        {
            uchar pixel = m2.cols * i + j;
            p[j][0] = pixel;
            p[j][1] = pixel;
            p[j][2] = pixel;

        }
    }
    cout << "m1 = \n" << m1 << endl;
    cout << "m2 = \n" << m2 << endl;
    //取出矩阵的第 i 行(index = i - 1）
    Mat line1 = m1.row(0);
    Mat line2 = m2.row(0);
    cout << "m1(0) = \n" << line1 << endl;
    cout << "m2(0) = \n" << line2 << endl;
    //取出矩阵的第 i 列(index = i - 1）
    Mat col1 = m1.col(0);
    Mat col2 = m2.col(0);
    cout << "m1(0) = \n" << col1 << endl;
    cout << "m2(0) = \n" << col2 << endl;
    //取对角线
    /*
     * Mat Mat::diag(int d) const
    参数 d=0 时，表示取主对角线；当参数 d>0 是，表示取主对角线下方的次对角线，
    如 d=1 时，表示取主对角线下方，且紧贴主多角线的元素；当参数 d<0 时，表示取
    主对角线上方的次对角线
    */
    Mat diag1 = m1.diag(1);
    Mat diag2 = m2.diag(0);
    cout << "m1(diag) = \n" << diag1 << endl;
    cout << "m2(diag) = \n" << diag2 << endl;
    
    //多行、多列选中,用range(start,end)方法，注意包含start但不包含end,下标也是从0开始的
    Mat A = Mat::eye(10,10,CV_8UC1);
    Mat B = A(Range::all(),Range(1,3));//提取第 2 到 3 列 all(）表示所有行或者列
    Mat C = B(Range(5,9),Range(0,2));  //提取 B 的第 6 至 9 行
    cout << "A = \n" << A << endl
         << "B = \n" << B << endl
         << "C = \n" << C << endl;
    //roi方法
    /*
    Rect(int _x,int _y,int _width,int _height);
    参数意思为：左上角x坐标
    左上角y坐标
    矩形的宽
    矩形的高
    //如果创建一个Rect对象rect(100, 50, 50, 100)，那么rect会有以下几个功能：
    rect.area();     //返回rect的面积 5000
    rect.size();     //返回rect的尺寸 [50 × 100]
    rect.tl();       //返回rect的左上顶点的坐标 [100, 50]
    rect.br();       //返回rect的右下顶点的坐标 [150, 150]
    rect.width();    //返回rect的宽度 50
    rect.height();   //返回rect的高度 100
    rect.contains(Point(x, y));  //返回布尔变量，判断rect是否包含Point(x, y)点

    //还可以求两个矩形的交集和并集
    rect = rect1 & rect2;
    rect = rect1 | rect2;

    //还可以对矩形进行平移和缩放
    rect = rect + Point(-100, 100);	//平移，也就是左上顶点的x坐标-100，y坐标+100
    rect = rect + Size(-100, 100);	//缩放，左上顶点不变，宽度-100，高度+100

    //还可以对矩形进行对比，返回布尔变量
    rect1 == rect2;
    rect1 != rect2;
    */
    Mat D = A(Rect(0,0,1,5));
    Rect rect1(0,0,1,5);
    Rect rect2(0,0,1,4);
    cout << "D = \n" << D << endl
         << "rect = " << rect1 << endl;//rect1 = [1 x 5 from (0, 0)]
    //使用括号运算符
    Mat E = A(rect1);
    cout << E << endl;
}
```

#### 1.4.4 Mat 数学运算

```C++
void mat_calculate()
{
    Mat A = Mat::eye(4,4,CV_32FC1);
    Mat B = A * 3 + 1;
    Mat C = B.diag(0) + B.col(1);
    cout << "A = \n" << A << endl << endl;
    cout << "B = \n" << B << endl << endl;
    cout << "C = \n" << C << endl << endl;
    // 对两个向量执行点乘运算，就是对这两个向量对应位一一相乘之后求和的操作，
    //点乘的结果是一个标量。
    cout << "C .* diag(B) = \n" << C.dot(B.diag(0)) << endl;
    cout << C.mul(B.diag(0)) << endl;
    cout << "A.t() = \n" << A.t() << endl;
    //参与点乘的两个Mat矩阵的数据类型（type）只能是
    //CV_32F、 CV_64FC1、 CV_32FC2、 CV_64FC2 这4种类型中的一种
    cout << "A.inv() = \n" << A.inv() << endl;//取逆对type也有要求
    cout << "A * B = \n" << A * B << endl;
    //mul会计算两个Mat矩阵对应位的乘积，所以要求参与运算的矩阵A的行列和B的行列数一致。
    //计算结果是跟A或B行列数一致的一个Mat矩阵。
    cout << "A.mul(B) = \n" << A.mul(B) << endl;
    //矩阵对应元素的乘法和除法： A.mul(B)， A/B， alpha/A
    
    //矩阵相乘
    float left[2][3] = {{1,2,3},{4,5,6}};
    float right[3][2] = {{1,4},{2,5},{3,6}};
    Mat left_Mat(2,3,CV_32FC1,left);
    Mat right_Mat(3,2,CV_32FC1,right);

    Mat result_Mat = left_Mat * right_Mat;

    cout << "left_Mat:\n" << left_Mat << endl
         << "right_Mat:\n" << right_Mat << endl
         << "result_Mat:\n" << result_Mat << endl;
}

A = 
[1, 0, 0, 0;
 0, 1, 0, 0;
 0, 0, 1, 0;
 0, 0, 0, 1]

B = 
[4, 1, 1, 1;
 1, 4, 1, 1;
 1, 1, 4, 1;
 1, 1, 1, 4]

C = 
[5;
 8;
 5;
 5]

C .* diag(B) = 
92
[20;
 32;
 20;
 20]
A.t() = 
[1, 0, 0, 0;
 0, 1, 0, 0;
 0, 0, 1, 0;
 0, 0, 0, 1]
A.inv() = 
[1, 0, 0, 0;
 0, 1, 0, 0;
 0, 0, 1, 0;
 0, 0, 0, 1]
A * B = 
[4, 1, 1, 1;
 1, 4, 1, 1;
 1, 1, 4, 1;
 1, 1, 1, 4]
A.mul(B) = 
[4, 0, 0, 0;
 0, 4, 0, 0;
 0, 0, 4, 0;
 0, 0, 0, 4]
left_Mat:
[1, 2, 3;
 4, 5, 6]
right_Mat:
[1, 4;
 2, 5;
 3, 6]
result_Mat:
[14, 32;
 32, 77]
```

#### 1.4.5 Mat_类

待学习

貌似用在小型矩阵的直接输入

```C++
//小型矩阵 直接输入 使用Mat_()类
Mat small_mat = (Mat_<double>(3,3) << 0,-1,0,-1,5,6,1,4,7.1);
```

### 1.5 RNG类 随机数

 RNG类是opencv里C++的随机数产生器。
它可产生一个64位的int随机数。

目前可按均匀分布和高斯分布产生随机数

```C++
void rng_test()
{
    //创建RNG对象，使用默认种子“-1”
    RNG rng;

    //产生64位整数
    int N1 = rng;
    cout << "N1 = " << N1 << endl;
    int N2 = rng.next();                    //返回下一个随机整数，即N1.next();
    cout << "N2 = " << N2 << endl;

    /*-------------产生均匀分布的随机数uniform和高斯分布的随机数gaussian---------*/

    //总是得到double类型数据0.000000，因为会调用uniform(int,int),只会取整数，所以只产生0
    double N1a = rng.uniform(0,1);
    cout << "N1a = " << N1a << endl;

    //产生[0,1)范围内均匀分布的double类型数据
    double N1b = rng.uniform((double)0,(double)1);
    cout << "N1b = " << N1b << endl;

    //产生[0,1)范围内均匀分布的float类型数据，注意被自动转换为double了。
    double N1c = rng.uniform(0.f,1.f);
    cout << "N1c = " << N1c << endl;

    //产生[0,1)范围内均匀分布的double类型数据。
    double N1d = rng.uniform(0.,1.);
    cout << "N1d = " << N1d << endl;

    double N2h = rng.operator double();        //返回下一个double型数
    cout << "N2h = " << N2h << endl;

    //可能会因为重载导致编译不通过(确实没通过。。)
    //double N1e = rng.uniform(0,0.999999);

    //产生符合均值为0，标准差为2的高斯分布的随机数
    double N1g = rng.gaussian(2);
    cout << "N1g = " << N1g << endl;
    //=========================================================================//
    //产生[1,1000)均匀分布的int随机数填充fillM
    Mat_<int>fillM(3,3);
    rng.fill(fillM,RNG::UNIFORM,1,1000);
    cout << "filM = \n" << fillM << endl << endl;

    Mat fillM1(3,3,CV_8UC1);
    rng.fill(fillM1,RNG::UNIFORM,1,1000,true);
    cout << "filM1 = \n" << fillM1 << endl << endl;

    Mat fillM2(3,3,CV_8UC1);
    rng.fill(fillM2,RNG::UNIFORM,1,1000,false);
    cout << "filM2 = \n" << fillM2 << endl << endl;
    //fillM1产生的数据都在[0,，255)内，且小于255；
    //fillM2产生的数据虽然也在同样范围内，但是由于用了截断操作，所以很多数据都是255,

    //产生均值为1，标准差为3的随机double数填进fillN
    //Mat_<double>fillN(3,3);
    Mat fillN(3,3,CV_64FC1);
    rng.fill(fillN,RNG::NORMAL,1,3);
    cout << "filN = \n" << fillN << endl << endl;
    //====================================================================//
    //randShuffle() 将原数组（矩阵）打乱
    /*
    randShuffle( InputOutputArray dst,     输入输出数组（一维）
                   double iterFactor=1. ,  决定交换数值的行列的位置的一个系数...
                   RNG* rng=0 )            可选）随机数产生器，0表示使用默认的随机数产生器，
                                            即seed=-1。rng决定了打乱的方法
    */
    Mat randShufM =(Mat_<double>(2,3) << 1,2,3,4,5,6);
    cout << "randShufM = \n" << randShufM << endl;
    randShuffle(randShufM,7,0);
    cout << "randShufM = \n" << randShufM << endl;
}
```

## 2. OpenCV ML

使用OpenCV提供的ML实现简单机器学习算法

### 2.1 SVM

OpenCV3.x与OpenCV2.x中SVM的接口有了很大变化，在接口上使用了虚函数取代以前的定义。

#### 2.1.1 训练过程示例

```C++
/* SVM训练过程tutorial*/
void test_opencv_svm_train()
{
    // two class classifcation
    int labels[4] = { 1, -1, -1, -1 };
    float trainingData[4][2] = { { 501, 10 }, { 255, 10 }, { 501, 255 }, { 10, 501 } };

    Mat trainingDataMat(4, 2, CV_32FC1, trainingData);//输入的训练数据格式:Mat CV_32FC1
    Mat labelsMat(4, 1, CV_32SC1, labels);

    Ptr<SVM> svm = SVM::create();     //创建支持向量机空模型,得到一个Ptr智能指针,指向SVM
    svm->setType(SVM::C_SVC);		 // 设置支持向量机的类型
    /*
    1、C_SVC : C类支撑向量分类机。 n类分组 （n≥2），容许用异常值处罚因子C进行不完全分类。
    2、NU_SVC : 类支撑向量分类机。n类似然不完全分类的分类器。参数为gamma代替C。
    3、ONE_CLASS : 单分类器，所有的练习数据提取自同一个类里，然后SVM建树了一个分界线以分别该类在特点空间中所占区域和其它类在特点空间中所占区域。
    4、EPS_SVR : 用于回归。练习集中的特征向量和拟合出来的超平面的间隔须要小于p。异常值处罚因子C被采取。
    5、NU_SVR : 回归机，gamma代替p
    */
    svm->setC(1.0);		// 设置惩罚因子
    /*
     C是惩罚系数，即对误差的宽容度。
     C越高，说明越不能容忍出现误差,容易过拟合。
     C越小，容易欠拟合。C过大或过小，泛化能力变差
    */
    svm->setKernel(SVM::LINEAR); // 设置核函数类型
    /*
     LINEAR：线性核函数；

       POLY:多项式核函数；
      / -d用来设置多项式核函数的最高此项次数；默认值是3
      / -r用来设置核函数中的coef0，也就是公式中的第二个r，默认值是0。
       一般选择1-11：1 3 5 7 9 11，也可以选择2,4，6…

       RBF:径向机核函数【高斯核函数】；
      / -g用来设置核函数中的gamma参数设置，默认值是1/k（k是类别数）
      gamma是选择RBF函数作为kernel后，该函数自带的一个参数。
       隐含地决定了数据映射到新的特征空间后的分布，
       gamma越大，支持向量越少，gamma值越小，支持向量越多。
       支持向量的个数影响训练与预测的速度。

       SIGMOID:神经元的非线性作用函数核函数；
      / -g用来设置核函数中的gamma参数设置，默认值是1/k（k是类别数）
      / -r用来设置核函数中的coef0，也就是公式中的第二个r，默认值是0

       PRECOMPUTED：用户自定义核函数
      */
    svm->setTermCriteria(TermCriteria(TermCriteria::MAX_ITER, 100, 1e-6));//迭代次数,终止条件

    svm->train(trainingDataMat, ml::SampleTypes::ROW_SAMPLE, labelsMat);//输入训练数据进行训练 train(训练样本,训练样本存储格式,样本标签)
    																	//ml::SampleTypes::ROW_SAMPLE 表示训练数据是一行一行的

    //const string save_file = "test.xml"; // .xml, .yaml, .jsons
    svm->save("test_model.xml");				// 保存svm模型
    cout << "save sucessful" << endl;
}
```

#### 2.1.2 测试过程示例

predict输出的结果一定要用float类型去读取

> `float re = svm->predict(predict_sample) // 每次输入一个样本,返回预测值re`
>
> `Mat result(rows,cols,CV_32FC1)`
>
> `float re1 = svm->predict(predict_samples,result)`
>
> 读取每个测试样本的预测值:`result.at<float>(i)`

`svm.predict`使用的是`alpha*sv*another-rho`，如果为负的话则认为是正样本

```C++
/* SVM测试过程tutorial */
void test_opencv_svm_predict()
{
    const string model_file  = "test_model.xml";
    const int labels[]={ 1, 1, 1, 1, -1, -1, -1, -1 };//测试样本标签
    const vector<vector<float>>predictData = { { 490.f, 15.f }, { 480.f, 30.f }, { 511.f, 40.f }, { 473.f, 50.f },
        { 2.f, 490.f }, { 100.f, 200.f }, { 247.f, 223.f }, {510.f, 400.f} };//测试样本

    //Mat sampleMat = (Mat_<float>(1, 2) << j, i);
    //const int feature_length = 2;
    const int predict_count = (int)predictData.size();
    double correct_num = 0;
    float correct_acc = 0;
    Ptr<SVM> svm = SVM::load(model_file);//读取模型
    for (int i = 0; i < predict_count; i++)
    {
        Mat prdictMat = (Mat_<float>(1, 2) << predictData[i][0],predictData[i][1]);
        float response = svm->predict(prdictMat);//使用svm->predict() 进行预测 这里一定要用float类型
        if (labels[i] == response) correct_num++;
        printf("actual class: %d, target calss: %f\n", labels[i], response);
    }
    correct_acc = (correct_num / predict_count) * 100;
    printf("correct_acc = %.2f%\n",correct_acc);
}
```

```C++
/*测试样本一次性输入*/
const string model_file  = "test_model.xml";
const int labels[]={ 1, 1, 1, 1, -1, -1, -1, -1 };
float predictData[8][2] = { { 490.f, 15.f }, { 480.f, 30.f }, { 511.f, 40.f }, { 473.f, 50.f },
                           { 2.f, 490.f }, { 100.f, 200.f }, { 247.f, 223.f }, {510.f, 400.f} };

Mat predictMat(8,2,CV_32FC1,predictData); //这样依据数据创建Mat,好像只支持数组

double correct_num = 0;
float correct_acc = 0;
Ptr<SVM> svm = SVM::load(model_file);
Mat predictMat(8,2,CV_32FC1,predictData);
cout << predictMat << endl;

double correct_num = 0;
float correct_acc = 0;
Ptr<SVM> svm = SVM::load(model_file);

Mat reponse_Mat(8,1,CV_8FC1);
float rst = svm->predict(predictMat,reponse_Mat);

for(int i =0;i < 8;i++)
{
    cout << reponse_Mat.at<float>(i,0) << endl;
    if(reponse_Mat.at<float>(i,0) == labels[i])
        correct_num++;
}

correct_acc = (correct_num / 8) * 100;
printf("correct_acc = %.2f%\n",correct_acc);
```



```C++
/** @brief Predicts response(s) for the provided sample(s)

    @param samples The input samples, floating-point matrix
    @param results The optional output matrix of results.
    @param flags The optional flags, model-dependent. See cv::ml::StatModel::Flags.
     */
CV_WRAP virtual float predict( InputArray samples, OutputArray results=noArray(), int flags=0 ) const = 0;
```

```C++
/*获取SVM的各项参数*/
void svm_test()
{
    // visual representation
    int width = 512;
    int height = 512;
    Mat image = Mat::zeros(height, width, CV_8UC3);

    // training data
    int labels[4] = { 1, 0, 0, 0 };
    float trainingData[4][2] = { { 501, 10 }, { 255, 10 }, { 501, 255 }, { 10, 501 } };

    Mat trainingDataMat(4, 2, CV_32FC1, trainingData);
    Mat labelsMat(4, 1, CV_32SC1, labels);

    cout << "trainingDataMat:\n" << trainingDataMat << endl;

    // initial SVM
    Ptr<ml::SVM> svm = ml::SVM::create();
    svm->setType(ml::SVM::Types::C_SVC);
    svm->setKernel(ml::SVM::KernelTypes::POLY); //使用多项式核函数,方便查看支持向量
    svm->setGamma(0.1);
    svm->setDegree(2);
//    svm->setDegree(2);
    svm->setTermCriteria(TermCriteria(TermCriteria::MAX_ITER, 100, 1e-6));

    // train operation
    svm->train(trainingDataMat, ml::SampleTypes::ROW_SAMPLE, labelsMat);//ml::SampleTypes::ROW_SAMPLE 表示训练数据是一行一行的

    int svdim = svm->getVarCount(); //Returns the number of variables in training samples
    cout << "svdim:" << svdim << endl;
    //线性核函数情况下,只有一个压缩的支持向量(原因未知)
    Mat svMat = svm->getSupportVectors();
    cout << "svMat = \n" << svMat << endl
         << "svMat.size = " << svMat.size << endl;
    /*
    svMat = 
        [501, 10;
         255, 10;
         501, 255]
    */
    Mat alphaMat;// = Mat::zeros(sn,svdim,CV_32F);//每个支持向量对应的参数α(拉格朗日乘子)，默认alpha是float64的
    Mat svidxMat;// = Mat::zeros(1,sn,CV_64F);//支持向量所在的索引,在支持向量中的索引,label如何获得?怎么获得支持向量在原训练数据中的index?
    //The method returns rho parameter of the decision function, a scalar subtracted from the weighted sum of kernel responses.
    double b = svm->getDecisionFunction(0,alphaMat,svidxMat);
    cout << "alphaMat = \n" << alphaMat << endl << alphaMat.size() << endl;
    /*
    alphaMat = 
            [5.883922424074261e-09, 5.926219268115407e-09, -1.181014169218967e-08]
    */
    cout << "svidxMat = \n" << svidxMat << endl;
    /*
    svidxMat = 
   			 [1, 2, 0]
    */
    cout << "b = \n" << b << endl;
    /*
    int sn = svMat.rows;
    cout << "svMat =\n" << svMat << endl;
    printf("svdim = %d\n,svNum = %d\n",svdim,sn);


    Mat weights(1,svdim,CV_32FC1);
    for(int i = 0;i < sn;i++)
    {
        int index = svidxMat.at<int>(0,i);
        //cout << "index: \n" << index << endl;
        weights += alphaMat.at<float>(0,i) * labelsMat.at<int>(index,0) * trainingDataMat.row(index);
    }
    cout << "weights: \n" << weights << endl;

    float bias;
    float temp = 0;
    int j = 3;
    for(int i = 0;i < sn;i++)
    {
        int index = svidxMat.at<int>(0,i);
        j = svidxMat.at<int>(0,j);
        //cout << "index: \n" << index << endl;
        temp += alphaMat.at<float>(0,i) * labelsMat.at<int>(index,0) * (trainingDataMat.row(index).dot(trainingDataMat.row(j)));
    }
    bias = labelsMat.at<int>(j,0) - temp;
    cout << "bias = \n" << bias << endl;

    float w1 = weights.at<float>(0,0);
    float w2 = weights.at<float>(0,1);

    float p1x = 0;
    float p1y = (-bias - w1 * p1x) / w2; cout << p1y << endl;

    float p2x = 250;
    float p2y = (-bias - w1 * p2x) / w2;cout << p2y << endl;

    Point2f p1(p1x,p2x);
    Point2f p2(p2x,p2y);
    line(image,p1,p2,Scalar(0,255,0),1,LINE_8);
    */

    Mat weights = alphaMat.at<float>(0,0) * 1 * svMat.row(0);
    cout << "weights: \n" << weights << endl;
    float w1 = weights.at<float>(0,0);
    float w2 = weights.at<float>(0,1);

    float p1x = 0;
    float p1y = (-b - w1 * p1x) / w2; cout << p1y << endl;

    float p2x = 250;
    float p2y = (-b - w1 * p2x) / w2;cout << p2y << endl;

    Point2f p1(p1x,p2x);
    Point2f p2(p2x,p2y);
    line(image,p1,p2,Scalar(0,255,0),1,LINE_8);

    // prediction
#if 0
    Vec3b green(0, 255, 0);
    Vec3b blue(255, 0, 0);
    for (int i = 0; i < image.rows; i++)
    {
        for (int j = 0; j < image.cols; j++)
        {
            Mat sampleMat = (Mat_<float>(1, 2) << j, i);
            float respose = svm->predict(sampleMat);
//            cout << respose << "  ";
            if (respose == 1)
                image.at<Vec3b>(i, j) = green;
            else if (respose == 0)
                image.at<Vec3b>(i, j) = blue;
        }
//        cout << endl;
    }
#endif

    int thickness = -1;
    int lineType = LineTypes::LINE_8;

    circle(image, Point(501, 10), 5, Scalar(0, 0, 0), thickness, lineType);
    circle(image, Point(255, 10), 5, Scalar(255, 255, 255), thickness, lineType);
    circle(image, Point(501, 255), 5, Scalar(255, 255, 255), thickness, lineType);
    circle(image, Point(10, 501), 5, Scalar(255, 255, 255), thickness, lineType);

    thickness = 2;
    lineType = LineTypes::LINE_8;

    Mat sv = svm->getSupportVectors();
    for (int i = 0; i < sv.rows; i++)
    {
        const float* v = sv.ptr<float>(i);
        circle(image, Point((int)v[0], (int)v[1]), 6, Scalar(128, 128, 128), thickness, lineType);
    }


    imshow("SVM Simple Example", image);


    waitKey(0);
}
```

#### 2.1.3 训练完成后SVM各参数的获取

**获取支持向量**

```C++
/** @brief Retrieves all the support vectors
	获取支持向量
    The method returns all the support vectors as a floating-point matrix, where support vectors are stored as matrix rows.
*/
CV_WRAP virtual Mat getSupportVectors() const = 0;

/** @brief Retrieves all the uncompressed support vectors of a linear %SVM
	获取线性SVM未压缩的支持向量,压缩后只有一个
    The method returns all the uncompressed support vectors of a linear %SVM that the compressed support vector, used for prediction, was derived from. They are returned in a floating-point matrix, where the support vectors are stored as matrix rows.
*/
CV_WRAP Mat getUncompressedSupportVectors() const;
```

**获取决策函数**

```C++
    /** @brief Retrieves the decision function
	获得决策函数,返回偏置
    @param i the index of the decision function. If the problem solved is regression, 1-class 
    or 2-class classification, then there will be just one decision function and the index should 
    always be 0. Otherwise, in the case of N-class classification, there will be \f$N(N-1)/2\f$decision functions.
    @param alpha the optional output vector for weights, corresponding to different support vectors.
        In the case of linear %SVM all the alpha's will be 1's.
    @param svidx the optional output vector of indices of support vectors within the matrix of
        support vectors (which can be retrieved by SVM::getSupportVectors). In the case of linear
        %SVM each decision function consists of a single "compressed" support vector.

    The method returns rho parameter of the decision function, a scalar subtracted from the weighted
    sum of kernel responses.
     */
    CV_WRAP virtual double getDecisionFunction(int i, OutputArray alpha, OutputArray svidx) const = 0;
```

### 2.2 K-means

+ **OpenCV中的Kmeans聚类**

+ **实验了一维数据,二维数据**

```C++

#include "opencv2/highgui.hpp"
#include "opencv2/core.hpp"
#include "opencv2/imgproc.hpp"
#include <iostream>

using namespace cv;
using namespace std;

// static void help()
// {
//     cout << "\nThis program demonstrates kmeans clustering.\n"
//             "It generates an image with random points, then assigns a random number of cluster\n"
//             "centers and uses kmeans to move those cluster centers to their representitive location\n"
//             "Call\n"
//             "./kmeans\n" << endl;
// }

void kmeans_test()
{
    const int MAX_CLUSTERS = 5;
    Scalar colorTab[] =
    {
        Scalar(0, 0, 255),
        Scalar(0,255,0),
        Scalar(255,100,100),
        Scalar(255,0,255),
        Scalar(0,255,255)
    };//最多5类,对应5种颜色

    Mat img(500, 500, CV_8UC3);
    RNG rng(12345);
//    for (int i = 1;i < 31;++i)
//    {
//        cout << rng.uniform(2,MAX_CLUSTERS + 1) << " ";
//        if(i % 10 == 0) cout << endl;
//    }

    while(1)
    {
        int k, clusterCount = rng.uniform(2, MAX_CLUSTERS+1);//生成（2,3,4,5）中一个数...k只是定义,没有初始化
        int i, sampleCount = rng.uniform(1, 1001);//样本数
        Mat points(sampleCount, 1, CV_32FC2), labels;//points是CV_32FC2类型,所以每个坐标是个Vec2f,Scalar(a,b)来赋值
        clusterCount = MIN(clusterCount, sampleCount);
        std::vector<Point2f> centers;//聚类中心

        /* generate random sample from multigaussian distribution */
        for( k = 0; k < clusterCount; k++ )
        {
            Point center;
            center.x = rng.uniform(0, img.cols);
            center.y = rng.uniform(0, img.rows);
            Mat pointChunk = points.rowRange(k*sampleCount/clusterCount,
                                             k == clusterCount - 1 ? sampleCount :
                                             (k+1)*sampleCount/clusterCount);//每次取出样本的sampleCount/clusterCount个
            rng.fill(pointChunk, RNG::NORMAL, Scalar(center.x,center.y), Scalar(img.cols*0.05,img.rows*0.05));
        }

        randShuffle(points, 1, &rng);//打乱行

        double compactness = kmeans(points,                     //待聚类数据
                                    clusterCount,               //簇数
                                    labels,                     //labels表示每一个样本的类的标签，是一个整数，从0开始的索引整数,是簇数.
                                    TermCriteria(TermCriteria::EPS+TermCriteria::COUNT, 10, 1.0),
                                    3,                          //聚类3次,取结果最好的那次，
                                    KMEANS_PP_CENTERS,          //MEANS_PP_CENTERS:centers 初始化方法
                                    centers);                   //聚类中心
                                                                //返回 紧密度
        img = Scalar::all(0);
        Mat img2 = img.clone();
        for( i = 0; i < sampleCount; i++ )
        {
            int clusterIdx = labels.at<int>(i);
            Point2f ipt = points.at<Point2f>(i);
            //Point2f p(ipt,250 + clusterIdx*10);
            circle( img, ipt, 2, colorTab[clusterIdx], FILLED, LINE_AA );
            circle(img2,ipt,2,Scalar::all(255));
        }//显示聚类后的数据
        for (i = 0; i < (int)centers.size(); ++i)
        {
            Point2f c = centers[i];
            //Point2f p(c,250 + i*10);
            circle( img, c, 40, colorTab[i], 1, LINE_AA );
        }//聚类中心
        cout << "Compactness: " << compactness << endl;

        imshow("Before_cluster",img2);
        imshow("After_cluster", img);

        char key = (char)waitKey();
        if( key == 27 || key == 'q' || key == 'Q' ) // 'ESC'
            break;
    }
}
```

```C++
/** @brief Finds centers of clusters and groups input samples around the clusters.

The function kmeans implements a k-means algorithm that finds the centers of cluster_count clusters
and groups the input samples around the clusters. As an output, \f$\texttt{labels}_i\f$ contains a
0-based cluster index for the sample stored in the \f$i^{th}\f$ row of the samples matrix.

@note
-   (Python) An example on K-means clustering can be found at
    opencv_source_code/samples/python/kmeans.py
@param data Data for clustering. An array of N-Dimensional points with float coordinates is needed.
Examples of this array can be:
-   Mat points(count, 2, CV_32F);
-   Mat points(count, 1, CV_32FC2);
-   Mat points(1, count, CV_32FC2);
-   std::vector\<cv::Point2f\> points(sampleCount);
@param K Number of clusters to split the set by.
@param bestLabels Input/output integer array that stores the cluster indices for every sample.
@param criteria The algorithm termination criteria, that is, the maximum number of iterations and/or
the desired accuracy. The accuracy is specified as criteria.epsilon. As soon as each of the cluster
centers moves by less than criteria.epsilon on some iteration, the algorithm stops.
@param attempts Flag to specify the number of times the algorithm is executed using different
initial labellings. The algorithm returns the labels that yield the best compactness (see the last
function parameter).
@param flags Flag that can take values of cv::KmeansFlags
@param centers Output matrix of the cluster centers, one row per each cluster center.
@return The function returns the compactness measure that is computed as
\f[\sum _i  \| \texttt{samples} _i -  \texttt{centers} _{ \texttt{labels} _i} \| ^2\f]
after every attempt. The best (minimum) value is chosen and the corresponding labels and the
compactness value are returned by the function. Basically, you can use only the core of the
function, set the number of attempts to 1, initialize labels each time using a custom algorithm,
pass them with the ( flags = #KMEANS_USE_INITIAL_LABELS ) flag, and then choose the best
(most-compact) clustering.
*/
CV_EXPORTS_W double kmeans( InputArray data, int K, InputOutputArray bestLabels,
                            TermCriteria criteria, int attempts,
                            int flags, OutputArray centers = noArray() );
```

![image-20200606211216036](/home/swc/.config/Typora/typora-user-images/image-20200606211216036.png)

### 2.3 PCA

**PCA降维**

```C++
 void DoPca(const Mat &_data, int dim, Mat &eigenvalues, Mat &eigenvectors)
   {
       assert( dim>0 );
       Mat data =  cv::Mat_<double>(_data);

       int R = data.rows;
       int C = data.cols;

       if ( dim>C )
           dim = C;

       //计算均值
       Mat m = Mat::zeros( 1, C, data.type() );

       for ( int j=0; j<C; j++ )
       {
           for ( int i=0; i<R; i++ )
           {
               m.at<double>(0,j) += data.at<double>(i,j);
           }
       }

       m = m/R;
       //求取6列数据对应的均值存放在m矩阵中，均值： [1.67、2.01、1.67、2.01、1.67、2.01]


       //计算协方差矩阵
       Mat S =  Mat::zeros( R, C, data.type() );
       for ( int i=0; i<R; i++ )
       {
           for ( int j=0; j<C; j++ )
           {
               S.at<double>(i,j) = data.at<double>(i,j) - m.at<double>(0,j); // 数据矩阵的值减去对应列的均值
           }
       }

       Mat Average = S.t() * S /(R);
       //计算协方差矩阵的方式----(S矩阵的转置 * S矩阵)/行数


       //使用opencv提供的eigen函数求特征值以及特征向量
       eigen(Average, eigenvalues, eigenvectors);
       cout << "===================================" << endl;
       cout << Average * (eigenvectors.row(0).t()) << endl;
       cout << eigenvalues.at<float>(0,0) * (eigenvectors.row(0).t()) << endl;
   }

   void pca_test()
   {
       float A[ 60 ]={
           1.5 , 2.3 , 1.5 , 2.3 , 1.5 , 2.3 ,
           3.0 , 1.7 , 3.0 , 1.7 , 3.0 , 1.7 ,
           1.2 , 2.9 , 1.2 , 2.9 , 1.2 , 2.9 ,
           2.1 , 2.2 , 2.1 , 2.2 , 2.1 , 2.2 ,
           3.1 , 3.1 , 3.1 , 3.1 , 3.1 , 3.1 ,
           1.3 , 2.7 , 1.3 , 2.7 , 1.3 , 2.7 ,
           2.0 , 1.7 , 2.0 , 1.7 , 2.0 , 1.7 ,
           1.0 , 2.0 , 1.0 , 2.0 , 1.0 , 2.0 ,
           0.5 , 0.6 , 0.5 , 0.6 , 0.5 , 0.6 ,
           1.0 , 0.9 , 1.0 , 0.9 , 1.0 , 0.9 };

       Mat DataMat = Mat::zeros( 10, 6, CV_32FC1 );

       //将数组A里的数据放入DataMat矩阵中
       for ( int i=0; i<10; i++ )
       {
           for ( int j=0; j<6; j++ )
           {
               DataMat.at<float>(i, j) = A[i * 6 + j];
           }
       }
       cout << "A = \n" << DataMat << endl;
       // OPENCV PCA
       /*
        * PCA(InputArray data, InputArray mean, int flags, int maxComponents = 0);//maxComponents:主成分个数
        * PCA(InputArray data, InputArray mean, int flags, double retainedVariance);//retainedVariance:主成分比重
       */
       
       PCA pca(DataMat, noArray(), PCA::DATA_AS_ROW,0.8);//保留80%

       cout << "(pca)eigenvalues =  \n " << pca.eigenvalues << endl;
       cout << "(pca)eigenvectors = \n" << pca.eigenvectors << endl;
       cout << "(pca)means = \n" << pca.mean << endl;
		//手动计算特征值,特征向量
       Mat eigenvalues;//特征值
       Mat eigenvectors;//特征向量

       DoPca(DataMat, 3, eigenvalues, eigenvectors);

       cout << "(my)eigenvalues =  \n " << eigenvalues << endl;
       cout << "(my)eigenvectors = \n" << eigenvectors << endl;


       Mat means = repeat(pca.mean,DataMat.rows/pca.mean.rows,DataMat.cols/pca.mean.cols);
       cout << "means: \n" << means << endl;
       //DataMat -= means;
       Mat myProMat = DataMat * pca.eigenvectors.t();
       cout << "(去均值)A = \n" << DataMat << endl;
       cout << "(my)投影后: \n" << myProMat << endl;
       Mat projectMat = pca.project(DataMat);
       //divide(I1,I2,dst,scale,int dtype=-1);//dst=saturate_cast(I1*scale/I2)
       //Mat U;//= projectMat / DataMat;
       //divide(projectMat,DataMat,U,-1);

       //cout << "投影矩阵: \n" << U << endl;
       cout << "投影后: \n" << projectMat << endl;
       cout << "type: " << projectMat.type() << endl;
       Mat backproMat = pca.backProject(projectMat);
       cout << "逆投影: \n" << backproMat << endl;
   }
```

```C++
A = 
[1.5, 2.3, 1.5, 2.3, 1.5, 2.3;
 3, 1.7, 3, 1.7, 3, 1.7;
 1.2, 2.9000001, 1.2, 2.9000001, 1.2, 2.9000001;
 2.0999999, 2.2, 2.0999999, 2.2, 2.0999999, 2.2;
 3.0999999, 3.0999999, 3.0999999, 3.0999999, 3.0999999, 3.0999999;
 1.3, 2.7, 1.3, 2.7, 1.3, 2.7;
 2, 1.7, 2, 1.7, 2, 1.7;
 1, 2, 1, 2, 1, 2;
 0.5, 0.60000002, 0.5, 0.60000002, 0.5, 0.60000002;
 1, 0.89999998, 1, 0.89999998, 1, 0.89999998]
(pca)eigenvalues =  
 [2.7613358;
 1.0636643]
(pca)eigenvectors = 
[0.4352051, 0.37938085, 0.43520534, 0.3793807, 0.43520522, 0.37938064;
 -0.37938061, 0.43520552, -0.37938067, 0.43520534, -0.37938088, 0.43520504]
(pca)means = 
[1.6700001, 2.01, 1.6700001, 2.01, 1.6700001, 2.01]
===================================
[1.201748061921488;
 1.047597284962308;
 1.201748061921488;
 1.047597284962308;
 1.201748061921488;
 1.047597284962308]
[0.000207748176391447;
 0.0001810998764546114;
 0.000207748176391447;
 0.0001810998764546115;
 0.000207748176391447;
 0.0001810998764546115]
(my)eigenvalues =  
 [2.761335804891784;
 1.063664069223222;
 1.620076219567892e-16;
 -1.041696932190316e-33;
 -8.694544730646442e-17;
 -5.303045989490724e-16]
(my)eigenvectors = 
[0.4352053306202595, 0.3793806182886049, 0.4352053306202594, 0.3793806182886051, 0.4352053306202594, 0.3793806182886051;
 0.3793806182886052, -0.4352053306202586, 0.3793806182886049, -0.4352053306202596, 0.379380618288605, -0.4352053306202596;
 0, 0.8157956251356043, -0.02929374518914471, -0.4078978125678017, 0.0292937451891439, -0.4078978125678018;
 0, -3.228851907363228e-17, -5.992184024091453e-17, -0.7071067811865475, 7.885603783723002e-17, 0.7071067811865475;
 0, 0.03382550334104961, 0.70649973566364, -0.01691275167052486, -0.70649973566364, -0.01691275167052469;
 -0.8164965809277263, 1.665334536937735e-16, 0.4082482904638632, -1.665334536937735e-16, 0.4082482904638632, -1.665334536937735e-16]
means: 
[1.6700001, 2.01, 1.6700001, 2.01, 1.6700001, 2.01;
 1.6700001, 2.01, 1.6700001, 2.01, 1.6700001, 2.01;
 1.6700001, 2.01, 1.6700001, 2.01, 1.6700001, 2.01;
 1.6700001, 2.01, 1.6700001, 2.01, 1.6700001, 2.01;
 1.6700001, 2.01, 1.6700001, 2.01, 1.6700001, 2.01;
 1.6700001, 2.01, 1.6700001, 2.01, 1.6700001, 2.01;
 1.6700001, 2.01, 1.6700001, 2.01, 1.6700001, 2.01;
 1.6700001, 2.01, 1.6700001, 2.01, 1.6700001, 2.01;
 1.6700001, 2.01, 1.6700001, 2.01, 1.6700001, 2.01;
 1.6700001, 2.01, 1.6700001, 2.01, 1.6700001, 2.01]
(去均值)A = 
[1.5, 2.3, 1.5, 2.3, 1.5, 2.3;
 3, 1.7, 3, 1.7, 3, 1.7;
 1.2, 2.9000001, 1.2, 2.9000001, 1.2, 2.9000001;
 2.0999999, 2.2, 2.0999999, 2.2, 2.0999999, 2.2;
 3.0999999, 3.0999999, 3.0999999, 3.0999999, 3.0999999, 3.0999999;
 1.3, 2.7, 1.3, 2.7, 1.3, 2.7;
 2, 1.7, 2, 1.7, 2, 1.7;
 1, 2, 1, 2, 1, 2;
 0.5, 0.60000002, 0.5, 0.60000002, 0.5, 0.60000002;
 1, 0.89999998, 1, 0.89999998, 1, 0.89999998]
(my)投影后: 
[4.5761504, 1.2957033;
 5.8516889, -1.1948794;
 4.8673515, 2.4205155;
 5.2457056, 0.48225659;
 7.5756493, 0.51916856;
 4.7702842, 2.0455782;
 4.546073, -0.05673724;
 3.5819001, 1.4730897;
 1.3356931, 0.21429849;
 2.3299437, 0.036912113]
投影后: 
[0.10810643, 0.5721128;
 1.3836447, -1.9184699;
 0.39930728, 1.6969252;
 0.77766162, -0.24133384;
 3.107605, -0.20442188;
 0.30224022, 1.3219877;
 0.078029051, -0.78032768;
 -0.88614398, 0.7494992;
 -3.1323509, -0.50929195;
 -2.1381004, -0.68667835]
type: 5
逆投影: 
[1.5, 2.3000002, 1.5, 2.3, 1.4999999, 2.3;
 2.9999995, 1.6999996, 3, 1.6999997, 3.0000002, 1.7000003;
 1.2000002, 2.9000008, 1.2000002, 2.9000003, 1.1999997, 2.8999999;
 2.0999997, 2.2, 2.0999999, 2.2, 2.0999999, 2.2;
 3.0999994, 3.1000004, 3.1000001, 3.0999999, 3.0999997, 3.0999997;
 1.3000001, 2.7000005, 1.3000001, 2.7000003, 1.2999997, 2.6999998;
 1.9999999, 1.6999998, 2, 1.6999999, 2.0000002, 1.7000002;
 1.0000002, 2.0000002, 1, 2, 0.99999994, 2;
 0.50000048, 0.59999937, 0.49999976, 0.5999999, 0.50000024, 0.60000026;
 1.0000004, 0.89999944, 0.99999988, 0.89999986, 1.0000002, 0.90000021]
```

```C++
    /** @overload
    @param data input samples stored as matrix rows or matrix columns.
    @param mean optional mean value; if the matrix is empty (@c noArray()),
    the mean is computed from the data.
    @param flags operation flags; currently the parameter is only used to
    specify the data layout (PCA::Flags)
    @param maxComponents maximum number of components that %PCA should
    retain; by default, all the components are retained.
    */
    PCA(InputArray data, InputArray mean, int flags, int maxComponents = 0);

    /** @overload
    @param data input samples stored as matrix rows or matrix columns.
    @param mean optional mean value; if the matrix is empty (noArray()),
    the mean is computed from the data.
    @param flags operation flags; currently the parameter is only used to
    specify the data layout (PCA::Flags)
    @param retainedVariance Percentage of variance that PCA should retain.
    Using this parameter will let the PCA decided how many components to
    retain but it will always keep at least 2.
    */
    PCA(InputArray data, InputArray mean, int flags, double retainedVariance);
```

```C++
    /** @brief Projects vector(s) to the principal component subspace.

    The methods project one or more vectors to the principal component
    subspace, where each vector projection is represented by coefficients in
    the principal component basis. The first form of the method returns the
    matrix that the second form writes to the result. So the first form can
    be used as a part of expression while the second form can be more
    efficient in a processing loop.
    @param vec input vector(s); must have the same dimensionality and the
    same layout as the input data used at %PCA phase, that is, if
    DATA_AS_ROW are specified, then `vec.cols==data.cols`
    (vector dimensionality) and `vec.rows` is the number of vectors to
    project, and the same is true for the PCA::DATA_AS_COL case.
    */
    Mat project(InputArray vec) const;

    /** @overload
    @param vec input vector(s); must have the same dimensionality and the
    same layout as the input data used at PCA phase, that is, if
    DATA_AS_ROW are specified, then `vec.cols==data.cols`
    (vector dimensionality) and `vec.rows` is the number of vectors to
    project, and the same is true for the PCA::DATA_AS_COL case.
    @param result output vectors; in case of PCA::DATA_AS_COL, the
    output matrix has as many columns as the number of input vectors, this
    means that `result.cols==vec.cols` and the number of rows match the
    number of principal components (for example, `maxComponents` parameter
    passed to the constructor).
     */
    void project(InputArray vec, OutputArray result) const;

    /** @brief Reconstructs vectors from their PC projections.

    The methods are inverse operations to PCA::project. They take PC
    coordinates of projected vectors and reconstruct the original vectors.
    Unless all the principal components have been retained, the
    reconstructed vectors are different from the originals. But typically,
    the difference is small if the number of components is large enough (but
    still much smaller than the original vector dimensionality). As a
    result, PCA is used.
    @param vec coordinates of the vectors in the principal component
    subspace, the layout and size are the same as of PCA::project output
    vectors.
     */
    Mat backProject(InputArray vec) const;

    /** @overload
    @param vec coordinates of the vectors in the principal component
    subspace, the layout and size are the same as of PCA::project output
    vectors.
    @param result reconstructed vectors; the layout and size are the same as
    of PCA::project input vectors.
     */
    void backProject(InputArray vec, OutputArray result) const;
```





