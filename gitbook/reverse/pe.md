这里每一个字符是16进制（0-E），可以表示4位2进制

![4344ecb7f24e22446768838ae5dc27f8.png](https://s1.imagehub.cc/images/2023/03/15/4344ecb7f24e22446768838ae5dc27f8.png)

在ascii中，8位2进制可以表示一个字符，故两位字符表示8位二进制，表示一个字符

![634658292f8cc51b975ec4e26e28d0d1.png](https://s1.imagehub.cc/images/2023/03/15/634658292f8cc51b975ec4e26e28d0d1.png)







### PE

#### 1.pe文件结构

~~~
windows		pe文件结构
linux		elf文件结构
~~~

![](https://s3.bmp.ovh/imgs/2022/11/09/6f9bfd24b279b3c4.png)

例如我用clion生成的文件batman.exe，用010ediotr打开

~~~c
#include "stdlib.h"

int main(){
    system("pause");
    system("ipconfig /all");
    system("pause");
}
~~~

前两个字节必是MZ

![](https://s3.bmp.ovh/imgs/2022/11/09/4652dab83ce34368.png)

在3C的位置，我们去找这个对应的80

![](https://s3.bmp.ovh/imgs/2022/11/09/81530f1bbfaaae7a.png)

80位置的字节对应的就是PE，代表这是一个可执行文件

![](https://s3.bmp.ovh/imgs/2022/11/09/ebc79cab767d1d91.png)

PE指纹。

![](https://s3.bmp.ovh/imgs/2022/11/09/e1b159cfb7167d9d.png)

~~~
1）dos部分：给dos用的
2）pe文件头：给windows用的
3）节表：决定节数据的内容（说明书）
4）节数据：真正的内容
~~~



#### 2.pe文件的两种状态

1）DOS部分

![](https://s3.bmp.ovh/imgs/2022/11/09/70773c331402a12f.png)

~~~
在windows上的winnt.h文件上，有_IMAGE_DOS_HEADER
~~~

![](https://s3.bmp.ovh/imgs/2022/11/09/a6fc9a58b4170459.png)

一个exe文件运行前和运行后的pe格式有差别

DOS MZ文件头是64个字节，如图

![](https://s3.bmp.ovh/imgs/2022/11/09/0fc9f02ff36c0654.png)

DOS Stub（DOS块）大小不确定，是给连接器用的，连接器会插入自己的数据，删掉也不会影响程序的运行



DOS MZ文件头的最后四个字节指向了PE文件头的开始位置

![](https://s3.bmp.ovh/imgs/2022/11/09/6dc276870cd5d72d.png)

![](https://s3.bmp.ovh/imgs/2022/11/09/7f3e7d316f2bd135.png)![](https://s3.bmp.ovh/imgs/2022/11/09/b1324869d53f5582.png)

那么Dos块就是两者之间的部分

![](https://s3.bmp.ovh/imgs/2022/11/09/1b221fa7c7d5f5fc.png)



2）PE文件头

![](https://s3.bmp.ovh/imgs/2022/11/09/2edab344ac1c6b72.png)

~~~http
    typedef struct _IMAGE_NT_HEADERS {
      DWORD Signature;				#	四个字节的文件头
      IMAGE_FILE_HEADER FileHeader;		#	子结构体（标准PE头）
      IMAGE_OPTIONAL_HEADER32 OptionalHeader;		#	子结构体（扩展PE头）
    } IMAGE_NT_HEADERS32,*PIMAGE_NT_HEADERS32;
~~~

标准PE头有20个字节

![](https://s3.bmp.ovh/imgs/2022/11/09/7ca1fdf9be5ddff9.png)



扩展PE头如果是32位，那么长度是224字节

![](https://s3.bmp.ovh/imgs/2022/11/09/5bcf510a87c62348.png)

因为这个有个数组，所以大，数组的每个成员是结构体。

在标准PE头中有一个规定扩展PE头大小的（2个字节）

![](https://s3.bmp.ovh/imgs/2022/11/09/d84fd61357641fb5.png)

![](https://s3.bmp.ovh/imgs/2022/11/09/fbbe8114dde7e7f4.png)

32位的是E0（224），64位的是F0（240）

那么向后找240个字节就是64位的扩展PE头

![](https://s3.bmp.ovh/imgs/2022/11/09/573116c295a48f39.png)

3）节表

![](https://s3.bmp.ovh/imgs/2022/11/09/5ba08051f91cae1e.png)

节表里的每个成员是40个字节

第一个成员.text

![](https://s3.bmp.ovh/imgs/2022/12/30/41d3871c37484345.png)

第二个成员.data

![](https://s3.bmp.ovh/imgs/2022/11/09/87ffd546202f9199.png)

第三个成员.rdata

![](https://s3.bmp.ovh/imgs/2022/12/30/2223b1b6786fcef6.png)

第四个成员.pdata

![](https://s3.bmp.ovh/imgs/2022/12/30/32b1f5f9ad144cc2.png)

第五成员.xdata

![](https://s3.bmp.ovh/imgs/2022/12/30/41c225da1df31f38.png)

。。。

再之后就是编译器插入的垃圾其他数据，四个节表成员代表四组数据

扩展PE头有一个成员

![](https://s3.bmp.ovh/imgs/2022/12/30/6b4c9953828d5765.png)

这个头指的是DOS 头 + PE 头 + 节表，按照文件对其以后的大小

假设1 + 2 + 3的大小是302个字节

那么SizeOfheaders的大小是在满足这个之后又必须被“文件对其”之后的大小的整数倍，如下

![](https://s3.bmp.ovh/imgs/2022/12/30/219c4967a884136e.png)

假设这个filealignment是200字节那么我的实际大小就是400字节，是它的整数倍大于302，为了执行的时候效率更高

![](https://s3.bmp.ovh/imgs/2022/12/30/219c4967a884136e.png)

这里我们在标准PE头之后查2 + 1 * 2 + 8 * 4 = 36 个字节之后就是filealignment

![](https://s3.bmp.ovh/imgs/2022/12/30/219c4ae1236d2d8b.png)

![](https://s3.bmp.ovh/imgs/2022/12/30/7aafbcc8e1f458c4.png)

这里就是filealignment是10进制的200

那么我们再去找sizeofheader，从文件对其之后再找20个字节

![](https://s3.bmp.ovh/imgs/2022/12/30/9fff202982cd1d08.png)

![](https://s3.bmp.ovh/imgs/2022/12/30/5649f14ce1dbacc8.png)

也就是十进制的400，必须是200的整数倍

![](https://s3.bmp.ovh/imgs/2022/12/30/24e1ae0215d33a5f.png)

这个之上都是头的大小，之下再开始就是真正的节数据了

也就是说假如.text块的大小是180字节，那么我们使得它为200字节，剩下的20字节用0填充

在硬盘中的头和在内存中的头是一样的

[![pSpHstU.png](https://s1.ax1x.com/2022/12/30/pSpHstU.png)](https://imgse.com/i/pSpHstU)

[![pSpHg1J.png](https://s1.ax1x.com/2022/12/30/pSpHg1J.png)](https://imgse.com/i/pSpHg1J)

在硬盘中的第一个节是400开始，但是在内存中的第一个节却不是400，是1000

[![pSpHzAf.png](https://s1.ax1x.com/2022/12/30/pSpHzAf.png)](https://imgse.com/i/pSpHzAf)

是因为内存对齐成员属性，在fileaglinment之上的sectionalignment规定了内存对齐

[![pSpbp4S.png](https://s1.ax1x.com/2022/12/30/pSpbp4S.png)](https://imgse.com/i/pSpbp4S)

是1000

PE的两种状态就是内存中和硬盘中的两种状态

#### 3.DOS头属性说明

[![pSpbaCD.png](https://s1.ax1x.com/2022/12/30/pSpbaCD.png)](https://imgse.com/i/pSpbaCD)[![pSpbd8e.png](https://s1.ax1x.com/2022/12/30/pSpbd8e.png)](https://imgse.com/i/pSpbd8e)

总共是64位

除了标红的这两个，其他的都是为16位服务的，现在都是32位或者64位所以已经用不到，可以删掉了

[![pSpb0vd.png](https://s1.ax1x.com/2022/12/30/pSpb0vd.png)](https://imgse.com/i/pSpb0vd)

我们把除了第一个和最后一个以外其他都变成0

另存为在桌面，运行，发现正常执行

[![pSpbDKA.png](https://s1.ax1x.com/2022/12/30/pSpbDKA.png)](https://imgse.com/i/pSpbDKA)

正常执行

[![pSpb6VP.png](https://s1.ax1x.com/2022/12/30/pSpb6VP.png)](https://imgse.com/i/pSpb6VP)

~~~http
操作系统如何判断是否是可执行文件？
前两个字节是MZ，倒数第四个字节指向的地方是PE头，所以这两处不能改动，否则不能运行

~~~

[![pSpbobq.png](https://s1.ax1x.com/2022/12/30/pSpbobq.png)](https://imgse.com/i/pSpbobq)

dos块的内容可以更改

不影响程序的运行，所以我们可以添加恶意代码shellcode进去

#### 4.标准PE头属性说明

[![pSpq4JO.png](https://s1.ax1x.com/2022/12/30/pSpq4JO.png)](https://imgse.com/i/pSpq4JO)

~~~http
    typedef struct _IMAGE_NT_HEADERS {
      DWORD Signature;		#	PE标识
      IMAGE_FILE_HEADER FileHeader;			#	标准PE头
      IMAGE_OPTIONAL_HEADER32 OptionalHeader;			#	扩展PE头
    } IMAGE_NT_HEADERS32,*PIMAGE_NT_HEADERS32;
~~~

如果更改PE标识也无法运行

标准PE头

[![pSpqBJU.png](https://s1.ax1x.com/2022/12/30/pSpqBJU.png)](https://imgse.com/i/pSpqBJU)

两个字节的machine：如果是00 00 那么就是可以运行在任何CPU上

[![pSpq2e1.png](https://s1.ax1x.com/2022/12/30/pSpq2e1.png)](https://imgse.com/i/pSpq2e1)

system64存储32位的，system32存储64位的

[![pSpqWo6.png](https://s1.ax1x.com/2022/12/30/pSpqWo6.png)](https://imgse.com/i/pSpqWo6)

numberofsections 代表有多少个节，也就是后面的0B 00 ，根据小端存储，就是000B，也就是11个节

[![pSpqTQH.png](https://s1.ax1x.com/2022/12/30/pSpqTQH.png)](https://imgse.com/i/pSpqTQH)

再往后的4个字节是timedatestamp，是编译器填写的时间戳，和创建时间修改时间无关

![](https://s3.bmp.ovh/imgs/2022/12/30/dc0395382e98e1bc.png)

~~~
6411C55F
~~~





63AEA394

http://tool.huixiang360.com/str/hex-decimal.php

https://tool.lu/timestamp/

![](https://s3.bmp.ovh/imgs/2022/12/30/5732c210ba871b20.png)

![](https://s3.bmp.ovh/imgs/2022/12/30/411cbbbdd9793172.png)

全部修改也不影响使用，然后跳过8个字节的两个字节是扩展PE头的大小

我们默认不该32位的是224字节（0xE0），64位的是240字节（0xF0）

![](https://s3.bmp.ovh/imgs/2022/12/30/7dfdbe3693f4f4b0.png)

再之后两个字节就是文件属性

![](https://s3.bmp.ovh/imgs/2022/12/30/5b2d23eb1cdf0565.png)

刚好2个字节，16位，代表每个属性26 02 就是 02 26

转2进制是0000 0010 0010 0110

根据上面那个图

~~~http
0000 0010 0010 0110
~~~

![](https://s3.bmp.ovh/imgs/2022/12/30/3abd3735b2fa7a5d.png)

这里数据位是1的，代表上图文件可执行

0000 0010 0010 0110

第1，2，5，9位可执行

![](https://s3.bmp.ovh/imgs/2022/12/30/7dfdbe3693f4f4b0.png)

这里第五位，我们知道32位的程序可处理4GB寻址空间，内核部分用了高2GB，我们只能使用低2GB，如何可以使用超过2GB的，说明这是一个64位的

#### 5.扩展PE头属性

![](https://s3.bmp.ovh/imgs/2022/12/30/e22bed193e7d0745.png)

32位的PE头和64位的PE头有差异

~~~http
    typedef struct _IMAGE_NT_HEADERS {
      DWORD Signature;
      IMAGE_FILE_HEADER FileHeader;
      IMAGE_OPTIONAL_HEADER32 OptionalHeader;
    } IMAGE_NT_HEADERS32,*PIMAGE_NT_HEADERS32;
~~~

~~~http
    typedef struct _IMAGE_NT_HEADERS64 {
      DWORD Signature;
      IMAGE_FILE_HEADER FileHeader;
      IMAGE_OPTIONAL_HEADER64 OptionalHeader;
    } IMAGE_NT_HEADERS64,*PIMAGE_NT_HEADERS64;
~~~

差别在 IMAGE_OPTIONAL_HEADER32 和 IMAGE_OPTIONAL_HEADER64

![](https://s3.bmp.ovh/imgs/2022/12/30/2b25f8bc1df294de.png)

这里最后的NumberOfRvaAndSizes 代表目录项数量，16个，乘以下面的数组长度，16张表（重点）

![](https://s3.bmp.ovh/imgs/2022/12/30/dacbe6a266fa449d.png)

~~~http
    typedef struct _IMAGE_OPTIONAL_HEADER {

      WORD Magic;
      BYTE MajorLinkerVersion;
      BYTE MinorLinkerVersion;
      DWORD SizeOfCode;
      DWORD SizeOfInitializedData;
      DWORD SizeOfUninitializedData;
      DWORD AddressOfEntryPoint;
      DWORD BaseOfCode;
      DWORD BaseOfData;
      DWORD ImageBase;
      DWORD SectionAlignment;
      DWORD FileAlignment;
      WORD MajorOperatingSystemVersion;
      WORD MinorOperatingSystemVersion;
      WORD MajorImageVersion;
      WORD MinorImageVersion;
      WORD MajorSubsystemVersion;
      WORD MinorSubsystemVersion;
      DWORD Win32VersionValue;
      DWORD SizeOfImage;
      DWORD SizeOfHeaders;
      DWORD CheckSum;
      WORD Subsystem;
      WORD DllCharacteristics;
      DWORD SizeOfStackReserve;
      DWORD SizeOfStackCommit;
      DWORD SizeOfHeapReserve;
      DWORD SizeOfHeapCommit;
      DWORD LoaderFlags;
      DWORD NumberOfRvaAndSizes;
      IMAGE_DATA_DIRECTORY DataDirectory[IMAGE_NUMBEROF_DIRECTORY_ENTRIES];
    } IMAGE_OPTIONAL_HEADER32,*PIMAGE_OPTIONAL_HEADER32;
~~~

Magic 可以判断是32位还是64位，如果转换之后是10B就是32位，是20B就是64位的

AddressOfEntryPoint 是程序的入口，一定要配合ImageBase 一起看（内存镜像基址）

这里我们ImageBase 就是我们的PE在4GB的内存中展开的初始地址，而AddressOfEntryPoint 则是我们程序开始的指向地址

[![pSpOaCT.png](https://s1.ax1x.com/2022/12/30/pSpOaCT.png)](https://imgse.com/i/pSpOaCT)

查过16个字节就是AddressOfEntryPoint程序入口 11 25（相对地址，相对ImageBase）

[![pSpOrr9.png](https://s1.ax1x.com/2022/12/30/pSpOrr9.png)](https://imgse.com/i/pSpOrr9)

[![pSpO6V1.png](https://s1.ax1x.com/2022/12/30/pSpO6V1.png)](https://imgse.com/i/pSpO6V1)



再跳过8个字节找ImageBase，就是01 00 00 00 00 10 00 00

~~~http
100000000010 + 1125 就是程序开始		00007FF775F11125
~~~

[![pSpOLPf.png](https://s1.ax1x.com/2022/12/30/pSpOLPf.png)](https://imgse.com/i/pSpOLPf)

所以程序之所以能找到入口，就是因为这个

所以加密就是不能让逆向分析人员轻易找到程序的入口，想办法藏起来



SizeOfImage 决定整个PE占整个内存的多大，一定是内存对齐的整数倍

SizeOfHeaders 是按照对齐后的大小，DOS头 + PE头 + 节表

[![pSpjSSO.png](https://s1.ax1x.com/2022/12/30/pSpjSSO.png)](https://imgse.com/i/pSpjSSO)

checksum 是校验和，判断文件是否被修改

（一个PE文件，两个字节两个字节的加再加上文件的长度就是校验和）

DllCharacteristics 是判断是否可以被DLL加载

[![pSpjF0A.png](https://s1.ax1x.com/2022/12/30/pSpjF0A.png)](https://imgse.com/i/pSpjF0A)



[![pSpjkTI.png](https://s1.ax1x.com/2022/12/30/pSpjkTI.png)](https://imgse.com/i/pSpjkTI)



#### 6.节表

[![pS9ARvn.png](https://s1.ax1x.com/2022/12/31/pS9ARvn.png)](https://imgse.com/i/pS9ARvn)

节表是结构体数组，

[![pS9Afuq.png](https://s1.ax1x.com/2022/12/31/pS9Afuq.png)](https://imgse.com/i/pS9Afuq)

这里的 union 可能比VirtualAddress 大，或者比它小

全局变量存在初始值时，加载进内存后，故union > SizeOfRawData

如果，全局变量不存在初始值时，那么这些变量在文件中不存在值，但是在加载进内存后存在值，故union < SizeOfRawData

每个结构体是40个字节

[![pS9AoUU.png](https://s1.ax1x.com/2022/12/31/pS9AoUU.png)](https://imgse.com/i/pS9AoUU)

那么前八个字节就是ascii字符串

接下来4个字节是实际数据

[![pS9AHC4.png](https://s1.ax1x.com/2022/12/31/pS9AHC4.png)](https://imgse.com/i/pS9AHC4)、

接下4个字节是内存偏移地址

[![pS9EFxA.png](https://s1.ax1x.com/2022/12/31/pS9EFxA.png)](https://imgse.com/i/pS9EFxA)

再接下来是在文件中对齐后的尺寸和偏移大小

节的属性

![](https://s3.bmp.ovh/imgs/2023/03/16/a3e711db547b113b.png)

在可执行文件的结构中，节表（Section Table）是用来描述程序执行时所需的各种数据段和代码段的位置、大小等信息的数据结构。其中，常见的节表包括：

~~~
\- .text：代码段，在这个区域存放着程序的可执行代码。
\- .data：数据段，用来存放已初始化的全局变量和静态变量。
\- .bss：数据段，用来存放未初始化的全局变量和静态变量。
\- .rdata：只读数据段，用来存放只读的全局变量和字符串常量。
~~~



~~~
// 在 .data 节表中分配空间，类型是 int，名称是 global_var
int global_var = 10;

在编译和链接之后生成的可执行文件中，就会有一个 .data 节表，其中包含了名为 global_var 的变量的初始值 10。

// 在 .rdata 节表中分配空间，内容是 "Hello, World!"
const char* str_const = "Hello, World!";
\```

那么在可执行文件中就会有一个 .rdata 节表，其中包含了字符串常量 "Hello, World!"。
~~~









#### 7.RVA和FOA的转换

如果一个全局变量有初始值，那么这个初始值一定是写在了PE文件内部，如果想改变一个全局变量的初始值，怎么做？

[![pS9EKPg.png](https://s1.ax1x.com/2022/12/31/pS9EKPg.png)](https://imgse.com/i/pS9EKPg)

![](https://s3.bmp.ovh/imgs/2022/12/31/739b7eaaf3c85b10.png)

相对虚拟地址RVA

![](https://s3.bmp.ovh/imgs/2022/12/31/72a157854552932b.png)

文件偏移地址FOA



《1》判断RVA是否在头部

FOA = RVA

《2》

RVA > = 节.VirualAddress（在内存中展开地址）

RVA  < = 节.VirualAddress + 当前节内存对齐后的大小

差值 = RVA - 节.VirualAddress

FOA = 节.PointToRawData（在文件中展开地址） + 差值

![](https://s3.bmp.ovh/imgs/2022/12/31/bff430e00d9a09aa.png)





#### 8.空白区添加代码

实现我们要在PE文件中，添加一段弹窗代码

~~~c++
#include <iostream>
using namespace std;
#include <windows.h>
int main() {
    
    MessageBoxA(0,0,0,0);
    return 0;
}
~~~

直接编译打包成32位的，利用ida查看反汇编

[![p9NgAzt.png](https://s1.ax1x.com/2023/05/05/p9NgAzt.png)](https://imgse.com/i/p9NgAzt)

反汇编之后发现这里

[![p9NgeL8.png](https://s1.ax1x.com/2023/05/05/p9NgeL8.png)](https://imgse.com/i/p9NgeL8)

这里的call是一个间接call

在dbg中，我们想要call 0x008B12AB，

[![p9NWKoD.png](https://s1.ax1x.com/2023/05/05/p9NWKoD.png)](https://imgse.com/i/p9NWKoD)

就在这里call 0x008B12AB，那么他会通过计算，用8B12AB - 8B1266 - 5

[![p9NW8SA.png](https://s1.ax1x.com/2023/05/05/p9NW8SA.png)](https://imgse.com/i/p9NW8SA)

通过运算，再减去5之后，就是40

~~~
要跳转的地址 - E8指令当前的地址(内存中) - 5
~~~



[![p9Nfncn.png](https://s1.ax1x.com/2023/05/05/p9Nfncn.png)](https://imgse.com/i/p9Nfncn)

[![p9Nf7CQ.png](https://s1.ax1x.com/2023/05/05/p9Nf7CQ.png)](https://imgse.com/i/p9Nf7CQ)

jmp





这里我们可知，push的硬编码是6A 00，call的硬编码是E8，jmp的硬编码是E9

~~~
6A 00 6A 00 6A 00 6A 00 E8 00 00 00 00 E9 00 00 00 00
~~~

一共18个字节

我们紧接着去user32.dll里面找messageboxA的地址，这里在我们的机器上，MessageboxA的地址是75B919E0

[![p9N4Co8.png](https://s1.ax1x.com/2023/05/05/p9N4Co8.png)](https://imgse.com/i/p9N4Co8)

我们要跳转的地址是 75B919E0 

我们的程序入口entrypoint的地址

~~~
要跳转的地址 - E8指令当前的地址(内存中) - 5
75B919E0 - (2F7 + IMAGEBASE) - 5
75B919E0 - 2F7 - 00400000 - 5 = 757916E4
~~~

[![p9NTt3D.png](https://s1.ax1x.com/2023/05/05/p9NTt3D.png)](https://imgse.com/i/p9NTt3D)

根据小段存储

~~~
6A 00 6A 00 6A 00 6A 00 E8 E4 16 79 75 E9 00 00 00 00
~~~

同理我们在E8的call调用了messageBoxA之后要跳回到程序入口，00001266（entrypoint），而imagebase是

00400000，所以我们实际要跳转到401266，根据公式

~~~
要跳转的地址 - E9指令当前的地址(内存中) - 5
401266 - 2FC - IMAGEBASE - 5 = 400F65 - 400000 = F65
~~~

所以后面就是E9 65 0F 00 00

~~~
6A 00 6A 00 6A 00 6A 00 E8 E4 16 79 75 E9 65 0F 00 00
~~~

这里我们的入口是2F0，我们要将程序的入口点修改为指向这里

[![p9N7wzF.png](https://s1.ax1x.com/2023/05/05/p9N7wzF.png)](https://imgse.com/i/p9N7wzF)

[![p9N7qFP.png](https://s1.ax1x.com/2023/05/05/p9N7qFP.png)](https://imgse.com/i/p9N7qFP)

这里从1266开始往后四个字节就是entrypoint

~~~
93 19 74 18
~~~

我要改为2F0

~~~
F0 02 00 00
~~~

保存之后另存为123.exe

#### 9.扩大节

![](https://s3.bmp.ovh/imgs/2022/12/31/7ee93efeebfc661a.png)

适合在最后一个节扩大节，这样不会带动其他节一起动

![](https://s3.bmp.ovh/imgs/2022/12/31/7f2f7cbcd658fc78.png)

1.在最后一个节分配新的空间

![](https://s3.bmp.ovh/imgs/2022/12/31/718dbcd479d8a1b9.png)

![](https://s3.bmp.ovh/imgs/2022/12/31/e0bd4bea7cfbac94.png)

2.修改最后一个节的sizeofrawdata和virtualsize改为N

![pS9Afuq.png](https://s1.ax1x.com/2022/12/31/pS9Afuq.png)

![](https://s3.bmp.ovh/imgs/2022/12/31/41cab858ee697178.png)

这里是在文件中的偏移大于在内存中的偏移地址，如果我们插入数据，使得节的大小变成1000，那么文件这里的偏移就应该是其整数倍，500 ，改为 00 05 00 00

而同样的，可以同步内存和文件的偏移大小，所以也可以改为00 05 00 00

3.找到sizeofimage

SizeOfImage 决定整个PE占整个内存的多大，一定是内存对齐的整数倍



4.最后可能改一下节的属性，让节具备可执行权限

![](https://s3.bmp.ovh/imgs/2023/03/16/de1f0af46c7c3e12.png)







#### 10.新增节

~~~http
为什么增加节，比如如果只是扩大节，而原来的节只具备读的功能，那么我们会相应的修改他的功能
就像是租房子，分开住最好
想租成功，就必须让节表剩余的空余部分可以容纳40个字节，即一个节的大小
~~~

![](https://s3.bmp.ovh/imgs/2022/12/31/891c84b3ee767e5f.png)

有11个节

![](https://s3.bmp.ovh/imgs/2022/12/31/8e0dc7973220d574.png)

在这里第11个节之后结束，第12个节开始可以插入40个字节

我们这里对第一个节.text进行复制过去

[![pS9Afuq.png](https://s1.ax1x.com/2022/12/31/pS9Afuq.png)

复制过去之后，原来的是11个节，现在是12个节，所以我们把标准PE头里面节的数量加一

![](https://s3.bmp.ovh/imgs/2022/12/31/75dbd84a306a1346.png)

前8个字节是名字，我们改成.tttt



![](https://s3.bmp.ovh/imgs/2022/12/31/45d351d3c7ef9bc6.png)

最后一个节的大小是88，实际对齐大小是200

#### 11.合并表

无音源

#### 12.导出表

一个可执行程序是由一个或多个PE文件组合的

![](https://s3.bmp.ovh/imgs/2022/12/31/dd674229c6e4bfd2.png)

如图我们执行了main.exe这个可执行文件，紧接着调用了很多dll文件

在任何一个PE文件内都有一张导入表，这张表记录了导入的其他的PE文件的名字，地址，函数等

通常情况下.exe不提供导出表，即不提供函数给别人用，但是并不代表不能提供函数给别人用

而.dll文件通常既有导入表也有导出表，即提供函数给别人用，又内部自身调用其他dll

![](https://s3.bmp.ovh/imgs/2022/12/31/af730a469f0b61e9.png)

在扩展PE头的最后一部分是16个数组（16 * 8）的结构体，包含导入表和导出表

![](https://s3.bmp.ovh/imgs/2022/12/31/6dcf220d0768737e.png)

这里的每个结构体包含两个4字节的（导入表的地址和大小）



1）找到标准PE头这里，发现是11个节

![](https://s3.bmp.ovh/imgs/2022/12/31/527bbc47f95dbdea.png)



2）倒着在第一个节上面找第一个结构体

![](https://s3.bmp.ovh/imgs/2022/12/31/6e83f080b1c42f52.png)

共计16个即第一组结构体的值就是 00 00 00 00 00 00 00 00，因为exe是自己用的，没有导出表

如果有值，如下

![](https://s3.bmp.ovh/imgs/2022/12/31/c6975e7b463f259e.png)

去找5A 90，大小是16进制的67

![c1b73293ff65214e96f917540b59bb9f.png](https://s1.imagehub.cc/images/2022/12/31/c1b73293ff65214e96f917540b59bb9f.png)

##### 导出表结构体

![28bc9b892a558201c88c0b46ae908f5f.png](https://s1.imagehub.cc/images/2022/12/31/28bc9b892a558201c88c0b46ae908f5f.png)

共40个字节

而原有的是90 5A 00 00 67 00 00 00

其中67转10进制就是103，明显大于40

![4cc70b15088265c54dd5777f65d27a4a.png](https://s1.imagehub.cc/images/2022/12/31/4cc70b15088265c54dd5777f65d27a4a.png)

故如上加起来

![891bf119cc7c22042d4e72a86cbc34bd.png](https://s1.imagehub.cc/images/2022/12/31/891bf119cc7c22042d4e72a86cbc34bd.png)

这里时间戳和上面的时间戳一样

name = 00 00 51 DE

导出函数的个数5个

以函数名字导出的个数3个

故有2个函数用序号导出的

![7d0274bc8dc0ff227ed3570696b50452.png](https://s1.imagehub.cc/images/2022/12/31/7d0274bc8dc0ff227ed3570696b50452.png)

接下来就是分析三张表

1）第一张表：导出函数地址表

2）第二张表：导出函数名称表

3）导出函数序号表

![b579c23e205b6409da95c72fa55b8f54.png](https://s1.imagehub.cc/images/2022/12/31/b579c23e205b6409da95c72fa55b8f54.png)

~~~http
根据导出表的地址，定位到三张表，根据上面知道我们有5个函数，但是有名字的函数有3个，另外一个的名字是以序号的方式命名，还有一个函数是全0

序号表和函数名称表是对应的

找函数名在函数名称表去找序号表中对应的，序号表中的是4，所以就在函数地址表中找是4的除法

用名字和用序号找
~~~

![61221ce5f2e1c42120028fd930bc971b.png](https://s1.imagehub.cc/images/2022/12/31/61221ce5f2e1c42120028fd930bc971b.png)



#### 13/14/15.导入表

![009b1e34a47e5c1d0e59f68720e270f7.png](https://s1.imagehub.cc/images/2022/12/31/009b1e34a47e5c1d0e59f68720e270f7.png)

在扩展PE头的最后16个结构体数组中，第一个是导出表，第二个就是导入表

通过导入表找到当前文件所依赖的模块

~~~http
导出表，导入表，重定位表，资源表		最重要
~~~

依赖一个模块就有一个导入表，依赖多个模块就有多个导入表

![bdf89d1bd3b8c3573d765a1b82ef1c27.png](https://s1.imagehub.cc/images/2023/01/01/bdf89d1bd3b8c3573d765a1b82ef1c27.png)

有多少个导入表，导入表所依赖的模块的名字是什么，导入表结构是20个字节

直到遇见20个0结束，如图

![ce0bd6c1c62bdf37bec174ce3d0f1464.png](https://s1.imagehub.cc/images/2023/01/01/ce0bd6c1c62bdf37bec174ce3d0f1464.png)

这里指向的是导入表起始地址00 00 80 00 h，我们去找

![5359865d8c0355f256db25c19b9ff595.png](https://s1.imagehub.cc/images/2023/01/01/5359865d8c0355f256db25c19b9ff595.png)

因为导入表结构是20个字节，我们一直找，直到发现连着20个 00

![426156b600916400bb4a0dedf05766cd.png](https://s1.imagehub.cc/images/2023/01/01/426156b600916400bb4a0dedf05766cd.png)

例如，第一个导入表

![f533fbf5120241d5a825b27232b28258.png](https://s1.imagehub.cc/images/2023/01/01/f533fbf5120241d5a825b27232b28258.png)

![694855e556806ef1aef149452a036d7e.png](https://s1.imagehub.cc/images/2023/01/01/694855e556806ef1aef149452a036d7e.png)

这里我们去找第一个导入表的地址，01 D5 D8，什么时候解析到00什么时候结束

![7436caebf16ecde8d133a841cd86e94d.png](https://s1.imagehub.cc/images/2023/01/01/7436caebf16ecde8d133a841cd86e94d.png)

依赖一个模块就有一个结构体，那么第一个结构体就是和KERNEL32.dll有关的东西

![8da41e2f699bdd7eb92fcd6028fc3bf0.png](https://s1.imagehub.cc/images/2023/01/01/8da41e2f699bdd7eb92fcd6028fc3bf0.png)

第二个模块就是USER32.dll

![b6f883cbdef8734e784432fa2cb79351.png](https://s1.imagehub.cc/images/2023/01/01/b6f883cbdef8734e784432fa2cb79351.png)

那么为什么实际上的调用dll比我们数的要多呢？

只直到名字，怎么去找dll？

确定依赖函数

![654d0b1850a0f699e33457349ca5609a.png](https://s1.imagehub.cc/images/2023/01/01/654d0b1850a0f699e33457349ca5609a.png)

![5d746b3cfa64c2e2c0809a605e158d82.png](https://s1.imagehub.cc/images/2023/01/01/5d746b3cfa64c2e2c0809a605e158d82.png)

这里第一个成员和最后一个成员也指向了另一张表

第一个成员指向的表叫INT 导入名称表（import name table）

第二个成员指向的表叫IAT 导入地址表（import address table）

两者名称不一样，内容一样

~~~http
导入名称表里面的任何一个东西都是一个结构体，这里是联合体，为什么有4个成员，是为当前的结构体起了四个名字，
~~~

![a7db509ec224b0d8143eeb7705262bf1.png](https://s1.imagehub.cc/images/2023/01/01/a7db509ec224b0d8143eeb7705262bf1.png)

如果INT中有10个成员，则依赖这个dll文件中的10个函数

![c559fbbf93007f72c39a35fa239728f5.png](https://s1.imagehub.cc/images/2023/01/01/c559fbbf93007f72c39a35fa239728f5.png)

说明

![ac36c1e886783791d8bd7e63a05cda99.png](https://s1.imagehub.cc/images/2023/01/01/ac36c1e886783791d8bd7e63a05cda99.png)

![0a229ac5c64c77f4da24c5854a213059.png](https://s1.imagehub.cc/images/2023/01/01/0a229ac5c64c77f4da24c5854a213059.png)

![6c2c2f675e017bf80611673cfa8fd6a3.png](https://s1.imagehub.cc/images/2023/01/01/6c2c2f675e017bf80611673cfa8fd6a3.png)

~~~http
说明我们依赖kernall.dll这个模块下的exithread函数
~~~

----

当我们写入如下函数messageBox时，该函数是User32.dll里面的，故在编译成exe后的pe中，导入表中就会找的到User32和messagebox这个函数名



![825e6eb44d01ed2b313e678975b0ea2b.png](https://s1.imagehub.cc/images/2023/01/01/825e6eb44d01ed2b313e678975b0ea2b.png)

目的是运行messageBox时，能够找到messageBox在哪里；反汇编，编译器帮我们生成了一个间接call

只要我们用的是其他dll里面的函数，生成的反汇编这里都是间接call，仅仅提供一个地址编号

[![6258334c8bb0ee4246e0818cff310b57.png](https://s1.imagehub.cc/images/2023/01/01/6258334c8bb0ee4246e0818cff310b57.png)](https://www.imagehub.cc/image/0ocMr)

PE加载前后是不一样的

![](https://s3.bmp.ovh/imgs/2023/03/16/b50a282e5ee1e12b.png) 

为什么有了IAT后还要INT呢？因为当IAT被篡改后，可以利用INT快速定位到对应的函数进行修复

![74e0f8996de0227c7637faffa683560e.png](https://s1.imagehub.cc/images/2023/01/01/74e0f8996de0227c7637faffa683560e.png)

~~~http
通过结构体，找到导出表函数的名字，根据名字得到函数地址，填入到IAT中
~~~



#### 17.重定位表

一个进程所需要的PE文件是如何被加载进内存中的

![d2c783f8cbcfcc013cd29ea30555d93c.png](https://s1.imagehub.cc/images/2023/01/01/d2c783f8cbcfcc013cd29ea30555d93c.png)

![cde708a3700531796341d1590465912f.png](https://s1.imagehub.cc/images/2023/01/01/cde708a3700531796341d1590465912f.png)

这里我们在将exe加载入内存时，假如根据exe的Imagebase安排，地址是27000，调用了第一个dll文件A，A的imagebase是1000000，那么刚好可以，可是万一我们再调用dll的B，它的imagebase也是1000000，那么两者就会冲突，这个时候操作系统就会按照内存展开的方式，将B的dll的imagebase的值进行复制展开，比如2000000

![44b3ef336c18155041b59aa74f3f0af4.png](https://s1.imagehub.cc/images/2023/01/01/44b3ef336c18155041b59aa74f3f0af4.png)

如果AB等PE文件内部都是按照RVA方式排布，那么两者不会重叠。例外如下

![c2e35648402cb021912529b2f36dd1fe.png](https://s1.imagehub.cc/images/2023/01/01/c2e35648402cb021912529b2f36dd1fe.png)

当我们定义了一个全部变量，并赋值11

![586ef65cfac435e7e8f10563f01d676b.png](https://s1.imagehub.cc/images/2023/01/01/586ef65cfac435e7e8f10563f01d676b.png)

查看反汇编，将11的值赋给地址是00427e34的地址，这明显不是RVA，而是一个已经计算好的值（算上imagebase），生成的二进制是00 42 7E 34 05 C7，直接编译进二进制exe里面

当前的imagebase是400000

而我们的赋值的地址是427e34

也就是说，当我们使用全局变量时，使用的并不是RVA，而是全局变量地址	

![8396634d0438ea7b160267a5ce3d66c3.png](https://s1.imagehub.cc/images/2023/01/01/8396634d0438ea7b160267a5ce3d66c3.png)

假设A和B都是这样以全局变量的方式赋值的PE文件，那么A的imagebase刚好是1000000，而我们赋值的11的地址是1012345，这样刚好在其内部，但是如果B也是这样，那么它的1012345不在其内部，则必须做内存地址偏移，如果不加以修正则无法使用。

![e7b4862c531e228832dd352dfa65fc41.png](https://s1.imagehub.cc/images/2023/01/01/e7b4862c531e228832dd352dfa65fc41.png)

如果一旦我们在硬编码后没有占在4，而在5上，那我们只需要将其修改为4即可

那么我需要一张表，这张表可以记录记录401048是需要修改的，这就是重定位表，他的作用就是当PE文件在内存中没有占住自己应该占住的时，就帮助他修改占住。重定位表是必须的！！！

那么如何找重定位表呢？去找扩展pe头里面的结构体数组

![0ef24f2970fbedc32a55b1371123975e.png](https://s1.imagehub.cc/images/2023/01/01/0ef24f2970fbedc32a55b1371123975e.png)

在这个结构体数组的第六个（基址重定位），它记录的也只是从那里开始（地址）和大小size

![3ed16771b08110ed3efed2b85b9d35a0.png](https://s1.imagehub.cc/images/2023/01/01/3ed16771b08110ed3efed2b85b9d35a0.png)

这些表都有额外的结构体去描述他们的样子

![baf8ff4999792e4d22e689b5be8197f7.png](https://s1.imagehub.cc/images/2023/01/01/baf8ff4999792e4d22e689b5be8197f7.png)

![fcbca71515e13d9a608cbdb15b847065.png](https://s1.imagehub.cc/images/2023/01/01/fcbca71515e13d9a608cbdb15b847065.png)

这样，如图，VirtualAddress是X，SizeOfBlock是Y，那么Y规定了当前重定位表是多少个，例如第一个是16个字节，第二个是20个字节，第三个是12个字节。直到连续遇见8个字节的 00 才结束。

![3d828f153adc54f8869f62b427ad2fb1.png](https://s1.imagehub.cc/images/2023/01/01/3d828f153adc54f8869f62b427ad2fb1.png)

![edebae99c99b89e629d7ee9261fa1b77.png](https://s1.imagehub.cc/images/2023/01/01/edebae99c99b89e629d7ee9261fa1b77.png)

假如我们需要修改801234，801235等1万个地址，每个地址是4KB，那么我们的重定位表就要记录4 * 10000KB，这样开销太大，但是假如我们可以先记录一个800000，然后再记录1234，1235，这样每个只用2KB，这样可以节省近一半的空间。

因此，这里的VirtualAddress就是X，也就是800000，而这里的SizeOfBlock也就是一个个1234，1235。那为什么每行是4KB呢？是因为在内存中是以4KB为单位的，在物理内存上4KB是一个物理页，一个物理页创建一个重定位表，

用X的大地址加上Y下面的小地址就是修复的地址。

问题又来了？

![52ca65393d5b8e1ccaaa22296d494d5f.png](https://s1.imagehub.cc/images/2023/01/01/52ca65393d5b8e1ccaaa22296d494d5f.png)

Y部分是两个字节即可，但是如图每颜色块是2个字节，但修复时只需要每个的高4位即可，而低4位是用来判断的

当低四位是0011 = 3时（二进制），则X + 低12位才是有意义修复的值，如果不是3则没有意义修复。

为什么呢？

因为程序有内存对齐的概念，如果不是3，说明值是填缝隙用的，是内存对齐用的。

========================

#### 18.注入shellcode

shellcode就是一段可以执行的机器码，不依赖外部环境，在任何地方都可以执行的机器码，对于做病毒的，那么shellcode就是恶意代码，对于保护者来说，壳就是shellcode。

~~~http
shellcode编写要求：
1.不能有全局变量
2.不能使用常量字符串
3.不能使用系统调用
4.不能嵌套调用其他函数：区分注入代码和注入模块，注入模块可以注入一大堆函数过去，但是痕迹太多；注入代码太罗嗦，写起来麻烦
~~~

![a03da4e24da771e91c2a06eb71c8a06a.png](https://s1.imagehub.cc/images/2023/01/01/a03da4e24da771e91c2a06eb71c8a06a.png)

1）全局变量不必须存在不用就不用

全局变量可能会被其他函数或模块修改，从而导致 shellcode 的行为出现意外的变化或异常。通过避免使用全局变量，可以确保 shellcode 只依赖于自身的状态，并且不会受到外部干扰。

在编写 Shellcode 时，不应该使用常量字符串的主要原因是为了增加其隐蔽性，避免被反汇编或静态分析工具检测到。常量字符串通常被存储在数据段或常量区中，并且可以通过静态分析等方式轻易地发现其内容和用途。

然而，在实际编写 Shellcode 的过程中，也有可能会使用常量字符串，这并不一定会导致 Shellcode 在其他系统上无法执行。这是因为在 Windows 操作系统中，常量字符串所在的数据段和常量区的位置是固定的，并且在不同的系统版本中基本保持一致。因此，在编写针对 Windows 系统的 Shellcode 时，常量字符串能够正常运行的概率较高。

但是需要注意的是，常量字符串作为一种不可更改的数据类型，其内容在内存中是只读的，不能直接修改。如果需要在 Shellcode 中修改字符串的内容，可以考虑使用字符数组等可修改的数据类型。

2）不能使用常量字符串

如果我们写常量字符串，那么如图

![01643fe78d2f02196f2dfc16f1e96f40.png](https://s1.imagehub.cc/images/2023/01/01/01643fe78d2f02196f2dfc16f1e96f40.png)

![c15cbf8a03cfcbcd37c07d54fba50147.png](https://s1.imagehub.cc/images/2023/01/01/c15cbf8a03cfcbcd37c07d54fba50147.png)

这样，编译器会把双引号内的字符串优先放入常量区，等用的时候会move过来，但是这样的硬编码复制到别的地方则无法使用，因为别的地方未必有这个常量区，也未必是china。

![453da9a1e0bface11ecae9c518d5be35.png](https://s1.imagehub.cc/images/2023/01/01/453da9a1e0bface11ecae9c518d5be35.png)

![f48acb2b196df9f792847a2922191fcb.png](https://s1.imagehub.cc/images/2023/01/01/f48acb2b196df9f792847a2922191fcb.png)

这样的代码就会在堆栈里，所有的shellcode必须这么搞，\0 就是0。



3）不能使用系统调用

我们正常操作是，用api要自己在本地获取地址，通过指针的方式把这个api地址传过去，我们为什么不能直接写一个messagebox()函授呢？

![a9fd25b452ced53aef1f16b162e97c0b.png](https://s1.imagehub.cc/images/2023/01/01/a9fd25b452ced53aef1f16b162e97c0b.png)

当我们直接写一个messageBox()时，编译器替我们完成某些操作。

当我们在程序里通过静态链接的方式，使用其他的dll，编译器给我们生成导入表，也就是当我们写了messagebox()时，编译器会生成User32.dll，但是我们本地这里加载User32.dll时的Messagebox的地址在别人机器上同样的地址，别人那里就不一定是Messagebox了

![7157103500aa4ef80a5ff11ebee55078.png](https://s1.imagehub.cc/images/2023/01/01/7157103500aa4ef80a5ff11ebee55078.png)

这是一种间接call，这个间接call里面的值存的是程序运行时动态填入的

![4eeda318f16c026d365c820fac6a5cac.png](https://s1.imagehub.cc/images/2023/01/01/4eeda318f16c026d365c820fac6a5cac.png)



但是当我们将这段代码复制到其他进程中时，也是这样执行的，但是别的进程没有这个导入表

![ae11fe6c8608c05f2afe907764b05300.png](https://s1.imagehub.cc/images/2023/01/01/ae11fe6c8608c05f2afe907764b05300.png)

所以我们尝试利用









#### 19.VirtualTable_Hook

虚表HOOK

[![p9Nzs1I.png](https://s1.ax1x.com/2023/05/05/p9Nzs1I.png)](https://imgse.com/i/p9Nzs1I)

~~~
IAT HOOK 影响一个进程 ring3
SSDT HOOK ring0	影响所有进程
~~~

虚函数和虚表

`Shape`类是一个纯虚类，它定义了两个纯虚函数`area()`和`perimeter()`， `Circle`类继承了`Shape`类，并实现了`area()`和`perimeter()`函数。

~~~c++
class Shape {
public:
    virtual double area() = 0;
    virtual double perimeter() = 0;
};

class Circle : public Shape {
private:
    double radius;

public:
    Circle(double r) : radius(r) {}

    double area() {
        return 3.14 * radius * radius;
    }

    double perimeter() {
        return 2 * 3.14 * radius;
    }
};

int main() {
    Shape* shapePtr;
    Circle c(5);

    shapePtr = &c;

    cout << "Area: " << shapePtr->area() << endl;
    cout << "Perimeter: " << shapePtr->perimeter() << endl;

    return 0;
}
~~~

虚表HOOK就是修改内存指针的指向地址，实现HOOK

`shapePtr->area()`，这里可以将area所在地址进行HOOK后指向你想指向的地址

这里IAT HOOK可以实现对导入表的HOOK，改变导入表中DLL中函数的地址，实现你调用MessageBox却实现了我的函数，具体可以通过远程线程注入的方式实现

![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/124102/36/37454/13330/6481d692F1ffbd5c6/de3e2d2e69b1e6e5.jpg)

内联HOOK可以改代码，比如IAT HOOK只能对导入表中的函数进行HOOK，如果用户自定义了函数怎么办，而且IAT HOOK对方可以检测，当对方对比哪个函数地址不一样就能排查出来哪个函数被HOOK了，你就HOOK不到了，而我们可以利用内联HOOK对函数代码的修改

###### HOOK攻防

~~~
阶段一：
检测：检测函数内有没有JMP，对应E9，检测跳转的范围是不是固定范围
绕过：通过call跳转，跳转范围可以先跳到短范围，然后再跳短范围

阶段二：
检测：全代码校验，启动一个线程，将我想要保护的机器指令放在一个数组里，然后每隔一段时间进行比较，只要发现不一样就说明被HOOK了，程序就禁止执行
CRC校验也是差不多的，
~~~


