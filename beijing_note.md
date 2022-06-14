## 充分利用速度信息

### step 1 估计车体/地面速度

bag里面并没有提供车体自身的速度信息，所以使用**地面的速度来代表车体的速度**；

这种方法要求地面点要提取准确，有很多提取地面点的方法，先写个函数接口，先用简单的方法实现，

```C++
//先极坐标栅格化，利用高度信息判断是否地面
for (int i=0;i<SegmentNum_swc;i++)
    {
        for (int j=0;j<BinNum_swc;j++)
        {
            int p_num = mesh[i][j].pointsVector.size();
            if(p_num == 0)
                continue;
            mesh[i][j].centerx /= p_num;
            mesh[i][j].centery /= p_num;
            mesh[i][j].centerz /= p_num;
            double delta_z = mesh[i][j].max_z - mesh[i][j].min_z;
            if(mesh[i][j].centerz < -2.5 && delta_z < 0.2)
            {
                mesh[i][j].segLabel = 0;
                segVector_ground.push_back(mesh[i][j]);//地面点
            }
            else
                segVector_obstacle.push_back(mesh[i][j]);
        }
    }
```

在平坦的地面上效果还不错，后期如果环境变化，要求上坡或者其他情况，再用复杂的算法重写接口。

用提取出来的地面点的速度信息去估计车体的速度，前后两帧可以估计**加速度**，delta_t 是固定的
$$
a=(v_t-v_{t-1})/\Delta t
$$
可以用100个点去估计，取均值和方差，这一帧地面点数points<100，取上一帧的结果+上一帧加速度   
$$
v_t=v_{t-1}+a*\Delta t
$$

+ [x] 方差过大的情况没有考虑 这时候提取到的"地面点"速度偏差太大,无法用,,,==已考虑,==将方差太大的去掉,取上一帧结果
+ [ ] 有时候 地面还有其他地方会出现  很多速度噪声点,这些点 的速度明显和周围点不一致,相差很多,要想办法去掉
+ [ ] 

### step 2 运动/静止目标提取

1. 初步想法

   **静止目标** 的速度 是 和**地面/车体 ** 速度  **一致**的

   ​															速度  **一致**的

   

2. 静/动分开做存在的问题

   有的物体可能会从动转为静

   

   