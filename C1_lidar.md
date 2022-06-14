### 1.启动雷达

ros: roslaunch rslidar_sdk start.launch 

在yaml文件中对前后补盲雷达已进行坐标系旋转

### 2.多雷达标定

#### 2.1 安装参数

测量不是太准，墙体对不上

front_main 

x:3cm

y:

z:16cm



main_car

x:224cm

z:未知，因为车要升起来



front_back:

x:

y:

z:未知



194+30+194

### 2.2 盲区

main: 0 0 0 

front:0 30cm 

back:0 50cm

遮挡形状是坡装，可以将几天线数去掉

