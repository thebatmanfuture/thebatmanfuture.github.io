## 汇编语言

#### 绪论

~~~
机器语言是机器指令的集合
机器指令是一台机器可以正确执行的命令，由一串二进制数表示，例 01010000
汇编语言的主体是汇编指令
汇编指令和机器指令的差别在于指令的表示方法上，汇编指令是机器指令便于记忆的书写格式，汇编指令是机器指令的助记符
~~~

[![OEgqvd.png](https://s1.ax1x.com/2022/05/04/OEgqvd.png)](https://imgtu.com/i/OEgqvd)

~~~
计算机中的数据和指令，存储在内存或磁盘上
存储器被划分为若干个存储单元，每个存储单元从0开始顺序编号
~~~

[![OERCo6.png](https://s1.ax1x.com/2022/05/04/OERCo6.png)](https://imgtu.com/i/OERCo6)

[![OER1fS.png](https://s1.ax1x.com/2022/05/04/OER1fS.png)](https://imgtu.com/i/OER1fS)

[![OEWS1g.png](https://s1.ax1x.com/2022/05/04/OEWS1g.png)](https://imgtu.com/i/OEWS1g)

[![OEWggg.png](https://s1.ax1x.com/2022/05/04/OEWggg.png)](https://imgtu.com/i/OEWggg)

~~~
内存地址空间
; CPU地址总线宽度为N，寻址空间为2^N B
; 8086CPU的地址总线宽度为20，那么可以寻址1MB个内存单元，其内存地址空间为1MB
~~~

[![OEhGlD.png](https://s1.ax1x.com/2022/05/04/OEhGlD.png)](https://imgtu.com/i/OEhGlD)

~~~
mount c d:\dosbox\masm
c:
~~~

#### 访问寄存器和内存

[![OE5yZj.png](https://s1.ax1x.com/2022/05/04/OE5yZj.png)](https://imgtu.com/i/OE5yZj)

[![OEIKTs.png](https://s1.ax1x.com/2022/05/04/OEIKTs.png)](https://imgtu.com/i/OEIKTs)

[![OEI0t1.png](https://s1.ax1x.com/2022/05/04/OEI0t1.png)](https://imgtu.com/i/OEI0t1)

[![OEIfAA.png](https://s1.ax1x.com/2022/05/04/OEIfAA.png)](https://imgtu.com/i/OEIfAA)

[![OExV1J.png](https://s1.ax1x.com/2022/05/04/OExV1J.png)](https://imgtu.com/i/OExV1J)

[![OEzYPU.png](https://s1.ax1x.com/2022/05/04/OEzYPU.png)](https://imgtu.com/i/OEzYPU)

[![OVSedx.png](https://s1.ax1x.com/2022/05/04/OVSedx.png)](https://imgtu.com/i/OVSedx)

~~~
物理地址=段地址×16+偏移地址
内存并没有分段，段的划分来自于CPU
~~~

[![OVpv5V.png](https://s1.ax1x.com/2022/05/04/OVpv5V.png)](https://imgtu.com/i/OVpv5V)

~~~asm
输入debug进入
R - 查看寄存器内容
R 寄存器名

D - 列出预设地址内存处的128个字节的内容
D 段地址:偏移地址

E 段地址:偏移地址 数据1 数据2 ...
E 段地址:偏移地址
例如：e 2000:0000 12 34 56 AB 3F F3
例如：e 2000:0000
12.61 34.41 56.62 AB.F4

U命令将内存中的机器指令翻译成汇编指令
有汇编指令
mov ax, 0123H
mov bx, 0003H
mov ax, bx
add ax, bx
:对应的机器码为
B8 23 01
BB 03 00
89 D8
01 D8
: e 地址 数据 - 写入
: d 地址 - 查看
: u 地址 - 查看代码

a	写入汇编指令	t	执行CS:IP处的指令
a 073f:0100
mov ax, 0123H
mov bx, 0003H
mov ax, bx
add ax, bx

t

q	退出debug
~~~

~~~
CS：代码段寄存器
IP： 指令指针寄存器
CS:IP：CPU将内存中CS:IP指向的内容当作指令执行
~~~

[![OVZirT.png](https://s1.ax1x.com/2022/05/04/OVZirT.png)](https://imgtu.com/i/OVZirT)

~~~
8086PC工作过程的简要描述：
（1）从CS:IP指向内存单元读取
指令，读取的指令进入指
令缓冲器；
（2）IP = IP + 所读取指令的长
度，从而指向下一条指令；
（3）执行指令。 转到步骤
（1），重复这个过程。
~~~

![](https://s1.ax1x.com/2022/05/09/O85Nan.png)

[![O8I6l8.png](https://s1.ax1x.com/2022/05/09/O8I6l8.png)](https://imgtu.com/i/O8I6l8)

[![O8IfTs.png](https://s1.ax1x.com/2022/05/09/O8IfTs.png)](https://imgtu.com/i/O8IfTs)

[![O8IbXF.png](https://s1.ax1x.com/2022/05/09/O8IbXF.png)](https://imgtu.com/i/O8IbXF)[![O8Ixt1.png](https://s1.ax1x.com/2022/05/09/O8Ixt1.png)](https://imgtu.com/i/O8Ixt1)

[![O8oZtI.png](https://s1.ax1x.com/2022/05/09/O8oZtI.png)](https://imgtu.com/i/O8oZtI)

[![O8oGAs.png](https://s1.ax1x.com/2022/05/09/O8oGAs.png)](https://imgtu.com/i/O8oGAs)

[![O8o0uF.png](https://s1.ax1x.com/2022/05/09/O8o0uF.png)](https://imgtu.com/i/O8o0uF)[![O8oDHJ.png](https://s1.ax1x.com/2022/05/09/O8oDHJ.png)](https://imgtu.com/i/O8oDHJ)

[![O8osE9.png](https://s1.ax1x.com/2022/05/09/O8osE9.png)](https://imgtu.com/i/O8osE9)

[![O8oWjO.png](https://s1.ax1x.com/2022/05/09/O8oWjO.png)](https://imgtu.com/i/O8oWjO)

#### 汇编语言程序

[![O8TpUs.png](https://s1.ax1x.com/2022/05/09/O8TpUs.png)](https://imgtu.com/i/O8TpUs)

[![O8T1xK.png](https://s1.ax1x.com/2022/05/09/O8T1xK.png)](https://imgtu.com/i/O8T1xK)

[![O8Tqo9.png](https://s1.ax1x.com/2022/05/09/O8Tqo9.png)](https://imgtu.com/i/O8Tqo9)

[![O87MwQ.png](https://s1.ax1x.com/2022/05/09/O87MwQ.png)](https://imgtu.com/i/O87MwQ)

[![O1zN4g.png](https://s1.ax1x.com/2022/05/08/O1zN4g.png)](https://imgtu.com/i/O1zN4g)

~~~assembly
masm xxx.sam	;编译生成obj
list xxx		;连接生成exe
~~~

[![O8jY4g.png](https://s1.ax1x.com/2022/05/09/O8jY4g.png)](https://imgtu.com/i/O8jY4g)

[![O8jxqP.png](https://s1.ax1x.com/2022/05/09/O8jxqP.png)](https://imgtu.com/i/O8jxqP)

[![O8vlRJ.png](https://s1.ax1x.com/2022/05/09/O8vlRJ.png)](https://imgtu.com/i/O8vlRJ)

[![O8vgdf.png](https://s1.ax1x.com/2022/05/09/O8vgdf.png)](https://imgtu.com/i/O8vgdf)

[![O8zY4O.png](https://s1.ax1x.com/2022/05/09/O8zY4O.png)](https://imgtu.com/i/O8zY4O)[![O8zU8e.png](https://s1.ax1x.com/2022/05/09/O8zU8e.png)](https://imgtu.com/i/O8zU8e)

[![O8zTaV.png](https://s1.ax1x.com/2022/05/09/O8zTaV.png)](https://imgtu.com/i/O8zTaV)

~~~
这里我们的loop s
在debug中看到的其实就是 loop 地址，这里是地址是直接指向我们add dx,ax的地址
~~~

[![OGipCV.png](https://s1.ax1x.com/2022/05/09/OGipCV.png)](https://imgtu.com/i/OGipCV)

~~~
问题：计算ffff:0~ffff:b字节单元中的数据的和，结果存储在dx中

对策：取出8位数据，加到16位的寄存器
mov al, ds:[addr]
mov ah, 0
add dx, ax
~~~

[![OGFhfP.png](https://s1.ax1x.com/2022/05/09/OGFhfP.png)](https://imgtu.com/i/OGFhfP)[![OGFIl8.png](https://s1.ax1x.com/2022/05/09/OGFIl8.png)](https://imgtu.com/i/OGFIl8)

[![OGkdBQ.png](https://s1.ax1x.com/2022/05/09/OGkdBQ.png)](https://imgtu.com/i/OGkdBQ)

[![OGA3b4.png](https://s1.ax1x.com/2022/05/09/OGA3b4.png)](https://imgtu.com/i/OGA3b4)

[![OGEpdJ.png](https://s1.ax1x.com/2022/05/09/OGEpdJ.png)](https://imgtu.com/i/OGEpdJ)

![](https://s3.bmp.ovh/imgs/2022/05/09/644af3d0d2bc0c83.png)

#### 内存寻址方式

![](https://s3.bmp.ovh/imgs/2022/05/09/9f0aeea6e8ab57d6.png)

![](https://s3.bmp.ovh/imgs/2022/05/09/ef7840b8292f968d.png)

~~~
就是说当我们用逻辑与 and 时，and 11011111时，和1与的是原来的数，和0与的一定是0，这样就把小写b变成大写B
反过来，用逻辑或 or 时， or 00100000时，和0或一定是原来的数，和1或一定是1，这样就把大写的B变成了小写的b
~~~

![](https://s3.bmp.ovh/imgs/2022/05/09/ab3fab8374469ddf.png)

![](https://s3.bmp.ovh/imgs/2022/05/09/38f4c57edbf0031c.png)

![](https://s3.bmp.ovh/imgs/2022/05/09/cf74daa2999f56b3.png)

~~~assembly
几种寄存器复习：

通用寄存器AX,BX,CX,DX			 可以看成是两个8位的寄存器构成
指针寄存器：SP					栈顶指针寄存器
指令指针寄存器：IP			
段寄存器：CS,SS,DS,ES			这里CS就是代码段寄存器，DS是数据段寄存器，SS是栈段寄存器
变址寄存器：SI,DI					SI源变址寄存器，DI目标变址寄存器
~~~

![](https://s3.bmp.ovh/imgs/2022/05/09/c446d1dbde1d06ac.png)

![](https://s3.bmp.ovh/imgs/2022/05/09/57c5379dcbc90058.png)

![](https://s3.bmp.ovh/imgs/2022/05/09/efdc4ccf3252f3ca.png)

![](https://s3.bmp.ovh/imgs/2022/05/09/3c0bed98d0259ce4.png)

![](https://s3.bmp.ovh/imgs/2022/05/09/e71644ebca849ff5.png)

![](https://s3.bmp.ovh/imgs/2022/05/09/a78ae21e5e42f65f.png)

![](https://s3.bmp.ovh/imgs/2022/05/09/b48543ecc920cb0c.png)

![](https://s3.bmp.ovh/imgs/2022/05/09/fbf7d8beb92911eb.png)

![](https://s3.bmp.ovh/imgs/2022/05/09/6b55c295896db40b.png)

![](https://s3.bmp.ovh/imgs/2022/05/09/60d9c423949b859a.png)

![](https://s3.bmp.ovh/imgs/2022/05/09/04b8bc1e32588ef0.png)

![](https://s3.bmp.ovh/imgs/2022/05/09/80e5a5e1cf144276.png)

![](https://s3.bmp.ovh/imgs/2022/05/09/471c434cd1fd2311.png)

[![OJUrtA.png](https://s1.ax1x.com/2022/05/09/OJUrtA.png)](https://imgtu.com/i/OJUrtA)

~~~
这里bx bp都可以作基址寄存器
~~~

[![OJat4s.png](https://s1.ax1x.com/2022/05/09/OJat4s.png)](https://imgtu.com/i/OJat4s)

[![OJaL8I.png](https://s1.ax1x.com/2022/05/09/OJaL8I.png)](https://imgtu.com/i/OJaL8I)

[![OJwf0O.png](https://s1.ax1x.com/2022/05/09/OJwf0O.png)](https://imgtu.com/i/OJwf0O)

[![OJ02Cj.png](https://s1.ax1x.com/2022/05/09/OJ02Cj.png)](https://imgtu.com/i/OJ02Cj)[![OJ0R8s.png](https://s1.ax1x.com/2022/05/09/OJ0R8s.png)](https://imgtu.com/i/OJ0R8s)

[![OJ0xr6.png](https://s1.ax1x.com/2022/05/09/OJ0xr6.png)](https://imgtu.com/i/OJ0xr6)

[![OJ0zqK.png](https://s1.ax1x.com/2022/05/09/OJ0zqK.png)](https://imgtu.com/i/OJ0zqK)

#### 流程转移和子程序

[![OJDoh4.png](https://s1.ax1x.com/2022/05/09/OJDoh4.png)](https://imgtu.com/i/OJDoh4)

[![OJ2UPK.png](https://s1.ax1x.com/2022/05/09/OJ2UPK.png)](https://imgtu.com/i/OJ2UPK)[![OJ2Drd.png](https://s1.ax1x.com/2022/05/09/OJ2Drd.png)](https://imgtu.com/i/OJ2Drd)

[![Ot4tjf.png](https://s1.ax1x.com/2022/05/10/Ot4tjf.png)](https://imgtu.com/i/Ot4tjf)















































































汇编语言 = 汇编指令（机器码的助记符） + 伪指令（编译器执行）+ 其他符号（编译器识别）

想要一个cpu运作，那么必须提供指令和数据，而指令和数据放在存储器，内存是主要的存储器

存储器被划分为若干存储单元，每个存储单元从0开始顺序编号

cpu和外部交流三部分：

1. 存地址信息：存储单元的地址
2. 控制信息：读或写命令
3. 数据信息：读或写数据

同样就有三种总线

[![H1aiPs.png](https://s4.ax1x.com/2022/02/08/H1aiPs.png)](https://imgtu.com/i/H1aiPs)

[![H1dpy6.png](https://s4.ax1x.com/2022/02/08/H1dpy6.png)](https://imgtu.com/i/H1dpy6)

##### 寄存器

cpu内部由：运算器，控制器，寄存器组成

8086CPU有14个寄存器：AX、BX、CX、DX、SI、DI、SP、BP、IP、CS、SS、DS、ES、PSW都是16位

- 字节：记为byte，一个字节由8个bit组成，可以存在8位寄存器中。
- 字：记为word，一个字由两个字节组成，可以存在一个16位寄存器中(16位CPU)

8086采用小端模式：高地址存放高位字节，低地址存放低位字节

[![H1dlTg.png](https://s4.ax1x.com/2022/02/08/H1dlTg.png)](https://imgtu.com/i/H1dlTg)

##### 通用寄存器

通用寄存器：通常用来存放一般性的数据，有AX、BX、CX、DX，它们可分为两个可独立使用的8位寄存器，

16位	8高位	8低位
AX	AH	AL
BX	BH	BL
CX	CH	CL
DX	DH	DL
在进行数据传送或运算时，要注意指令的两个操作对象的位数应当是一致的

一个8位寄存器所能存储的数据范围是0 ~ 2^8-1。

##### 3、8086CPU给出物理地址的方法

8086CPU有20位地址总线，可以传送20位地址，达到1MB寻址能力。
8086CPU又是16位结构，在内部一次性处理、传输、暂时存储的地址为16位。
从8086CPU的内部结构来看，如果将地址从内部简单地发出，那么它只能送出16位的地址，表现出的寻址能力只有64KB。
8086CPU采用一种在内部用两个16位地址合成的方法来形成一个20位的物理地址。

[![H10hJH.png](https://s4.ax1x.com/2022/02/08/H10hJH.png)](https://imgtu.com/i/H10hJH)

地址加法器采用物理地址 = 段地址×16 + 偏移地址的方法用段地址和偏移地址合成物理地址

例如，8086CPU要访问地址为`123C8H`的内存单元，`1230H`左移一位(空出4位)加上`00C8H`合成`123C8H`

[![H1cuXd.png](https://s4.ax1x.com/2022/02/08/H1cuXd.png)](https://imgtu.com/i/H1cuXd)

基础地址+偏移地址=物理地址

##### 段寄存器

段寄存器：8086CPU有4个段寄存器：`CS、DS、SS、ES`，提供内存单元的段地址。

CS为代码段寄存器，IP为指令指针寄存器，CPU将CS、IP中的内容当作指令的段地址和偏移地址,用它们合成指令的物理地址，CPU将CS:IP指向的内容当作指令执行。(即PC)

[![H1WJVs.png](https://s4.ax1x.com/2022/02/08/H1WJVs.png)](https://imgtu.com/i/H1WJVs)

8086CPU的工作过程简要描述

1. 从CS:IP指向的内存单元读取指令，读取的指令进入指令缓冲器；
2. IP=IP+所读取指令的长度，从而指向下一条指令；
3. 执行指令。转到步骤1，重复这个过程。

在8086CPU加电启动或复位后（即CPU刚开始工作时）CS和IP被设置为CS=FFFFH，IP=0000H，即在8086PC机刚启动时，FFFF0H单元中的指令是8086PC机开机后执行的第一条指令。

8086CPU提供转移指令修改CS、IP的内容。

jmp 段地址:偏移地址：用指令中给出的段地址修改CS，偏移地址修改IP。如：jmp 2AE3:3

jmp 某一合法寄存器：仅修改IP的内容。如：**jmp ax**。在含义上好似：**mov IP，ax**

8086CPU不支持将数据直接送入段寄存器的操作，这属于8086CPU硬件设计



































