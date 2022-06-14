> 滤波器(随机估计)
>
> 坐标系  刚体运动
>
> 回路和图优化
>
> 视觉几何?

<img src="slam基础知识.assets/image-20200914200513523.png" alt="image-20200914200513523" style="zoom:67%;" />

## 基本理论一:坐标系|刚体运动

### 1.旋转矩阵

A:world frame

B:body frame

<img src="slam基础知识.assets/image-20200915153357062.png" alt="image-20200915153357062" style="zoom:67%;" />



<img src="slam基础知识.assets/image-20200915153521018.png" alt="image-20200915153521018" style="zoom:67%;" />

==标量r~ij~可用每个矢量在其参考坐标系中单位方向上投影的分量表示==

下面的截图出自台大机器人学课件1

<img src="slam基础知识.assets/image-20200915154347034.png" alt="image-20200915154347034" style="zoom:67%;" />

![image-20200915154455203](slam基础知识.assets/image-20200915154455203.png)

<img src="slam基础知识.assets/image-20200915160925898.png" alt="image-20200915160925898" style="zoom:67%;" />

![image-20200915161425969](slam基础知识.assets/image-20200915161425969.png)

<img src="slam基础知识.assets/image-20200915160925898.png" alt="image-20200915160925898" style="zoom:67%;" />![image-20200915161425969](slam基础知识.assets/image-20200915161425969.png)<img src="slam基础知识.assets/image-20200915160925898.png" alt="image-20200915160925898" style="zoom:67%;" />![image-20200915161425969](slam基础知识.assets/image-20200915161425969.png)

<img src="slam基础知识.assets/image-20200915195858626.png" alt="image-20200915195858626" style="zoom:67%;" />

<img src="slam基础知识.assets/image-20200915200839063.png" alt="image-20200915200839063" style="zoom:67%;" />

<img src="slam基础知识.assets/image-20200915201021204.png" alt="image-20200915201021204" style="zoom:67%;" />

#### **旋转矩阵的用法总结:**

<img src="slam基础知识.assets/image-20200915201652772.png" alt="image-20200915201652772" style="zoom:67%;" />

#### **旋转矩阵与转角**

![image-20200915202403356](slam基础知识.assets/image-20200915202403356.png)

##### **1.fixed angles**

固定x y z轴,转动的顺序不同,最后的状态也不同

逆时针为正

![image-20200915203108236](slam基础知识.assets/image-20200915203108236.png)

![image-20200915204442052](slam基础知识.assets/image-20200915204442052.png)

Atan2(x,y):根据xy值可以确定象限,atan()无法区分象限

##### 2.Euler angles

按照被转动对象的三个坐标轴转动

![image-20200915210040670](slam基础知识.assets/image-20200915210040670.png)

euler angles和fixed angles 相反的顺序,最后的状态相同

![image-20200915210432012](slam基础知识.assets/image-20200915210432012.png)



![image-20200915210631379](slam基础知识.assets/image-20200915210631379.png)

![image-20200915210706330](slam基础知识.assets/image-20200915210706330.png)

**小结**

![image-20200915211017849](slam基础知识.assets/image-20200915211017849.png)

四元数的运算效率更加高

### 2. 旋转+平移=转移矩阵

![image-20200915212617526](slam基础知识.assets/image-20200915212617526.png)

![image-20200915213128574](slam基础知识.assets/image-20200915213128574.png)

![image-20200915214821477](slam基础知识.assets/image-20200915214821477.png)

### **转移矩阵用法总结:**

![image-20200915215143530](slam基础知识.assets/image-20200915215143530.png)

### 转移矩阵的运算

#### 1.连续运算

![image-20200915215422879](slam基础知识.assets/image-20200915215422879.png)

#### 2.转移矩阵的逆矩阵

![image-20200915215918650](slam基础知识.assets/image-20200915215918650.png)

#### 3.连续运算,求未知的相对关系

![image-20200915220217179](slam基础知识.assets/image-20200915220217179.png)

![image-20200915220421408](slam基础知识.assets/image-20200915220421408.png)

![image-20200915220519378](slam基础知识.assets/image-20200915220519378.png)

### 3.四元数

![image-20200916095117818](slam基础知识.assets/image-20200916095117818.png)

![image-20200916095212554](slam基础知识.assets/image-20200916095212554.png)

![image-20200916095257040](slam基础知识.assets/image-20200916095257040.png)

![在这里插入图片描述](slam基础知识.assets/20200722173002422.png)

![在这里插入图片描述](slam基础知识.assets/20200722173124575.png)

![在这里插入图片描述](slam基础知识.assets/20200722173403889.png)

## 基本理论二: 李群与李代数  Lie Group

### 1.李群

+ 除了要对**刚体运动**进行描述,还要对它进行**估计**和**优化**

+ 李代数 无约束优化

![image-20200916103331299](slam基础知识.assets/image-20200916103331299.png)

![image-20200916103718746](slam基础知识.assets/image-20200916103718746.png)

![image-20200916104110628](slam基础知识.assets/image-20200916104110628.png)

### 2.李代数

![image-20200916104609933](slam基础知识.assets/image-20200916104609933.png)

![image-20200916104802427](slam基础知识.assets/image-20200916104802427.png)

![image-20200916104946989](slam基础知识.assets/image-20200916104946989.png)

![image-20200916105521155](slam基础知识.assets/image-20200916105521155.png)

![image-20200916105906922](slam基础知识.assets/image-20200916105906922.png)

![image-20200916111447268](slam基础知识.assets/image-20200916111447268.png)

![image-20200916112153399](slam基础知识.assets/image-20200916112153399.png)

![image-20200916112249491](slam基础知识.assets/image-20200916112249491.png)

