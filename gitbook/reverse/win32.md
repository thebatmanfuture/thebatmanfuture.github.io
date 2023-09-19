![](https://s3.bmp.ovh/imgs/2023/05/11/9a137fc1036fb4af.png)

#### 2.字符编码

![](https://s3.bmp.ovh/imgs/2023/03/16/221f39e0f211a921.png)

unicode是对应关系

utf-16和utf-8是在计算机中存储关系

#### 3.c语言中的宽字符

![](https://s3.bmp.ovh/imgs/2023/03/16/e2e921b208e67c71.png)

就是指unicode这种编码

第一种采用ascii方式存储，消耗5个字节，第二种采用utf-16，消耗6个字节

如果采用unicode这种，则函数是w xxx

![](https://s3.bmp.ovh/imgs/2023/03/16/2e22ad7acec9f6b0.png)

~~~c
#include "stdafx.h"
#include <locale.h>

int main(){
    setlocate(LC_ALL,"");// 使用控制台默认的编码
    wchar_t swzStr[] = L"中国";
    wprintf(L"%s\n",x1);
    return 0;
    
}

~~~

#### 4.win32 API中的宽字符

win32

![](https://s3.bmp.ovh/imgs/2023/03/16/aa8879dfd8bc268a.png)

~~~
TCHAR STZ[] = TEXT("中国");
~~~

~~~c
#include <stdio.h>
#include "stdlib.h"
#include "windows.h"

void fun(){
    char szBuffer[] = {'C', 'h', 'i', 'n', 'a', '\0'};
}

int main(int argc, char* argv[]){

    fun();
    TCHAR STZ[] = TEXT("中国");

    return 0;
}
~~~

windows为了应对ascii格式和unicode格式，每个都提供了两种格式

例如

~~~c
    MessageBoxA();
    MessageBoxW();
~~~

~~~c
#include <stdio.h>
#include "stdlib.h"
#include "windows.h"

void fun(){
    char szBuffer[] = {'C', 'h', 'i', 'n', 'a', '\0'};
}

int main(int argc, char* argv[]){

    char sztitle[] = "标题";
    char szcontent[] = "欢迎大家来到win32api";
    MessageBoxA(0, szcontent, sztitle, MB_YESNO);

    return 0;
}
~~~

![](https://s3.bmp.ovh/imgs/2023/03/16/480809a4a124638f.png)

但是如果使用以上内容跨平台性很差，故使用宏

~~~c
int main(int argc, char* argv[]){

    TCHAR stztitle[] = "title is batmanfuture";
    TCHAR stzcontent[] = "my name is batmanfuture";
    MessageBox(0, stzcontent, stztitle, MB_OK);

    return 0;
}
~~~

![](https://s3.bmp.ovh/imgs/2023/03/16/c451a13b790a10a2.png)



#### 5.进程的创建过程

~~~
高2g公用
低2g是自己的
~~~

![](https://s3.bmp.ovh/imgs/2023/03/16/20ccabfcc08dd471.png)

进程的创建

![](https://s3.bmp.ovh/imgs/2023/03/20/7877a4bfb5312b93.png)

双击exe时通过explorer.exe的createprocess()创建进程

~~~
1.映射exe文件：将exe加载到内存
2.创建内核对象eprocess：每个进程都有一个eprocess对象
3.映射系统DLL：将ntdll.dll也加载进内存
4.创建线程内核对象ethread：
5.启动线程
	映射DLL
	线程开始执行
~~~

![](https://s3.bmp.ovh/imgs/2023/05/12/5c92b3b594eae0a8.png)

白加黑就是在映射DLL这一步修改



![](https://s3.bmp.ovh/imgs/2023/03/16/172cece570a34faa.png)

每个进程都有自己的EPROCESS对象

每个exe都需要一个系统DLL，即NTDLL,dll

![](https://s3.bmp.ovh/imgs/2023/03/16/2c42b11d8eb0cf82.png)

![](https://s3.bmp.ovh/imgs/2023/03/16/76adf45055bca10c.png)

.

![](https://s3.bmp.ovh/imgs/2023/03/16/7ff2285dad1fc93a.png)



#### 6.创建进程

利用createProcess创建进程，父进程就是explorer.exe，子进程就是创建的进程；但是我如果通过A进程利用createProcess创建B进程，那么B的父进程就是A

c语言命令行参数

~~~c
int main(int argc, char* argv[]){

    printf("%s - %s \n", argv[0], argv[1]);
    getchar();
    return 0;
}
~~~

![](https://s3.bmp.ovh/imgs/2023/03/16/65cdc0e8e5306641.png)

![](https://s3.bmp.ovh/imgs/2023/03/16/a1800c3dd79338fa.png)

#### 7.句柄表

内核对象-

![](https://s3.bmp.ovh/imgs/2023/03/20/37d52a701a37f33c.png)

![](https://s3.bmp.ovh/imgs/2023/03/16/a117ba013c09c7d7.png)

当我们在应用层创建一个进程，那么内核层会给我们准备一个结构体EPROCESS，当我们创建一个线程时，会准备一个Tthread结构体，故我们称进程这种为内核对象

![](https://s3.bmp.ovh/imgs/2023/03/16/7799710d711e8a11.png)

每个进程才有一个句柄表

![](https://s3.bmp.ovh/imgs/2023/03/16/74ccff78d0576016.png)

这个表解决，不能直接把内核对象的结构体地址返给应用层，如果应用层修改了，就会蓝屏

故windows增加了一道防火墙，即通过句柄表的方式记录内核层中内核对象的结构体的地址

![](https://s3.bmp.ovh/imgs/2023/03/16/17a1d0278bdd084a.png)

并不是closehandle后，这个内存对象就会死的；因为可能像如下这种关闭一个之后另一个还在，则A为1，不会死；线程例外，因为进程里面线程没有死那么进程就不会死



每个进程都有自己的句柄表，句柄表内的值不同进程的地址不一样，当有两个基于进程的内核对象时，他的计时器就是2



closehandler只是计数器减一

![e57c43f31bdbde5f8d76e6a31abdedc5.png](https://s1.imagehub.cc/images/2023/03/16/e57c43f31bdbde5f8d76e6a31abdedc5.png)

当我们发现有这样的安全描述符，那么就一定是内核对象

~~~
createCount()
~~~

![554004ae27b81c738052f18a478e26cf.png](https://s1.imagehub.cc/images/2023/03/16/554004ae27b81c738052f18a478e26cf.png)

真正的句柄表其实有三列，左侧的地方为1则表示可以被继承，为0则表示不可以被继承

![f4d05c834f003a41ff4d05ee8af41e42.png](https://s1.imagehub.cc/images/2023/03/16/f4d05c834f003a41ff4d05ee8af41e42.png)

这里在创建子进程的时候，允许把父进程的句柄表复制一份过去

![3ab78a499c1a6a4a7815b66f489d8229.png](https://s1.imagehub.cc/images/2023/03/16/3ab78a499c1a6a4a7815b66f489d8229.png)

每个进程至少有一个线程，如果我们使用closehandle(p1.hProcess)和closehandle(p1.hThread)来使得计时器归0，但是只有先关闭线程之后才能关闭进程，所以当我们直接执行该两句，浏览器并不会关闭，只有当我们手动关闭浏览器，进程才会关闭



例如，我利用CreateProcess创建一个打开calc.exe的进程

~~~c++
#include <iostream>
#include <windows.h>

using namespace std;

int main() {
    STARTUPINFO si;
    PROCESS_INFORMATION pi;

    ZeroMemory(&si, sizeof(si));
    si.cb = sizeof(si);
    ZeroMemory(&pi, sizeof(pi));

    // Create the child process.
    char path[1000] = "C:\\Windows\\System32\\calc.exe";

    if (!CreateProcess(path,   // 文件名
        NULL,           // 命令行参数
        NULL,           // 进程句柄不可继承
        NULL,           // 线程句柄不可继承
        FALSE,          // 无特殊标志
        0,              // 没有额外的创建标志
        NULL,           // 使用父进程的环境变量
        NULL,           // 使用父进程的工作目录
        &si,
        &pi)) {
        cerr << "Failed to create process: " << GetLastError() << endl;
        return 1;
    }

    cout << "Created process with PID: " << pi.dwProcessId << endl;
    cout << "Process handle: " << pi.hProcess << endl;

    // Wait until child process exits.
    WaitForSingleObject(pi.hProcess, INFINITE);

    // Close process and thread handles.
    CloseHandle(pi.hProcess);
    CloseHandle(pi.hThread);
    getchar();

    return 0;
}

~~~

![](https://s3.bmp.ovh/imgs/2023/05/12/501374738a87e03b.png)

利用OpenProcess和TerminateProcess关闭一个进程

我们如下代码的功能就是关闭我打开的这个进程，它的PID是66396

~~~c++
#include <iostream>
#include <windows.h>

using namespace std;

int main() {
    DWORD pid = 66396;   // 需要关闭的进程的 PID

    HANDLE hProcess = OpenProcess(PROCESS_TERMINATE, FALSE, pid);
    if (hProcess == NULL) {
        cerr << "Failed to open process: " << GetLastError() << endl;
        return 1;
    }

    if (!TerminateProcess(hProcess, 0)) {
        cerr << "Failed to terminate process: " << GetLastError() << endl;
        CloseHandle(hProcess);
        return 2;
    }

    cout << "Terminated process with PID: " << pid << endl;

    CloseHandle(hProcess);
    return 0;
}

~~~





#### 8.进程相关API

pid可以全局

~~~
进程id，进程句柄，线程id，线程句柄 皆为 全局句柄表中的索引(独一无二)
~~~





![](https://s3.bmp.ovh/imgs/2023/03/17/0f54f45502937eca.png)

![](https://s3.bmp.ovh/imgs/2023/03/17/7079c957a7ecf908.png)![](https://s3.bmp.ovh/imgs/2023/03/17/daa69b59a3b55cfb.png)

![](https://s3.bmp.ovh/imgs/2023/05/12/675d4a4f819e8dd1.png)

#### 9.创建线程

![](https://s3.bmp.ovh/imgs/2023/05/12/a40bffd1aaf4b3dc.png)

createthread

~~~c++
#include <iostream>
#include <windows.h>

using namespace std;

DWORD WINAPI ThreadProc(LPVOID lpParameter) {
    // 输出线程 ID 和参数值
    cout << "Thread created: " << GetCurrentThreadId() << endl;
    cout << "Thread parameter: " << lpParameter << endl;

    // 线程执行逻辑
    for (int i = 0; i < 10; i++) {
        cout << "Thread is running... (" << i << ")" << endl;
        Sleep(1000);
    }

    return 0;
}

int main() {
    HANDLE hThread;
    DWORD dwThreadId;
    LPVOID lpParameter = (LPVOID)12345;  // 设置线程参数

    // 创建新线程
    hThread = CreateThread(NULL, 0, ThreadProc, lpParameter, 0, &dwThreadId);
    if (hThread == NULL) {
        cerr << "Failed to create thread: " << GetLastError() << endl;
        return 1;
    }

    cout << "Created thread with ID: " << dwThreadId << endl;

    // 等待线程结束
    WaitForSingleObject(hThread, INFINITE);

    // 关闭线程句柄
    CloseHandle(hThread);

    return 0;
}
~~~



~~~
1.线程的内核对象的计数器为0
2.线程的执行代码执行完毕
~~~





![](https://s3.bmp.ovh/imgs/2023/05/12/af1b33c9fbe31f73.png)





closeHandler并不是真正意义上的关闭句柄，而是计时器减一

![](https://s3.bmp.ovh/imgs/2023/03/17/8ea85262ae91858d.png)

#### 10.线程控制

SuspendThread函数（挂起）

RusumeThread函数（恢复）

`SuspendThread` 和 `ResumeThread` 函数是 Windows API 中用于控制线程挂起和恢复的函数。其中，`SuspendThread` 函数可以用来暂停一个正在运行的线程，使其进入挂起状态；而 `ResumeThread` 函数则可以用来恢复一个被挂起的线程，使其继续运行。

~~~c++
#include <iostream>
#include <windows.h>

using namespace std;

DWORD WINAPI ThreadProc(LPVOID lpParameter) {
    // 输出线程 ID 和参数值
    cout << "Thread created: " << GetCurrentThreadId() << endl;
    cout << "Thread parameter: " << lpParameter << endl;

    // 线程执行逻辑
    for (int i = 0; i < 10; i++) {
        cout << "Thread is running... (" << i << ")" << endl;
        Sleep(1000);
    }

    return 0;
}

int main() {
    HANDLE hThread;
    DWORD dwThreadId;
    LPVOID lpParameter = (LPVOID)12345;  // 设置线程参数

    // 创建新线程
    hThread = CreateThread(NULL, 0, ThreadProc, lpParameter, 0, &dwThreadId);
    if (hThread == NULL) {
        cerr << "Failed to create thread: " << GetLastError() << endl;
        return 1;
    }

    cout << "Created thread with ID: " << dwThreadId << endl;

    // 暂停线程
    DWORD dwRet = SuspendThread(hThread);
    if (dwRet == -1) {
        cerr << "Failed to suspend thread: " << GetLastError() << endl;
    } else {
        cout << "Thread is suspended." << endl;
    }

    // 等待一段时间
    Sleep(5000);

    // 恢复线程
    dwRet = ResumeThread(hThread);
    if (dwRet == -1) {
        cerr << "Failed to resume thread: " << GetLastError() << endl;
    } else {
        cout << "Thread is resumed." << endl;
    }

    // 等待线程结束
    WaitForSingleObject(hThread, INFINITE);

    // 关闭线程句柄
    CloseHandle(hThread);

    return 0;
}
~~~



WaitForSingleObject

~~~c++
#include <iostream>
#include <windows.h>

using namespace std;

DWORD WINAPI ThreadProc(LPVOID lpParameter) {
    // 输出线程 ID 和参数值
    cout << "Thread created: " << GetCurrentThreadId() << endl;
    cout << "Thread parameter: " << lpParameter << endl;

    // 线程执行逻辑
    for (int i = 0; i < 10; i++) {
        cout << "Thread is running... (" << i << ")" << endl;
        Sleep(1000);
    }

    return 0;
}

int main() {
    HANDLE hThread;
    DWORD dwThreadId;
    LPVOID lpParameter = (LPVOID)12345;  // 设置线程参数

    // 创建新线程
    hThread = CreateThread(NULL, 0, ThreadProc, lpParameter, 0, &dwThreadId);
    if (hThread == NULL) {
        cerr << "Failed to create thread: " << GetLastError() << endl;
        return 1;
    }

    cout << "Created thread with ID: " << dwThreadId << endl;

    // 等待线程结束
    DWORD dwRet = WaitForSingleObject(hThread, INFINITE);
    if (dwRet == WAIT_OBJECT_0) {
        cout << "Thread is finished." << endl;
    } else {
        cerr << "Failed to wait for thread: " << GetLastError() << endl;
    }

    // 关闭线程句柄
    CloseHandle(hThread);

    return 0;
}
~~~

#### 11.临界区

#### 12.互斥体











#### 14.窗口的本质

[![ppNnU1I.md.png](https://s1.ax1x.com/2023/03/20/ppNnU1I.md.png)](https://imgse.com/i/ppNnU1I)

所有的进程共用一个全局的句柄表，每个窗口都只有一个

[![ppNnYhd.png](https://s1.ax1x.com/2023/03/20/ppNnYhd.png)](https://imgse.com/i/ppNnYhd)

HANDLE是私有表（进程自己的句柄表）的索引

HWND是全局句柄表的索引

[![ppNumVS.png](https://s1.ax1x.com/2023/03/20/ppNumVS.png)](https://imgse.com/i/ppNumVS)[![ppNuVDf.png](https://s1.ax1x.com/2023/03/20/ppNuVDf.png)](https://imgse.com/i/ppNuVDf)

~~~
每个线程都有一个消息队列
~~~

产生消息来源：

鼠标，键盘，应用程序，操作系统内核

#### 15.消息队列

![](https://s3.bmp.ovh/imgs/2023/05/12/0e800d2e7bcbc2fa.png)

#### 16.第一个win窗口

~~~
handle 内核模块句柄
hwnd   指向窗口的句柄
hdc    设备上下文的句柄
instance 指向模块的句柄		真正的对象在ring0，dword
~~~

![](https://s3.bmp.ovh/imgs/2023/05/12/fc26c7fd12a816f3.png)

![](https://s3.bmp.ovh/imgs/2023/05/12/9e4df9f96fa431de.png)





#### 虚拟内存和物理内存

[![ppNK4kF.png](https://s1.ax1x.com/2023/03/20/ppNK4kF.png)](https://imgse.com/i/ppNK4kF)

[![ppNKbOx.png](https://s1.ax1x.com/2023/03/20/ppNKbOx.png)](https://imgse.com/i/ppNKbOx)



#### 私有内存的申请释放

内存分为私有和映射

[![ppNMA78.png](https://s1.ax1x.com/2023/03/20/ppNMA78.png)](https://imgse.com/i/ppNMA78)

[![ppNMVAS.png](https://s1.ax1x.com/2023/03/20/ppNMVAS.png)](https://imgse.com/i/ppNMVAS)

~~~
VirTualAlloc函数是Windows系统提供的一个内存分配函数，可以用它来向进程申请一段指定大小的虚拟内存空间。该函数支持多种内存分配方式，例如可读、可写、可执行等

使用完毕的内存空间必须通过VirtualFree函数手动释放，否则会导致内存泄漏
~~~

~~~c++
#include <windows.h>
#include <stdio.h>

int main()
{
    // 申请4KB的虚拟内存空间
    LPVOID lpvBase = VirtualAlloc(NULL, 4096, MEM_COMMIT, PAGE_READWRITE);

    if (lpvBase == NULL) {
        printf("VirtualAlloc failed.\n");
        return 1;
    }else{
        printf("batmanfuture");
    }

    // 在申请的内存区域中写入数据
    memset(lpvBase, 'A', 4096);

    // 释放申请的内存空间
    VirtualFree(lpvBase, 0, MEM_RELEASE);

    return 0;
}
~~~

[![ppNMoE8.png](https://s1.ax1x.com/2023/03/20/ppNMoE8.png)](https://imgse.com/i/ppNMoE8)

[![ppNQ95F.png](https://s1.ax1x.com/2023/03/20/ppNQ95F.png)](https://imgse.com/i/ppNQ95F)

#### 共享内存的申请释放

CreateFileMapping

~~~c++
#include <windows.h>
#include <stdio.h>

int main() {
    // 创建一个映射对象
    HANDLE hMapFile = CreateFileMapping(
            INVALID_HANDLE_VALUE,   // 对象映射到内存而不是文件
            NULL,                   // 默认安全属性
            PAGE_READWRITE,         // 可读写
            0,                      // 映射对象的最大大小，高位
            sizeof(int),            // 映射对象的最大大小，低位
            "MySharedMemory");      // 映射对象名称

    if (hMapFile == NULL) {
        printf("Failed to create file mapping object (%d)\n", GetLastError());
        return 1;
    }

    // 将映射对象映射到当前进程的地址空间
    int* pData = (int*)MapViewOfFile(hMapFile,
                                     FILE_MAP_ALL_ACCESS,
                                     0,
                                     0,
                                     sizeof(int));
    if (pData == NULL) {
        printf("Failed to map view of file (%d)\n", GetLastError());
        CloseHandle(hMapFile);
        return 1;
    }

    // 修改共享内存
    *pData = 123;

    // 等待用户按键
    printf("Press any key to close the program and release the shared memory...\n");
    getchar();

    // 关闭映射对象
    UnmapViewOfFile(pData);
    CloseHandle(hMapFile);

    return 0;
}
~~~

#### 文件系统

[![ppNQtVf.png](https://s1.ax1x.com/2023/03/20/ppNQtVf.png)](https://imgse.com/i/ppNQtVf)

。。。。

[![ppNQBxs.png](https://s1.ax1x.com/2023/03/20/ppNQBxs.png)](https://imgse.com/i/ppNQBxs)

[![ppNQrMn.png](https://s1.ax1x.com/2023/03/20/ppNQrMn.png)](https://imgse.com/i/ppNQrMn)[![ppNQyq0.png](https://s1.ax1x.com/2023/03/20/ppNQyq0.png)](https://imgse.com/i/ppNQyq0)

==

#### 内存映射文件

通过内存映射文件实现了两个进程共享一个文件

[![ppNlgOI.png](https://s1.ax1x.com/2023/03/20/ppNlgOI.png)](https://imgse.com/i/ppNlgOI)

写拷贝

[![ppNlc6A.png](https://s1.ax1x.com/2023/03/20/ppNlc6A.png)](https://imgse.com/i/ppNlc6A)

这里我们如果再进程A修改物理内存中的某个内容，那么根据写拷贝，他会复制一份内容到物理页，这样你的修改并不会影响真正的物理内存中的数据内容，这样进程B在操作物理内存时数据和原来是一样的



#### 27.远程线程

CreateRemoteThread

[![ppagfEt.png](https://s1.ax1x.com/2023/03/22/ppagfEt.png)](https://imgse.com/i/ppagfEt)

test.exe

在本地通过CreateThread创建线程

~~~c++
#include <windows.h>
#include "stdio.h"

void Run(){
    for (int i = 0; i < 10; ++i) {
        Sleep(1000);
        printf("Run()-----------------\n");
    }
}

DWORD WINAPI ThreadProc(LPVOID lpParamter){

    Run();
    return 0;
}

int main(int argc,char* argv[])
{
    HANDLE hThread = CreateThread(NULL, 0, ThreadProc, NULL, 0, NULL);
    CloseHandle(hThread);

    getchar();

    return 0;
}
~~~



1.exe

利用CreateRemoteThread实现远程线程注入，我们利用MyCreateRemoteThread函数实现注入到如上test.exe里面的ThreadProc函数，使其执行，这样就会一直打印"run..."，MyCreateRemoteThread函数的第一个参数就是你要注入的进程的PID，第二个参数是你要在别人的进程里面创建线程需要提供别人的函数（被注入执行的对象）

而CreateRemoteThread参数，其他都和CreateThread一样，只是多了一个参数hProcess，即你要控制的进程的句柄，而我们利用OpenProcess函数，即可从PID获取到对应的句柄

![](https://s3.bmp.ovh/imgs/2023/05/12/9784dc4c76c1a3e8.png)

~~~c++
#include <windows.h>
#include "stdio.h"

BOOL MyCreateRemoteThread(DWORD dwProcessID,

        DWORD (*dwProcAddr)(LPVOID)

){//这里的两个参数分别是进程id和你要注入的那个函数

    HANDLE hProcess;
    HANDLE hThread;
    DWORD dwThreadID;
    dwThreadID = 0;
    hProcess = 0;


    // 1.获取进程句柄
    hProcess = OpenProcess(PROCESS_ALL_ACCESS, FALSE, dwProcessID);
    if(hProcess == NULL){
        OutputDebugString("openprocess error!!!\n");
        return FALSE;
    }
    // 2.创建远程线程
    hThread = CreateRemoteThread(hProcess, NULL, 0, dwProcAddr,NULL,0,&dwThreadID);
    if(hThread == NULL){
    OutputDebugString("hThread error!!!\n");

    CloseHandle(hProcess);
    return FALSE;

    }


    CloseHandle(hThread);
    CloseHandle(hProcess);
    return true;
}

int main(int argc,char* argv[])
{
    // 67352
    MyCreateRemoteThread();

    getchar();

    return 0;
}
~~~

#### 28.远程线程注入

[![ppdwkG9.png](https://s1.ax1x.com/2023/03/22/ppdwkG9.png)](https://imgse.com/i/ppdwkG9)

[![ppdw1Gd.png](https://s1.ax1x.com/2023/03/22/ppdw1Gd.png)](https://imgse.com/i/ppdw1Gd)

[![ppaWeje.png](https://s1.ax1x.com/2023/03/22/ppaWeje.png)](https://imgse.com/i/ppaWeje)

VirtualAlloc

VirtualAllocEx可以为其他进程分配内存

~~~c++
#include <windows.h>
#include <iostream>

using namespace std;

int main(int argc, char* argv[]) {
    // 1. 获取目标进程句柄
    HANDLE hProcess = OpenProcess(PROCESS_ALL_ACCESS, FALSE, <PID>);
    if (hProcess == NULL) {
        cerr << "Failed to open process: " << GetLastError() << endl;
        return 1;
    }

    // 2. 在目标进程中分配内存空间
    LPVOID lpAddress = VirtualAllocEx(hProcess, NULL, MAX_PATH, MEM_COMMIT, PAGE_READWRITE);
    if (lpAddress == NULL) {
        cerr << "Failed to allocate memory in remote process: " << GetLastError() << endl;
        CloseHandle(hProcess);
        return 1;
    }

    // 3. 将DLL文件路径写入目标进程的内存空间中
    char dllPath[] = "C:\\123.dll";
    if (!WriteProcessMemory(hProcess, lpAddress, dllPath, strlen(dllPath) + 1, NULL)) {
        cerr << "Failed to write memory in remote process: " << GetLastError() << endl;
        VirtualFreeEx(hProcess, lpAddress, 0, MEM_RELEASE);
        CloseHandle(hProcess);
        return 1;
    }

    // 4. 创建目标进程并启动
    STARTUPINFO si = { sizeof(si) };
    PROCESS_INFORMATION pi;
    if (!CreateProcess(NULL, "<Target.exe>", NULL, NULL, FALSE, CREATE_SUSPENDED, NULL, NULL, &si, &pi)) {
        cerr << "Failed to create process: " << GetLastError() << endl;
        VirtualFreeEx(hProcess, lpAddress, 0, MEM_RELEASE);
        CloseHandle(hProcess);
        return 1;
    }

    // 5. 在目标进程中加载DLL并执行
    HMODULE hKernel32 = GetModuleHandle("kernel32.dll");
    LPVOID lpLoadLibraryA = (LPVOID)GetProcAddress(hKernel32, "LoadLibraryA");
    HANDLE hThread = CreateRemoteThread(hProcess, NULL, 0, (LPTHREAD_START_ROUTINE)lpLoadLibraryA, lpAddress, 0, NULL);
    if (hThread == NULL) {
        cerr << "Failed to create remote thread: " << GetLastError() << endl;
        VirtualFreeEx(hProcess, lpAddress, 0, MEM_RELEASE);
        TerminateProcess(pi.hProcess, 0);
        CloseHandle(pi.hProcess);
        CloseHandle(pi.hThread);
        CloseHandle(hProcess);
        return 1;
    }

    // 6. 等待远程线程结束
    WaitForSingleObject(hThread, INFINITE);

    // 7. 关闭句柄和释放内存空间
    CloseHandle(hThread);
    VirtualFreeEx(hProcess, lpAddress, 0, MEM_RELEASE);
    ResumeThread(pi.hThread);
    CloseHandle(pi.hProcess);
    CloseHandle(pi.hThread);
    CloseHandle(hProcess);

    return 0;
}
~~~



#### 进程间的通信



#### 模块隐藏

[![ppdDRdf.png](https://s1.ax1x.com/2023/03/22/ppdDRdf.png)](https://imgse.com/i/ppdDRdf)





~~~
一般情况下有如下dll注入方法：　　　　
1.修改注册表来注入dll；
2.使用CreateRemoteThread函数对运行中的进程注入dll；
3.使用SetWindowsHookEx函数对应用程序挂钩(HOOK)迫使程序加载dll；
4.替换应用程序一定会使用的dll；
5.把dll作为调试器来注入；
6.用CreateProcess对子进程注入dll
7.修改被注入进程的exe的导入地址表。
~~~

























