﻿# MR2 Robot 
## 湖北工业大学 ROBOCON 2019 

**本项目是一个并联式的四足机器人 每条腿使用了两个电机驱动共四条腿，八个电机期望实现四种步态，从慢到快依次为，行进步态（三足支撑）、小跑步态（两拍步态）、溜蹄步态、疾驰步态。**


>@感谢国内外各种开源项目、社区，在本项目中遇到过很多困难，很多次觉得无助，想放弃的时候，看见前人已经做出来的成果，就觉得有方向在前面，想要追求的东西，有人做出来过，可以实现，努力就可以做到，而且要把它做大做好 ，这个希望一直支撑着我走到现在。但我们的四足很多地方都还不够完美，甚至说不上好，做机器人是我的梦想，有机会的话，还是会将他做下去。

**这里是我做这个项目时候跟进的一些项目**

1、Stanford DOGGO https://github.com/Nate711/Doggo
>这是一个非常优秀的低成本（相对来说）无刷电机开源四足机器人，他们的控制逻辑
非常好，可以看到我的项目里面有很多地方的写法遵循了他们的思路。

2.MIT Mini Cheetah

>这是前段时间很火的一款无刷电机关节式的狗，在视频里可以看到性能非常好，但是
我没找到他们的开源网站

3.Ghost minitaur https://gitlab.com/ghostrobotics
>minitaur是我们四足机器人的结构参考，当时跟结构组的同学看到他们的视频时候，立马就拍板
觉得能做，因为有改项目引路，所以在结构大方向上没有出现很大的问题，感谢宾夕法尼
亚大学所作的探索，不过遗憾的是，这个项目并没有开源，已经商业化了，ghost公司有这一款机器人的地盘售卖，开放SDK。

---


***@provide by ylt in HBUT*** **2019年5月18-20**

**robocon 2019 南方赛区**

**广西.柳州**

## DEBUG

### d-2-19.5.18
+ 今天和别的学校交流了一波，3508的力量是足够支撑这个重量的机器人的跳跃的，问题在于速度环的PID需要是一个三阶的系统，这个参数很难找，要发挥电调最大的电流，难点在于电机的控制部分
+ 如果说全程使用跳跃的步态，对于电机的损伤也是非常的大的，在调试的时候要非常注意电机的电流，做电流检测。
+ 还有一个需要改进的问题是现在电机的初始位置，应该做一个基准校准程序，通过限位开关或者绝对编码器来确定他，不能单单的依靠手动来定位
+ 



### d-2019.5.16
+ 邻近比赛，很久没有更新写日志，将这一段时间的更新全写在一起
+ 成功的使用了惯性导航来保证由机械误差所引起的方向误差，使用了一个PID
解决了爬坡步态问题，使用一个高频率小跑，使用方向纠偏可以完整的实现不
偏的上坡，但是上坡的时候需要将机身后退升高前腿降低，将步态水平线方向
逆时针旋转15度以适应坡角，减少摩擦力不足引起的打滑现象
+ 开了一个任务做整个比赛的逻辑控制流
+ 问题：做了很多的分离步态，导致步态参数区域变得非常长，程序冗余多
导航模式选择过于复杂，应该使用结构体包装
旋转的时候方向并没有使用pid控制，导致系统在整个过程中会出现不可控的
因素


### d-2019.4.23
+ 使用陀螺仪实现了机身平行自稳


### d-2019.4.20
 + 增加combination.c将组合动作移到这里来写
 + 增加navigation.c 实现了机器人定向行走


### d-2019.4.17
+ 添加了mti30的接收 
+ 添加了和openmv对接的函数


### d-2019.4.15
+ 增加bsp_uart 移植大疆的串口逻辑，使用空闲中断DMA成功


### d-2019.4.11
+ 增加了参数检测函数IsValidParams,避免步态参数错误造成的机械损坏（
未测试）


### d-2019.4.10
+ 优化软件结构


### d-2019.4.2
+ 添加了每一个步态参数的pid实现pid分开调节
+ 添加了双腿分开行走的函数
+ bug! 7号和号电机有一点到不到位置  可能是应为运算能力到达上限了 处理
器处理八个电机还是有一些吃紧
+ 解决方法：释放电机的运算能力 串口和定时器都使用DMA


### d-2019.3.23
+ 添加测试直走后退前左前后左旋右旋
+ 添加了舵机驱动 使用tim4ch1 ch2，驱动两个舵机
+ 默认pid参数最大电机特性较硬
+ 陀螺仪暂时放弃不加，没有太好的应用条件


### d-2019.3.19
+ 更新了新的结构体参数，使每一种步态以参数的形式储存在数组里面，更新步态
的时候只需要改变参数即可
+ 增加了串口调试的通用性，可以在串口里面切换不同的步态
+ 第一次下地测试结果比较理想，预计现有的pid对于walk步态问题不大，但是在
高速运行的时候，可能会有些问题，下个版本中增加pid参数化调节，在程序的入
口处给出pid参数，每一种步态使用不同的pid参数达到稳定和性能的平衡


### d-2019.3.16
+ 重新构建了腿部轨迹计算逻辑，将其分为三层，依次传递参数，使逻辑清楚，重
复利用率高，并且为以后复杂的步态提供接口函数，将参数写入数组里,通过修改
参数生成不同的足部轨迹
+ 重新构建了调试函数，加入了步态控制，使用上位机发送命令，控制机器人的停
止和启动，预留了其他姿态的接口，方便以后完善
+ 完善了程序中函数的注释


