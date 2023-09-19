![](https://s3.bmp.ovh/imgs/2023/03/23/5e1e691c04c4a3c9.png)

寄存器

~~~
mov EAX,1	将1放在32位的寄存器中，用0补位

~~~



##### 修改寄存器的值

![](https://s3.bmp.ovh/imgs/2023/03/23/06f23b17f536b914.png)

在这里尝试修改EAX的值

~~~
mov EAX,123
~~~

![](https://s3.bmp.ovh/imgs/2023/03/23/670b5d16329f62fe.png)

10进制的123就是16进制的7B

![](https://s3.bmp.ovh/imgs/2023/03/23/e14b9d60f5a10e4f.png)

##### 修改内存的值

![](https://s3.bmp.ovh/imgs/2023/03/23/fa31791bc44fc505.png)

~~~
mov dword ptr ds:[0x61FFF0],12345678
~~~

![](https://s3.bmp.ovh/imgs/2023/03/23/afce0bb0275ee4d1.png)

![](https://s3.bmp.ovh/imgs/2023/03/23/acfd33586321d86d.png)

##### 将寄存器里的值写入内存

~~~
mov dword ptr ds:[0x61FFF0],EAX
~~~

![](https://s3.bmp.ovh/imgs/2023/03/23/d9d0c6ceaf2edd28.png)

![](https://s3.bmp.ovh/imgs/2023/03/23/858c1f01d718508a.png)

##### 将内存的值存在寄存器

~~~
mov EBX,dword ptr ds:[0x61FFF0]
~~~

![](https://s3.bmp.ovh/imgs/2023/03/23/003d88221bc7dddc.png)

![](https://s3.bmp.ovh/imgs/2023/03/23/d1c7fb748850993e.png)









##### 例如mov指令

![d19c91404e89c54a0c80e473946c0d86.png](https://s1.imagehub.cc/images/2023/01/01/d19c91404e89c54a0c80e473946c0d86.png)

![b2250ba54bab2117bad093def020264a.png](https://s1.imagehub.cc/images/2023/01/01/b2250ba54bab2117bad093def020264a.png)

![6a6aabc778ba798925b53b68ca3e92b1.png](https://s1.imagehub.cc/images/2023/01/01/6a6aabc778ba798925b53b68ca3e92b1.png)







[![2c3f4dcda000a1da334d91371699bb7e.png](https://s1.imagehub.cc/images/2023/01/02/2c3f4dcda000a1da334d91371699bb7e.png)](https://www.imagehub.cc/image/0Ydnj)

这里8位寄存器，AL，CL，DL，BL这四个是16位寄存器的低八位，而AH CH DH  BH是高八位寄存器

![08d97dfc68addd5d320d0b0cb9bde442.png](https://s1.imagehub.cc/images/2023/01/02/08d97dfc68addd5d320d0b0cb9bde442.png)

每个进程都有的4GB是虚拟内存，当用的时候才会加载入物理内存

~~~
mov dword ptr ds:[0x00CFF7E0], 0X12345678
~~~



![c14973de82ffa79e7f76592819bb0c1a.png](https://s1.imagehub.cc/images/2023/01/02/c14973de82ffa79e7f76592819bb0c1a.png)

![3388799beac37e1b3d26d2ead0a06f27.png](https://s1.imagehub.cc/images/2023/01/02/3388799beac37e1b3d26d2ead0a06f27.png)

![a04bda28e944e728e42833d9759ad052.png](https://s1.imagehub.cc/images/2023/01/02/a04bda28e944e728e42833d9759ad052.png)

![138c53006500b5ce2a77ae87addd4cf3.png](https://s1.imagehub.cc/images/2023/01/02/138c53006500b5ce2a77ae87addd4cf3.png)

![571f05b4dc3f5e6802f2835c4ab1937f.png](https://s1.imagehub.cc/images/2023/01/02/571f05b4dc3f5e6802f2835c4ab1937f.png)

![30d0007de3f934d303e7fa8f536043a4.png](https://s1.imagehub.cc/images/2023/01/02/30d0007de3f934d303e7fa8f536043a4.png)



这里我们

~~~
mov EDX,dword ptr ds:[ECX+4]
~~~

![](https://s3.bmp.ovh/imgs/2023/03/23/665f9cd10526fbff.png)

这里我们就是通过将ECX + 4的地址0061ECC8的值0061F35C放在EDX寄存器中，执行

![](https://s3.bmp.ovh/imgs/2023/03/23/cc973b8001b67b21.png)







![d713e34108d7a00380b4ed06a936e46c.png](https://s1.imagehub.cc/images/2023/01/02/d713e34108d7a00380b4ed06a936e46c.png)



![3612374551919fd52c0a9a97b4d2848a.png](https://s1.imagehub.cc/images/2023/01/02/3612374551919fd52c0a9a97b4d2848a.png)

##### 部分指令

~~~
mov		赋值
add		加法	add eax,ebx //eax = eax + ebx
sub		减法
and		与
or		或
xor		异或
not		非
movs	可以从内存到内存	movs byte ptr es:[edi],byte ptr ds:[esi],每次执行完esi和edi加1地址
stos	stos byte ptr es:[edi]
rep
pop
push
jmp
call
~~~





![250852ed29f45bff1fb18bf202957ae9.png](https://s1.imagehub.cc/images/2023/01/02/250852ed29f45bff1fb18bf202957ae9.png)

![f9b73b78e4153e307662889669e3e82c.png](https://s1.imagehub.cc/images/2023/01/02/f9b73b78e4153e307662889669e3e82c.png)![fd0999524315deaecfecde8ff999fcfc.png](https://s1.imagehub.cc/images/2023/01/02/fd0999524315deaecfecde8ff999fcfc.png)

![708b8e7845e51c9531d5fda80d71b2d1.png](https://s1.imagehub.cc/images/2023/01/02/708b8e7845e51c9531d5fda80d71b2d1.png)

~~~
and dword ptr ds:[0x61F330],EDX
~~~

![](https://s3.bmp.ovh/imgs/2023/03/23/3c2fb76dc5214cdd.png)

![](https://s3.bmp.ovh/imgs/2023/03/23/014339139f5ce65d.png)







[![ea6fc2dba60067aa561a3ab37ed7857e.png](https://s1.imagehub.cc/images/2023/01/02/ea6fc2dba60067aa561a3ab37ed7857e.png)](https://www.imagehub.cc/image/3rfUG)

![be8453715d87de7290b3a39c3c10de9d.png](https://s1.imagehub.cc/images/2023/01/02/be8453715d87de7290b3a39c3c10de9d.png)





![be30d61eaef93deac3846b4222282b1d.png](https://s1.imagehub.cc/images/2023/01/02/be30d61eaef93deac3846b4222282b1d.png)

![689341029e0688f6c7e7e220f0b9c9d9.png](https://s1.imagehub.cc/images/2023/01/02/689341029e0688f6c7e7e220f0b9c9d9.png)

~~~
MOV指令和 MOVS指令区别：
MOV指令适用于，寄存器和寄存器，内存和寄存器，寄存器和内存；但是不能从内容到内容
MOVS指令适用于内存和内存
~~~

~~~
movs byte ptr es:[edi],byte ptr ds:[esi]
~~~

![](https://s3.bmp.ovh/imgs/2023/03/23/e7aae05536c5193d.png)

执行后

![](https://s3.bmp.ovh/imgs/2023/03/23/2707f6d9191afae1.png)

发现ESI和EDI加1，下面的地址内容也变了

====

标志寄存器EFL

DF位：如果DF位为0，则ESI和EDI执行完+1，如果为1，则-1

![](https://s3.bmp.ovh/imgs/2023/03/23/9d43f2581a30883f.png)

~~~
movs dword ptr es:[edi],dword ptr ds:[esi]
~~~

[![ppwgf4x.png](https://s1.ax1x.com/2023/03/23/ppwgf4x.png)](https://imgse.com/i/ppwgf4x)

这里我们DF是0，且是DWORD，在执行后应该自加4

执行之后，

[![ppwgovD.png](https://s1.ax1x.com/2023/03/23/ppwgovD.png)](https://imgse.com/i/ppwgovD)

stop

[![ppwgx8f.png](https://s1.ax1x.com/2023/03/23/ppwgx8f.png)](https://imgse.com/i/ppwgx8f)

这里如果我们是

~~~
stos byte ptr es:[edi]
那么会将al的值取出来放在edi中
stos word ptr es:[edi]
那么会将ax的值取出来放在edi中
stos dword ptr es:[edi]
那么会将eax的值取出来放在edi中
~~~

[![ppw2jeJ.png](https://s1.ax1x.com/2023/03/23/ppw2jeJ.png)](https://imgse.com/i/ppw2jeJ)

这样执行的话，就是将al放在edi地址所指向的地方

后两位变成21，且edi+1

[![ppwRVwd.png](https://s1.ax1x.com/2023/03/23/ppwRVwd.png)](https://imgse.com/i/ppwRVwd)

重复执行

[![ppwR06U.png](https://s1.ax1x.com/2023/03/23/ppwR06U.png)](https://imgse.com/i/ppwR06U)

这样就是令ecx = 10之后，执行movsd指令16次，然后执行

[![p9UfnMR.png](https://s1.ax1x.com/2023/05/06/p9UfnMR.png)](https://imgse.com/i/p9UfnMR)







##### 3.堆栈

![6c4c9307ea2aba034895fdc2b4294612.png](https://s1.imagehub.cc/images/2023/01/02/6c4c9307ea2aba034895fdc2b4294612.png)

~~~http
堆栈是特殊的内存
~~~

ESP寄存器：栈指针寄存器

[![ppwfTJI.png](https://s1.ax1x.com/2023/03/23/ppwfTJI.png)](https://imgse.com/i/ppwfTJI)

~~~
mov dword ptr ds:[0x0061F990],1
mov dword ptr ds:[0x0061F98C],2
~~~

执行

[![ppwh9Wq.png](https://s1.ax1x.com/2023/03/23/ppwh9Wq.png)](https://imgse.com/i/ppwh9Wq)

当我们的堆栈用完之后要告诉程序我们用到那里了

（堆栈是从大地址到小地址），现在的栈顶已经指向了8C那个位置，那么我们需要通过一个指令修改ESP的值

~~~
sub esp,8
~~~

[![ppwhJTH.png](https://s1.ax1x.com/2023/03/23/ppwhJTH.png)](https://imgse.com/i/ppwhJTH)[![ppwhdpt.png](https://s1.ax1x.com/2023/03/23/ppwhdpt.png)](https://imgse.com/i/ppwhdpt)

push指针

如果我们执行

~~~
push EAX
~~~

[![ppw4k9I.png](https://s1.ax1x.com/2023/03/23/ppw4k9I.png)](https://imgse.com/i/ppw4k9I)

那么栈顶就会-4，eax的值就会被压入栈顶

[![ppw4nHg.png](https://s1.ax1x.com/2023/03/23/ppw4nHg.png)](https://imgse.com/i/ppw4nHg)



pop指令

~~~
pop eax
~~~

[![ppw5CrT.png](https://s1.ax1x.com/2023/03/23/ppw5CrT.png)](https://imgse.com/i/ppw5CrT)4







##### 4.修改EIP指令

~~~http
EIP寄存器存储的是CPU下一次要执行的地址
这个所存储的是什么，CPU下一次就会找这个地址
~~~

~~~http
用jmp指令修改EIP，jmp 下一次跳转到
~~~

![dfe32dd7ebf575346fdb989b8ff45e58.png](https://s1.imagehub.cc/images/2023/01/02/dfe32dd7ebf575346fdb989b8ff45e58.png)

~~~
jmp 000000001
jmp near eax

~~~

call指令

~~~
摁F7
call 0x0061ED88
将0x0061ED88存在EIP，把当前指令的下一行地址存在了堆栈中
~~~

[![ppwTUQ1.png](https://s1.ax1x.com/2023/03/23/ppwTUQ1.png)](https://imgse.com/i/ppwTUQ1)

执行后，77115D2B被压入到堆栈中

[![ppwTdL6.png](https://s1.ax1x.com/2023/03/23/ppwTdL6.png)](https://imgse.com/i/ppwTdL6)

[![ppwTWOP.png](https://s1.ax1x.com/2023/03/23/ppwTWOP.png)](https://imgse.com/i/ppwTWOP)





[![ppwThef.png](https://s1.ax1x.com/2023/03/23/ppwThef.png)](https://imgse.com/i/ppwThef)

~~~
该指令把当前栈顶里的值放在了EIP，同时栈顶指针+4
~~~





##### 反调试Fake

~~~
不让我们轻易的分析程序
~~~

断点的功能

    程序执行到断点就会停下来，其实当我们把exe拖进来之后，程序停止时的EIP中的地址就是一个断点。
    3、断点的本质是什么？
    断点的本质其实是指令INT 3，当CPU执行的时候，见到INT 3 的时候就会停下来，停在调试器里。
    当我们F2设置断点的时候，那么这一行汇编对应的机器码的第一个字节就已经被改写为 0xCC ，尽管调试器不会显示，但是实际上已经进行了更改，调试器在执行到这里检测到了 0xCC 之后就会断下。（0xCC是int 3 的机器码。)
 我们就是可以通过F8一下子会执行完，但F7是一步一步的执行这一特性，如果说F8执行CALL开始到RET返回的中间，堆栈中的值做了修改，那么RET的时候，返回的就是堆栈中此时的地址，这样的话你就做到了反调试。

        比如：CALL 0x233244，然后这条指令所在的地方假如是：0x233000，假设它的下一个指令地址是：0x233001。
    
        那么它会跳到233244这个地址，执行233244地址中的指令 ，然后将233001存入堆栈中，以便之后的返回。假设此时233244地址中的指令是：mov dword ptr ds:[esp],11223344H
    
        那么他就会修改堆栈中的值为11223344，这个时候如果执行到RET指令，就会跳转到11223344地址去执行指令，这个时候就到了人生地不熟的地方了，调试员也会懵逼，这就做到了最简单的反调试。


##### 函数

~~~‘
通过cal和ret指令调用函数并返回
~~~



~~~asm
mov ecx,1
mov edx,2
call 0x77121F19

add ecx,edx
mov eax,ecx
ret

77121F19
~~~

[![ppwHCE8.png](https://s1.ax1x.com/2023/03/23/ppwHCE8.png)](https://imgse.com/i/ppwHCE8)

f8执行之后

[![ppwHA3j.png](https://s1.ax1x.com/2023/03/23/ppwHA3j.png)](https://imgse.com/i/ppwHA3j)

这里就是执行加法函数，用call跳转到函数地址，最后ret返回

##### 使用堆栈来传递参数

~~~assembly
我们尝试对5个数进行相加
push 1
push 2
push 3
push 4
push 5
~~~

[![p9UoB8O.png](https://s1.ax1x.com/2023/05/06/p9UoB8O.png)](https://imgse.com/i/p9UoB8O)

这里我们push了之后再写函数体内容，此时我们的ESP是008FF26C，我们需要将压入到堆栈中的12345相加，所以

###### ESP寻址

~~~assembly
mov eax,dword ptr ds:[esp+4]
add eax,dword ptr ds:[esp+8]
add eax,dword ptr ds:[esp+C]
add eax,dword ptr ds:[esp+10]
add eax,dword ptr ds:[esp+14]
ret
~~~

然后call指令调用

~~~
call 0x77891F17
~~~

执行完后，eax成功被修改为0x4 + 0x8 + 0xC + 0xA + 0xE = 

[![p9UocqA.png](https://s1.ax1x.com/2023/05/06/p9UocqA.png)](https://imgse.com/i/p9UocqA)











##### 堆栈平衡

[![p9UT0wn.png](https://s1.ax1x.com/2023/05/06/p9UT0wn.png)](https://imgse.com/i/p9UT0wn)

外平栈：在函数外部平衡堆栈

~~~assembly
push 1
push 2
call 0x00418407
add esp+8

mov eax,dword ptr ds:[esp+4]
add eax,dword ptr ds:[esp+8]
ret
~~~







内平栈：在函数内部平衡堆栈

~~~assembly
push 1
push 2
call 0x00418407

mov eax,dword ptr ds:[esp+4]
add eax,dword ptr ds:[esp+8]
ret 8
~~~



##### 外挂

跨进程内存读取

~~~c
#include "stdlib.h"
#include "stdio.h"
#include "windows.h"

void Attack()
{
    printf("******************攻击******************\n");
    return;
}

int main() {
    printf("回车开始:\n");
    getchar();
    while (1)
    {
        Attack();
        Sleep(3000);
    }
    getchar();
    return 0;
}
~~~

这里编译好之后放在dbg中



##### EBP寻址

[![p9ULJKO.png](https://s1.ax1x.com/2023/05/06/p9ULJKO.png)](https://imgse.com/i/p9ULJKO)

通过EBP寻址是为了解决通过ESP寻址导致的栈顶数据的修改，如果我们通过EBP寻址如上图

~~~assembly
push 1
push 2
call 0x12345678
add esp,8

push ebp
mov ebp,esp
sub esp,10
mov eax,dword ptr ss:[ebp+8]
mov eax,dword ptr ss:[ebp+C]
mov esp,ebp
pop ebp
ret
~~~

[![p9UOGYn.png](https://s1.ax1x.com/2023/05/06/p9UOGYn.png)](https://imgse.com/i/p9UOGYn)



##### JCC指令

能够修改EIP寄存器的指令有jmp，call，ret

[![p9UjFPA.png](https://s1.ax1x.com/2023/05/06/p9UjFPA.png)](https://imgse.com/i/p9UjFPA)[![p9UjmqS.png](https://s1.ax1x.com/2023/05/06/p9UjmqS.png)](https://imgse.com/i/p9UjmqS)

[![p9UjlPs.png](https://s1.ax1x.com/2023/05/06/p9UjlPs.png)](https://imgse.com/i/p9UjlPs)

标志寄存器EFLAGS

[![p9Uj1Gn.png](https://s1.ax1x.com/2023/05/06/p9Uj1Gn.png)](https://imgse.com/i/p9Uj1Gn)

###### CF位

当无符号数发现进位或者借位的时候将其置1，反之为0

mov指令不会影响标志寄存器，加减乘除的指令会影响标志寄存器

用来标识无符号整型运算的溢出状态

例如

~~~assembly
//进位
mov al,0xFE
add al,2
//借位
mov al,0x7F
sub al,0xFF
~~~

[![p9Uj6sK.png](https://s1.ax1x.com/2023/05/06/p9Uj6sK.png)](https://imgse.com/i/p9Uj6sK)

我们这里执行完这个之后进位，CF变成1

###### PF位

用于传输过程中的奇偶校验

如果结果的最低有效字节包含偶数个1位则为1，否则清0

~~~
mov ax,0x113
add ax,0x1
~~~

执行完之后

[![p9UjTQP.png](https://s1.ax1x.com/2023/05/06/p9UjTQP.png)](https://imgse.com/i/p9UjTQP)

[![p9Uj7sf.png](https://s1.ax1x.com/2023/05/06/p9Uj7sf.png)](https://imgse.com/i/p9Uj7sf)

~~~
因为113(16进制) + 1(16进制)
0000 0001 0001 0011
0000 0000 0000 0001
0000 0001 0001 0100
结果里的低字节位是0001 0100，只有奇数个1
~~~

###### AF位

[![p9UvEFJ.png](https://s1.ax1x.com/2023/05/06/p9UvEFJ.png)](https://imgse.com/i/p9UvEFJ)

###### ZF位

[![p9UvnQx.png](https://s1.ax1x.com/2023/05/06/p9UvnQx.png)](https://imgse.com/i/p9UvnQx)

[![p9UvlwD.png](https://s1.ax1x.com/2023/05/06/p9UvlwD.png)](https://imgse.com/i/p9UvlwD)

###### SF位

[![p9Uv1Te.png](https://s1.ax1x.com/2023/05/06/p9Uv1Te.png)](https://imgse.com/i/p9Uv1Te)

###### OF位

[![p9UvtSI.png](https://s1.ax1x.com/2023/05/06/p9UvtSI.png)](https://imgse.com/i/p9UvtSI)

###### DF位

[![p9UvU6P.png](https://s1.ax1x.com/2023/05/06/p9UvU6P.png)](https://imgse.com/i/p9UvU6P)

















