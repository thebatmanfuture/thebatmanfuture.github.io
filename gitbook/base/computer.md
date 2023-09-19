## 计算机组成原理

#### 第一章-基本组成

~~~
冯诺依曼计算机体系特点：
1.五大部件：运算器，控制器，存储器，输入，输出
2.指令和数据都是以二进制方式
3.指令由操作码和地址码组成
4.以运算器为中心
5.指令是有顺序存储
6.有指令和数据
~~~

[![qbkfzT.png](https://s1.ax1x.com/2022/04/04/qbkfzT.png)](https://imgtu.com/i/qbkfzT)

[![qbAz90.png](https://s1.ax1x.com/2022/04/04/qbAz90.png)](https://imgtu.com/i/qbAz90)

[![qbEUv8.png](https://s1.ax1x.com/2022/04/04/qbEUv8.png)](https://imgtu.com/i/qbEUv8)

~~~
MAR:存储器地址寄存器反映存储单元的个数
MDR:存储器数据寄存器反映存储字长
8个bit组成一个Byte，也就是字节。一个存储单元可以存储一个字节，也就是8个二进制位
MAR和MDR独占使用
逻辑结构和物理结构

设 MAR = 4 位，MDR = 8 位
存储单元个数 16
存储字长 8
~~~

[![qbVOWq.png](https://s1.ax1x.com/2022/04/04/qbVOWq.png)](https://imgtu.com/i/qbVOWq)

[![qbeUv6.png](https://s1.ax1x.com/2022/04/04/qbeUv6.png)](https://imgtu.com/i/qbeUv6)

[![qbVvlV.png](https://s1.ax1x.com/2022/04/04/qbVvlV.png)](https://imgtu.com/i/qbVvlV)

[![qbZltA.png](https://s1.ax1x.com/2022/04/04/qbZltA.png)](https://imgtu.com/i/qbZltA)

[![LasZqO.png](https://s1.ax1x.com/2022/04/18/LasZqO.png)](https://imgtu.com/i/LasZqO)

~~~
取数指令
~~~

[![qbZNnS.png](https://s1.ax1x.com/2022/04/04/qbZNnS.png)](https://imgtu.com/i/qbZNnS)

~~~
技术指标：
1.机器字长-CPU一次能处理的位数
2.运算速度-主频(计算频率)，吉普森法，MIPS，CPI
~~~

###### 总结

~~~
1.冯诺依曼计算机体系特点：6点
2.现代计算机体系特点(流程图)
3.硬件的组成
4.存储器，运算器，控制器的组成
5.取指令流程图
6.技术指标
~~~

#### 第三章-系统总线

~~~
总线：各个部件共享的传输介质
总线：有串行(一次一位)和并行(一次多位)
~~~

~~~
1. 面向 CPU 的双总线结构框图
缺点：对CPU消耗太大
~~~

[![qbmHTe.png](https://s1.ax1x.com/2022/04/04/qbmHTe.png)](https://imgtu.com/i/qbmHTe)

~~~
2. 单总线结构框图
缺点：总线被占用时，无法完成CPU和主存读取
~~~

[![qbneXV.png](https://s1.ax1x.com/2022/04/04/qbneXV.png)](https://imgtu.com/i/qbneXV)

~~~
3. 以存储器为中心的双总线结构框图
~~~

[![qbnYX6.png](https://s1.ax1x.com/2022/04/04/qbnYX6.png)](https://imgtu.com/i/qbnYX6)

~~~
总线分类：
1)总线使用位置
~~~

[![qbn6jP.png](https://s1.ax1x.com/2022/04/04/qbn6jP.png)](https://imgtu.com/i/qbn6jP)

~~~
地址总线通过数据总线传播地址
~~~

[![qbnjN4.png](https://s1.ax1x.com/2022/04/04/qbnjN4.png)](https://imgtu.com/i/qbnjN4)

~~~
总线特性和性能指标：
(1)特性：
~~~

[![qbu9jx.png](https://s1.ax1x.com/2022/04/04/qbu9jx.png)](https://imgtu.com/i/qbu9jx)

~~~
(2)性能指标
~~~

[![qbuPu6.png](https://s1.ax1x.com/2022/04/04/qbuPu6.png)](https://imgtu.com/i/qbuPu6)

~~~
标准传输率是理论最高速率，而不是实际值
同步：时钟
异步：控制信息



~~~





~~~
总线控制：
设置总线判优控制的目的是为了解决多个部件同时申请总线时的使用权分配问题
(1)总线判优控制
~~~

[![qbu7PH.png](https://s1.ax1x.com/2022/04/04/qbu7PH.png)](https://imgtu.com/i/qbu7PH)

~~~
1.链式查询
缺点：对故障很敏感，且优先级固定，速度慢
~~~

[![qbKkMq.png](https://s1.ax1x.com/2022/04/04/qbKkMq.png)](https://imgtu.com/i/qbKkMq)

~~~
2.计数器定时查询
缺点：需要的设备地址线更多
优先级可以相同，可以不同，相同随机计数，不同从前到后
~~~

[![qbK6w8.png](https://s1.ax1x.com/2022/04/04/qbK6w8.png)](https://imgtu.com/i/qbK6w8)

~~~
3.独立请求方式
速度最快，但是增加控制线数
~~~

[![qbMFte.png](https://s1.ax1x.com/2022/04/04/qbMFte.png)](https://imgtu.com/i/qbMFte)

~~~
(2)总线通信控制
~~~

[![qbMhND.png](https://s1.ax1x.com/2022/04/04/qbMhND.png)](https://imgtu.com/i/qbMhND)

[![qbQV5F.png](https://s1.ax1x.com/2022/04/04/qbQV5F.png)](https://imgtu.com/i/qbQV5F)

~~~
同步式数据输入：
地址-读命令-数据
同步式数据输出：
地址-数据-写命令
~~~



[![qbQOMR.png](https://s1.ax1x.com/2022/04/04/qbQOMR.png)](https://imgtu.com/i/qbQOMR)

###### 总结

~~~
1.总线是什么，有什么分类
2.总线结构框图(三个)缺点
3.总线分类
4.总线特性和性能指标
5.总线判优控制
6.总线通信控制-传输周期，四种方式
~~~

#### 第四章-存储器

~~~
存储器分类
(1)按存储介质分类
半导体，磁表面，光介质
~~~

[![qbYAyQ.png](https://s1.ax1x.com/2022/04/04/qbYAyQ.png)](https://imgtu.com/i/qbYAyQ)

~~~
(2)按存取方式
随机访问存储器：RAM
只读存储器：ROM
~~~

[![qbYMWT.png](https://s1.ax1x.com/2022/04/04/qbYMWT.png)](https://imgtu.com/i/qbYMWT)

~~~
(3)按在计算机中的作用分类
~~~

[![qbYWff.png](https://s1.ax1x.com/2022/04/04/qbYWff.png)](https://imgtu.com/i/qbYWff)

~~~
存储器的层次结构

寄存器和缓存-静态RAM
主存-动态RAM
~~~

[![qbtaHs.png](https://s1.ax1x.com/2022/04/04/qbtaHs.png)](https://imgtu.com/i/qbtaHs)

[![qbUkQO.png](https://s1.ax1x.com/2022/04/04/qbUkQO.png)](https://imgtu.com/i/qbUkQO)

~~~
主存的基本组成
半导体存储器的基本组成

译码器
(1)线性译码：控制简单，速度快，地址空间较小
(2)多重译码：控制复杂，地址空间大
~~~

[![qbaQE9.png](https://s1.ax1x.com/2022/04/04/qbaQE9.png)](https://imgtu.com/i/qbaQE9)

[![O1eejU.jpg](https://s1.ax1x.com/2022/05/08/O1eejU.jpg)](https://imgtu.com/i/O1eejU)

~~~
主存和 CPU 的联系
~~~

![image-20220404141842747](C:\Users\batma\AppData\Roaming\Typora\typora-user-images\image-20220404141842747.png)

~~~
字节：8位二进制数的组成1kb = 8b(一个b[比特]就是一个二进制数)
字：通常由多个字节（8位）组成，一个字的位数即为字长，64位（8个字节）
存储单元：以8位二进制作为一个存储单元，也就是一个字节
存储字：存储字的位数，也就是存储字的长度，存储字长
~~~

https://blog.csdn.net/weixin_44915226/article/details/104543111

[![qbfgMQ.png](https://s1.ax1x.com/2022/04/04/qbfgMQ.png)](https://imgtu.com/i/qbfgMQ)

[![qbhbff.png](https://s1.ax1x.com/2022/04/04/qbhbff.png)](https://imgtu.com/i/qbhbff)

[![qb4aNt.png](https://s1.ax1x.com/2022/04/04/qb4aNt.png)](https://imgtu.com/i/qb4aNt)

~~~
位扩展和字扩展
~~~

[![qb4Tu4.png](https://s1.ax1x.com/2022/04/04/qb4Tu4.png)](https://imgtu.com/i/qb4Tu4)

~~~
半导体存储芯片的译码驱动方式
~~~

[![qbIPQU.png](https://s1.ax1x.com/2022/04/04/qbIPQU.png)](https://imgtu.com/i/qbIPQU)[![qbIFL4.png](https://s1.ax1x.com/2022/04/04/qbIFL4.png)](https://imgtu.com/i/qbIFL4)

~~~
静态RAM的存储元结构
一个存储元只能存储一个数据，至少包含6个三极管
~~~

[![La6oVO.png](https://s1.ax1x.com/2022/04/18/La6oVO.png)](https://imgtu.com/i/La6oVO)[![La64r6.png](https://s1.ax1x.com/2022/04/18/La64r6.png)](https://imgtu.com/i/La64r6)

~~~
集成度高，功耗小(有刷新过程)，所以慢
~~~

~~~
T1 T2触发器工作管，T3 T4 负载管
T5 T6门控
~~~

~~~
这个CS是片选，只要片选有效就工作，如果无效就不工作
WE是写有效，低是写，高是读
~~~

![qqlN4O.png](https://s1.ax1x.com/2022/04/04/qqlN4O.png)

[![LacVs0.png](https://s1.ax1x.com/2022/04/18/LacVs0.png)](https://imgtu.com/i/LacVs0)

~~~
1. 集中刷新
2. 分散刷新
3. 异步刷新
~~~





~~~
写入：
T1 Cg记忆部件
T3 写选门控
T3门开
写线信号对Cg充放电
读出：
T1 Cg记忆部件
T2 读选门控
T4 预充电控制
~~~

~~~
动态RAM-三管
~~~

[![qq1tLn.png](https://s1.ax1x.com/2022/04/04/qq1tLn.png)](https://imgtu.com/i/qq1tLn)

[![qq3U6H.png](https://s1.ax1x.com/2022/04/04/qq3U6H.png)](https://imgtu.com/i/qq3U6H)

[![LaclW9.png](https://s1.ax1x.com/2022/04/18/LaclW9.png)](https://imgtu.com/i/LaclW9)[![Lac8Q1.png](https://s1.ax1x.com/2022/04/18/Lac8Q1.png)](https://imgtu.com/i/Lac8Q1)[![LacGsx.png](https://s1.ax1x.com/2022/04/18/LacGsx.png)](https://imgtu.com/i/LacGsx)https://s1.ax1x.com/2022/04/18/Laccef.png[![LacjfJ.png](https://s1.ax1x.com/2022/04/18/LacjfJ.png)](https://imgtu.com/i/LacjfJ)



[![LagM0f.png](https://s1.ax1x.com/2022/04/18/LagM0f.png)](https://imgtu.com/i/LagM0f)

~~~
低位交叉可以提高效率
~~~

[![Lag1AS.png](https://s1.ax1x.com/2022/04/18/Lag1AS.png)](https://imgtu.com/i/Lag1AS)

[![LagdBV.png](https://s1.ax1x.com/2022/04/18/LagdBV.png)](https://imgtu.com/i/LagdBV)

[![LagccR.png](https://s1.ax1x.com/2022/04/18/LagccR.png)](https://imgtu.com/i/LagccR)

[![LagfHK.png](https://s1.ax1x.com/2022/04/18/LagfHK.png)](https://imgtu.com/i/LagfHK)

[![LagODP.png](https://s1.ax1x.com/2022/04/18/LagODP.png)](https://imgtu.com/i/LagODP)



###### 总结

~~~
1.存储器分类-存储介质，存取方式，作用
2.存储器的层次结构
3.主存的基本组成
4.主存和 CPU 的联系
5.存储单元地址分配
6.主存的技术指标
7.半导体的芯片结构
8.片选线作用
9.静态RAM-基本单元电路，读和写，2114
10.动态RAM-基本单元电路，字扩展，位扩展，都扩展
~~~















#### 第五章-输入输出

~~~
发展概况：
1.早期
分散连接，CPU和IO设备串行工作-程序查询方式
2.接口模块和DMA阶段
总线连接，CPU和IO设备并行工作-两种方式：中断方式，DMA 方式
3. 具有通道结构的 阶段
4. 具有I/O处理的 阶段
~~~

~~~
输入输出系统组成
~~~

[![qqGEzF.png](https://s1.ax1x.com/2022/04/04/qqGEzF.png)](https://imgtu.com/i/qqGEzF)

[![qqLpoF.png](https://s1.ax1x.com/2022/04/04/qqLpoF.png)](https://imgtu.com/i/qqLpoF)

[![qqOZ1s.png](https://s1.ax1x.com/2022/04/04/qqOZ1s.png)](https://imgtu.com/i/qqOZ1s)

[![qqOun0.png](https://s1.ax1x.com/2022/04/04/qqOun0.png)](https://imgtu.com/i/qqOun0)

[![qqOBND.png](https://s1.ax1x.com/2022/04/04/qqOBND.png)](https://imgtu.com/i/qqOBND)

[![qqO3h4.png](https://s1.ax1x.com/2022/04/04/qqO3h4.png)](https://imgtu.com/i/qqO3h4)

[![qqOdHK.png](https://s1.ax1x.com/2022/04/04/qqOdHK.png)](https://imgtu.com/i/qqOdHK)

~~~
I/O设备
~~~

[![qqXXQA.png](https://s1.ax1x.com/2022/04/04/qqXXQA.png)](https://imgtu.com/i/qqXXQA)

~~~
I/O接口
~~~

[![qqjEyn.png](https://s1.ax1x.com/2022/04/04/qqjEyn.png)](https://imgtu.com/i/qqjEyn)

[![qqjGO1.png](https://s1.ax1x.com/2022/04/04/qqjGO1.png)](https://imgtu.com/i/qqjGO1)

###### 总结

~~~
1.输入输出设备发展阶段(4个)
2.输入输出系统的组成
3.IO设备与主机的联系方式
4.IO设备与主机的连接方式
5.IO设备与主机的控制方式
~~~





#### 第六章-计算机运算方法

~~~
原码：
正数的原码是本身，负数的原码第一位是1
~~~

![](https://i.bmp.ovh/imgs/2022/04/04/2a3567f4c8326981.png)

~~~
补码：当真值为 负 时，补码 可用 原码除符号位外，每位取反，末位加 1 求得
~~~

![](https://i.bmp.ovh/imgs/2022/04/04/ae8415cc38b67e17.png)![](https://i.bmp.ovh/imgs/2022/04/04/7a272ead137dd36b.png)

![](https://i.bmp.ovh/imgs/2022/04/05/aa96084fc7a85945.png)

![](https://i.bmp.ovh/imgs/2022/04/05/fdef4e27ce97f846.png)

![](https://i.bmp.ovh/imgs/2022/04/05/8d0998183aac0690.png)

~~~
移码和补码只差一个符号位
~~~

![](https://i.bmp.ovh/imgs/2022/04/05/75cfad31e80cd5ac.png)

~~~
方便的判断浮点数的阶码大小
~~~

![](https://s3.bmp.ovh/imgs/2022/04/05/ddf8b9fe40a2d61a.png)

![](https://s3.bmp.ovh/imgs/2022/04/05/13df2d92ea9380e7.png)

~~~
S	定点小数
j	定点整数
~~~

![](https://s3.bmp.ovh/imgs/2022/04/05/8b9316f6d5e4ba91.png)

[![LpxZuV.png](https://s1.ax1x.com/2022/04/08/LpxZuV.png)](https://imgtu.com/i/LpxZuV)

[![LpzQZ8.png](https://s1.ax1x.com/2022/04/08/LpzQZ8.png)](https://imgtu.com/i/LpzQZ8)[![Lpz8iQ.png](https://s1.ax1x.com/2022/04/08/Lpz8iQ.png)](https://imgtu.com/i/Lpz8iQ)

~~~
小数点后一个有效位-2	基数
小数点后两位有效-4位	基础

对于负数，补码时0有效，1无效


~~~











![](https://s3.bmp.ovh/imgs/2022/04/05/3fdb82806219bbae.png)

###### 总结

~~~
1.三种机器数：原码，反码，补码
2.移码表示法
3.定点表示和浮点表示-浮点数表示形式
~~~











































