## 0. 北京雷达

**消息流图**

<img src="/home/swc/.config/Typora/typora-user-images/image-20200611094527399.png" alt="image-20200611094527399" style="zoom:80%;" />

**channels:**

```
0 rgb
1 avg_int
2 vel
3 radius
4 theta
5 phi
6 w
7 h
8 point_moving
9 point_in_vel_window
10 v0
```

## 1.处理思路

根据点云和速度信息,把行人,车辆提取出来

可以利用胡俊的模型,进一步叠加速度信息,实现车辆/行人检测,因为这两者的速度和静止的障碍都不一样.



构造栅格地图，将所有的点云数据都投影到各自对应的栅格中。

然后针对每一个栅格中点云的速度值，赋予这个栅格速度属性（这个过程本身就是一个去噪的过程，用一个栅格中最有代表性的点的速度作为这个栅格的速度，建议采用中值滤波）。在此基础上，将速度大小相当位置相邻的栅格合并在一起作为同一个目标，最终聚类出具有相当的速度大小同时位置相邻的目标，同时也得到了这个目标运动速度大小。然后进一步结合前后帧信息，确定这个目标的速度方向。这样，即便你并不清楚这个目标是什么类型，但是也能利用点云速度信息和点云位置信息，分离出不同的目标并给出它们的运动趋势的预测。最终以颜色代表速度大小，箭头绘制速度方向。你看你能否尝试这样做一下？



这样做出来的目的，其实就是实现了对所有运动目标的检测，而不管这个目标是你认识的还是不认识的。