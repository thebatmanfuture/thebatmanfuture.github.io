

##### 1.叙述一下DOS头

~~~
DOS头由dos mz头和dos块组成，dos mz头是64个字节，开头是4D 5A，表示MZ，最后四个字节(从3C开始)所指向的就是PE文件标识，处于dos mz头和PE文件标识之间的就是dos块，是给连接器用的，现在没用，
~~~

##### 2.叙述一下PE头

~~~
PE头由三部分组成：PE文件标识(4字节)，标准PE头(20字节)，扩展PE头(224/240字节)
标准PE头属性说明：
    1-2字节表示可以在某CPU上运行
    3-4字节表示节的数量
    17-18字节表示扩展PE头的大小，0xE0表示224字节(32位)，0xF0表示240字节(64位)
扩展PE头属性说明：
magic 1-2字节如果是10B就是32位，如果是20B就是64位
AddressOfEntryPoint 17-20字节是程序入口
imagebase 29-32字节 内存镜像基址
sectionalignment	内存对齐
filealignment	文件对齐


sizeofimage	   程序在内存中展开之后的大小，一定是内存对齐的整数倍
sizeofheaders  文件对齐的整数倍

numberfrvaandsize 目录项数目
结构体数组 2个成员，每个成员4个字节，有numberfrvaandsize个
~~~

##### 3.叙述一下节表

~~~
40个字节
前8个字节是名字
	pointertorawdata	文件中的起始位置
	VirtualAddress 		内存中的起始位置
~~~



##### 4.RVA和FOA是什么，如何转换？

~~~
RVA是相对虚拟地址，例如我们有一个全局变量，它所在的exe运行之后的地址是40000，我们就用40000减去imagebase这个内存镜像基址得到的就是RVA
得到了RVA后，我们要找在文件中的地址是多少，即FOA
~~~



##### 5.怎么判断PE是DLL,还是EXE？

在`IMAGE_FILE_HEADER.Characteristics`里面

![pSpq4JO.png](https://s1.ax1x.com/2022/12/30/pSpq4JO.png)

~~~
0000 0010 0010 0110
~~~

![](https://s3.bmp.ovh/imgs/2022/12/30/3abd3735b2fa7a5d.png)

这里数据位是1的，代表上图文件可执行

##### 6.怎么判断PE文件是32位还是64位?

1）在标准PE头中的Machine，即前两个字节表示可以运行在什么CPU上，这个可以判断

2）或者是sizeofoptionalheader，可选PE头的大小，如果是0xE0就是32位，0xF0就是64位

3）扩展PE头的前两个字节Magic，如果是10B就是32位，如果是20B就是64位



##### 7.PE加载过程

调用CreateProcess启动，生产内核对象，分配4GB进程空间，系统加载器加载dll文件以及完成重定位,IAT,接着创建进程的主线程，
之后主线程为每个DLL调用_DLLMainCRTStartup函数，然后主线程根据EXE是GUI（图形化）程序还是CUI（控制台）程序执行MainCRTStartup
最后一步执行WinMain(或者main函数)
`ReadFile`读取文件头信息，判断是否是PE文件
获取映像总大小，申请可读可写可执行的内存全部填为0
获取区段信息，循环读取每一个区段内容，将`ReadFile`读取的数据映射到内存中
修复重定位
修复IAT
创建线程，线程回调指向OEP





