![image-20200916112353480](slam基础知识.assets/image-20200916112353480.png)

![image-20200916112522821](slam基础知识.assets/image-20200916112522821.png)

![image-20200916112555202](slam基础知识.assets/image-20200916112555202.png)

### 3.指数与对数映射

![image-20200916145510106](slam基础知识.assets/image-20200916145510106.png)

![image-20200916145806715](slam基础知识.assets/image-20200916145806715.png)

![image-20200916145847627](slam基础知识.assets/image-20200916145847627.png)

![image-20200916150416697](slam基础知识.assets/image-20200916150416697.png)

![image-20200916150506376](slam基础知识.assets/image-20200916150506376.png)

### 4.李代数求导与扰动模型

![image-20200916150713248](slam基础知识.assets/image-20200916150713248.png)

![image-20200916150948367](slam基础知识.assets/image-20200916150948367.png)

![image-20200916151242487](slam基础知识.assets/image-20200916151242487.png)

![image-20200916151923001](slam基础知识.assets/image-20200916151923001.png) 

![image-20200916152116398](slam基础知识.assets/image-20200916152116398.png)

![image-20200916152950384](slam基础知识.assets/image-20200916152950384.png)

![image-20200916153110854](slam基础知识.assets/image-20200916153110854.png)

![image-20200916154422019](slam基础知识.assets/image-20200916154422019.png)

![image-20200916154913481](slam基础知识.assets/image-20200916154913481.png)

![image-20200916155103219](slam基础知识.assets/image-20200916155103219.png)

![image-20200916155237924](slam基础知识.assets/image-20200916155237924.png)

### 5.Sophus库的使用

```C++
#include <iostream>
#include <cmath>

#include "eigen3/Eigen/Core"
#include "eigen3/Eigen/Geometry"

#include "sophus/so3.hpp"
#include "sophus/se3.hpp"

using namespace std;
using Eigen::Matrix3d;
using Eigen::Vector3d;
using Eigen::Quaterniond;
using Eigen::AngleAxisd;


int main(int argc,char* argv[])
{
    // 沿Z轴转90度的旋转矩阵
    Matrix3d R = AngleAxisd(M_PI / 2, Vector3d(0, 0, 1)).toRotationMatrix();
    // 或者四元数
    Quaterniond q(R);
    Sophus::SO3d SO3_R(R);              // Sophus::SO3d可以直接从旋转矩阵构造
    Sophus::SO3d SO3_q(q);              // 也可以通过四元数构造
    // 二者是等价的
    cout << "SO(3) from matrix:\n" << SO3_R.matrix() << endl;
    cout << "SO(3) from quaternion:\n" << SO3_q.matrix() << endl;
    cout << "they are equal" << endl;

    // 使用对数映射获得它的李代数
    Vector3d so3 = SO3_R.log();
    cout << "so3 = " << so3.transpose() << endl;
    // hat 为向量到反对称矩阵
    cout << "so3 hat=\n" << Sophus::SO3d::hat(so3) << endl;
    // 相对的，vee为反对称到向量
    cout << "so3 hat vee= " << Sophus::SO3d::vee(Sophus::SO3d::hat(so3)).transpose() << endl;

    // 增量扰动模型的更新
    Vector3d update_so3(1e-4, 0, 0); //假设更新量为这么多
    Sophus::SO3d SO3_updated = Sophus::SO3d::exp(update_so3) * SO3_R;
    cout << "SO3 updated = \n" << SO3_updated.matrix() << endl;

    cout << "*******************************" << endl;
    // 对SE(3)操作大同小异
    Vector3d t(1, 0, 0);           // 沿X轴平移1
    Sophus::SE3d SE3_Rt(R, t);           // 从R,t构造SE(3)
    Sophus::SE3d SE3_qt(q, t);            // 从q,t构造SE(3)
    cout << "SE3 from R,t= \n" << SE3_Rt.matrix() << endl;
    cout << "SE3 from q,t= \n" << SE3_qt.matrix() << endl;
    // 李代数se(3) 是一个六维向量，方便起见先typedef一下
    typedef Eigen::Matrix<double, 6, 1> Vector6d;
    Vector6d se3 = SE3_Rt.log();
    cout << "se3 = " << se3.transpose() << endl;
    // 观察输出，会发现在Sophus中，se(3)的平移在前，旋转在后.
    // 同样的，有hat和vee两个算符
    cout << "se3 hat = \n" << Sophus::SE3d::hat(se3) << endl;
    cout << "se3 hat vee = " << Sophus::SE3d::vee(Sophus::SE3d::hat(se3)).transpose() << endl;

    // 最后，演示一下更新
    Vector6d update_se3; //更新量
    update_se3.setZero();
    update_se3(0, 0) = 1e-4;
    Sophus::SE3d SE3_updated = Sophus::SE3d::exp(update_se3) * SE3_Rt;
    cout << "SE3 updated = " << endl << SE3_updated.matrix() << endl;

    return 0;
}
```

## 基本理论三:非线性优化

### 1.状态估计问题





## ICP原理及其实现

[参考1](https://zhuanlan.zhihu.com/p/104735380)

截图:

![image-20200929221956929](slam基础知识.assets/image-20200929221956929.png)

![image-20200929222041403](slam基础知识.assets/image-20200929222041403.png)

![image-20200929222123926](slam基础知识.assets/image-20200929222123926.png)

![image-20200929222154014](slam基础知识.assets/image-20200929222154014.png)

![image-20200929222225273](slam基础知识.assets/image-20200929222225273.png)

![image-20200929222258606](slam基础知识.assets/image-20200929222258606.png)

![p](slam基础知识.assets/image-20200929222323838.png)

![image-20200929222427902](slam基础知识.assets/image-20200929222427902.png)