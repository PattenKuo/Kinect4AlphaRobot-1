
# Kinect4AlphaRobot
## 基于Kinect 2.0 与 Alpha 1s 阿尔法机器人的体感控制开发
![Kinect4AlphaRobot logo](https://raw.githubusercontent.com/sixcit/Kinect4AlphaRobot/master/readme-img/logo.jpg)

您好! 我是 **创客机械羊**，[广州创客节](http://maker-gz.com) 的官方指定吉祥物。^_^
项目是两位第二届创客成员@Cheman @JYang 完成。

### 项目说明
项目通过Kinect捕捉人体动作数据，通过蓝牙模块控制 Alpha Robot （优必选阿尔法机器人1S）

### 演示视频
[https://pan.baidu.com/s/1i5JyiUx](https://pan.baidu.com/s/1i5JyiUx)

### 项目技术点
* 蓝牙通信串口开发（项目可实现的基础）
* Kinect获取到的骨骼节点三维坐标数据与Alpha 1S电机转动角度的转换运算
* 机器人动作重心偏移解决方法（未解决）
* 解决Kinect排除多人数据捕捉干扰方法（未解决）

### 项目硬件模块
![bluemodel](https://raw.githubusercontent.com/sixcit/Kinect4AlphaRobot/master/readme-img/220645xhyws3tsjj3g5jw1.jpg)
![kinect2.0](https://raw.githubusercontent.com/sixcit/Kinect4AlphaRobot/master/readme-img/220645of84nu2lf522f2fs.jpg)
![alpha1s](https://raw.githubusercontent.com/sixcit/Kinect4AlphaRobot/master/readme-img/220646aisiujmhc7u2e3lc.jpg)


* BLE蓝牙4.0串口模块
* Kinect One 2.0（可同时记录6个人的20个关节点）
* Alpha 1s 阿尔法机器人（16个自由度）
* Win8系统以上的主机（支持USB3.0+内存在4G以上）

### 开发软件环境
* Kinect for Windows  SDK  2.0(附带kinect browser与kinect studio)
* 串口调试助手
* Visual Studio 2017（本项目使用C#）

### 实现原理思路
![yuanli](https://raw.githubusercontent.com/sixcit/Kinect4AlphaRobot/master/readme-img/220646w805w0z9tnujzndj.png)

* 利用Kinect获取人体骨骼关节点的三维空间坐标数据（）

> Kinect技术可追踪20个骨骼关节点，骨骼数据包含20个关节点的X,Y,Z坐标信息，我们利用Kinect SDK 里的BodyBase案例里的深度图像处理技术进行二次开发，取相对应的14个节点数值

![zuobiao](https://raw.githubusercontent.com/sixcit/Kinect4AlphaRobot/master/readme-img/220907drmmj6tgy6ygtr9g.png)
（图片只画出左侧标注）

*  头部(Head) –肩膀中心(ShoulderCenter)
* 肩膀中心(ShoulderCenter) – 脊柱中心(Spine)
*  脊柱中心(Spine) – 髋部中心(HipCenter)
*  髋部中心(HipCenter) – 左或右膝关节(KneeLeft or KneeRight)
* 左膝关节KneeLeft(右膝关节KneeRight) – 左踝关节leLeft (右踝关节AnkleRight)
* 左踝关节leLeft (右踝关节AnkleRight)- 左脚FootLeft (右脚FootRight)
* 左手(HeadLeft) –左手腕(Wrist Left)
*  左手腕(Wrist Left) – 左胳膊肘(Elbow Left)
* 左胳膊肘(Elbow Left) – 左肩膀(Shoulder Left)
* 左肩膀(Shoulder Left)–肩膀中心(Shoulder Center)
* 肩膀中心(Shoulder Center)-右肩膀(Shoulder Right)
* 右肩膀(Shoulder Right)- 右胳膊肘 (Elbow Right)
* 右胳膊肘 (Elbow Right)- 右手腕(Wrist Right)
* 右手腕(Wrist Right)- 右手 (Hand Right)

#### Kinect 人体感应示意图
![yuanli](https://raw.githubusercontent.com/sixcit/Kinect4AlphaRobot/master/readme-img/220646vdm7sky2na3a3du3.jpg)

面向感应器，X代表左右，Y代表上下，Z代表前后（距离在1.2~3.5m范围内，1.8米最适合）
蓝牙通信协议开发标准（可上优必选官网下载，看懂文档是开发者的基本功就不多说了）

> 要注意的是它是16进制，需要把组装好的命令字符串进行转换打包后，通过蓝牙模块发送
应用三角函数进行角度转换

面向感应器，三维坐标在扫描画面的左下角，以画面左边手臂为参考；
手从竖直立正抬至P状态为动作过程分析，r为与初始位置时的角度；
每一帧获取到的r值随运动改变，电机也将即时改变角度；



#### 体感控制示意图（面向设备）

![jishuan](https://raw.githubusercontent.com/sixcit/Kinect4AlphaRobot/master/readme-img/220646iz4fz300105fpf7s.jpg)

设P状态下，取shoulder肩部点O（x0,y0,z0）,elbow肘部点P（x,y,z），Z轴值保持不变；

对于肘到肩部shoulder所对应的机器人电机可忽略Z轴的影响，即通过这两点的X与Y值来求出r值。

当双手竖直向下时，Alpha 1S机器人有以下初始值：

* 机器人的左手（即画面右则手臂） shoulder对应的机器人电机初始角度为 0 度，竖直向上时为180度；
* 机器人的右手（即画面左则手臂） shoulder对应的机器人电机初始角度 180 度，竖直向上时为0度；

tan值的 + 与 –  区域正好可表达角度是大于90度还是小于90度，也可观察其递变规律。

转化为平面三角运算示意图为：为：



当手竖直向下时 电机角度 r= 0 ,当手竖直向上时，电机角度 r= 180; 则  
> tan r = (x-x0)/（y-y0）,  r = arctan (x-x0)/（y-y0）(0<r<π)

将小数转换成实际角度  
> α = r/π*180


将含有角度值的命令字符串用16进制转换后通过蓝牙传给机器人，即可响应。

同样道理，当手向前水平合拢时，肩部关节点shoulder基本不变，通过X与Z轴求得手臂在肩部点前后转动的角度，方法与公式就不再详细说明。

#### 勾搭我们
* QQ：690683755 (非诚勿扰)
* E-mail：cheman@6cit.com


