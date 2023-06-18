###### 1）你知道在python中实现命令执行有几种方式吗？

1-os

~~~python
os.system
os.popen
os.spawnlp
os.execl
~~~

2-commands（2.6之后被淘汰）

~~~python
commands.getoutput('ls -l')
~~~

3-subprocess

~~~python
subprocess.call
subprocess.Popen
~~~



###### 2）如何某公司做一款可以在线运行python代码的项目，防御方如何保证安全，攻击方如何绕过？[沙箱逃逸]

~~~
防御者：
1-正则匹配import os,commands,subprocess等危模块，发现就拦
2-删除掉对危害函数所在的模块的引用
3-删除 builtins模块内部的eval,exec等代码执行危险函数
4-正则匹配，对system,popen,getoutput,exec等函数拦截
~~~

~~~
攻击者绕过：
1-替换绕过，利用codecs，importlib模块实现导入
2-添加引用
3-reload重新加载
4-利用getattr实现对模块内部的函数进行加解密
5-利用模板注入实现调用任意模块进行rce【应归属模板注入篇】
~~~



###### 3）python的序列化和反序列化漏洞知道吗？

python中实现反序列化的函数有很多，但是可以对对象进行操作的只有json和pickle（3版本）而pickle最常用

~~~python
import pickle
a = "haha"
b = pickle.dumps(a)
print(b)
# b'\x80\x04\x95\x08\x00\x00\x00\x00\x00\x00\x00\x8c\x04haha\x94.'
~~~

像php的序列化是得到了一组字符串，但是python这里序列化之后是得到的字节码[详细见字节码对照表]

我们将这段字节码转换成指令序列

~~~python
import dis

code = b'\x80\x04\x95\x08\x00\x00\x00\x00\x00\x00\x00\x8c\x04haha\x94.'
dis.dis(code)
~~~

~~~python
\x80: PROTO 标记，用于指示当前的 pickle 协议版本。
\x04: 整数 4 的编码。
\x95: MARK 标记，用于将后续的对象打包到一个元组中。
\x08: 字符串长度为 8 的编码。
\x00\x00\x00\x00\x00\x00\x00: 字符串 "haha" 的编码。
\x8c: SHORT_BINSTRING 标记，用于表示短字符串（长度小于 256）。
\x04: 短字符串长度为 4 的编码。
\x94: TUPLE1 标记，用于将元组压入堆栈。
~~~

~~~python
    0: PROTO      4
    2: BINPUT     0
    4: MARK
    5: BININT1    4
    7: BININT     0
   10: EMPTY_TUPLE
   11: SHORT_BINSTRING 'haha'
   18: TUPLE1
   19: REDUCE
   20: POP
   21: POP_MARK
   22: LOAD_CONST None
   25: RETURN_VALUE
~~~

![imageb5b3f8a22d3aa1ac.png](https://cdnjson.com/images/2023/05/26/imageb5b3f8a22d3aa1ac.png)

![image2be00267c0c60cde.png](https://cdnjson.com/images/2023/05/26/image2be00267c0c60cde.png)

###### 4）php反序列化漏洞和python反序列化漏洞有什么区别吗？

python：如下，反序列化时可以直接将字符串解析成可执行的代码对象，并且可以执行其中包含的命令

~~~python
import pickle

s = b"cos\nsystem\n(S'whoami'\ntR."
pickle.loads(s)
~~~

或者你也可以直接对这段内容进行反序列化[字节码]

~~~python
import pickle
s = b'\x80\x04\x95\x1c\x00\x00\x00\x00\x00\x00\x00\x8c\x02nt\x94\x8c\x06system\x94\x93\x94\x8c\x04calc\x94\x85\x94R\x94.'
pickle.loads(s)
~~~

当前，你也可以像php那种反序列化对象，利用__ reduce __ 魔术方法，在进行反序列化的时候调用

~~~python
import os
import pickle
class A:
    def __reduce__(self):
        s = "calc"
        return (os.system, (s,))
e = A()
s = pickle.dumps(e)
print(s, type(s))
haha = pickle.loads(s)
~~~

php：由于 PHP 的反序列化机制不同于 Python 和 Java 等语言，所以无法像 Python 那样直接将字符串解析为可执行的代码并进行执行，如果要执行反序列化后的代码，需要创建一个可执行对象并将其传递给 `unserialize()` 函数进行反序列化

~~~php
<?php
highlight_file(__FILE__);
error_reporting(0);

class test{
	public $a = "hahahaha";
	public function displayVar(){
		eval($this->a);
	}
}
$c = new test();
$c->displayVar();
$get = $_GET["haha"];
$b = unserialize($get);
$b->displayVar();
~~~

###### 5）用python如何编写一个远控？

大致步骤是：编写客户端和服务端代码，服务端放在公网上运行，然后客户端运行，主动去连接服务端，然后服务端输入命令whoami之后发送给客户端，客户端接收之后在本地执行之后将结果返回到服务端

1-我这里首先假设两者最后都利用PyInstaller 打包成exe后，为了解决在使用 PyInstaller 对 Python 代码进行打包时，包含了 `input()` 函数的脚本会导致打包后的可执行文件无法正常运行，即我想在GUI和图形界面上可以接收输入和返回结果，所以利用`input_thread = threading.Thread(target=input_command, args=(client_socket,))`来接收输入

2-问题二，正常输入正确的命令之后可以执行，也应该让输入不正确的命令将结果返回

所以我们用subprocess替换popen来实现命令执行

~~~python
        try:
            # 使用 subprocess 模块执行命令
            process = subprocess.Popen(command, shell=True,
                                       stdout=subprocess.PIPE,
                                       stderr=subprocess.PIPE)
            out, err = process.communicate()

            # 获取命令输出结果或错误信息
            result = out if out else err

        except Exception as e:
            # 捕获异常并将错误信息返回给客户端
            result = str(e).encode('utf-8')
~~~

3-第三个问题就是如果你在执行某命令时，比如ipconfig，内部有中文返回，采用utf-8的方式对二进制流进行解码输出会失败，所以采用gbk的方式解码，如图

![image.png](https://cdnjson.com/images/2023/05/27/image.png)![image129c2c6f67a0235a.png](https://cdnjson.com/images/2023/05/27/image129c2c6f67a0235a.png)![imageca5d4acda5386b5e.png](https://cdnjson.com/images/2023/05/27/imageca5d4acda5386b5e.png)

4-如何解决粘包问题，例如当我执行systeminfo时

client_socket.recv(4096)

通过修改缓存的大小来减缓粘包问题，故只有tcp流存在粘包，而udp不存在

5-记得通信循环，接收循环



###### 6）创建进程的两种方式？

~~~python
from multiprocessing import Process
import time


def task(name):
    print('%s is running'%name)
    time.sleep(3)
    print('%s is over'%name)


if __name__ == '__main__':
    # 1 创建一个对象
    p = Process(target=task, args=('jason',))
    # 容器类型哪怕里面只有1个元素 建议要用逗号隔开
    # 2 开启进程
    p.start()  # 告诉操作系统帮你创建一个进程  异步
    print('主')
~~~



~~~python
第二种方式 类的继承
from multiprocessing import Process
import time


class MyProcess(Process):
    def run(self):
        print('hello bf girl')
        time.sleep(1)
        print('get out!')


if __name__ == '__main__':
    p = MyProcess()
    p.start()
    print('主')
~~~



###### 7）同步锁和异步锁是什么，python代码如何实现？

同步锁和异步锁都是用于控制多线程或多进程并发访问共享资源的一种技术。

同步锁，也称为互斥锁：，指一次只能有一个线程或进程访问共享资源，其他线程或进程需要等待当前的线程或进程释放锁之后才能继续访问。`threading.Lock()`

异步锁，也称为信号量：在共享资源上允许多个线程或进程访问，但同时只允许一定数量的线程或进程访问，超过数量限制的线程或进程需要等待其他线程或进程释放资源之后才能继续访问

 `asyncio.Semaphore()`

同步锁如下，功能是提取urls.txt中的每一行url判断是否存活，如下在主进程中采用多线程的方式，这里`threadLock = threading.Lock()`生成了一把锁，然后让子线程去抢，能够保证在判断存活之后写入到urlsGetResult.txt时不会同时写入，避免出现错误

~~~python
import sys
import requests
import time
import threading
import time
from requests.packages.urllib3.exceptions import InsecureRequestWarning
requests.packages.urllib3.disable_warnings(InsecureRequestWarning)
threadLock = threading.Lock()

def urlsGet(url):
    timeout = 6
    headers = {
    'User-Agent' : 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/60.0.3112.90 Safari/537.36'
 }
    try:
        get_response = requests.get(url=url,timeout=timeout, headers=headers)
        print(url+" GET请求测试结果-->"+str(get_response.status_code))
        file = open("urlsGetResult.txt", "a", encoding='UTF-8')
        if get_response.status_code in (200, 201, 301, 302):
            time.sleep(4)
            threadLock.acquire()  # 同步锁,用于异步写入，避免同时写入出现错误
            file.write(url)
            file.write('\n')
            file.close()
            threadLock.release()  # 释放同步锁
    except Exception as e:
        pass

if __name__ == '__main__':
    with open('urls.txt', 'r', encoding='utf-8') as fp:
        urls = fp.readlines()
        # timeout=1
        for url in urls:
            url = url.replace("\n", "")
            t = threading.Thread(target=urlsGet, args=(url,))  # 注意传入的参数一定是一个元组!
            t.start()
~~~

~~~
本程序总共用时 9.16 秒
本程序总共用时 9.14 秒
本程序总共用时 9.16 秒
~~~

###### 8）4钟IO模型是哪4种，各自都有什么特点，cobalt strike用到了哪些IO模型，体现在哪些方面？

~~~
阻塞IO：最垃圾的
非阻塞IO：循环接收消耗自己CPU(抢住CPU不放)
多路复用IO：可以接收多个客户端链接，效率最高
异步IO：在监听期间可以去忙别的，对资源占用少
~~~

cobalt strike用到了多路复用IO和异步IO，多路复用IO体现在可以接收多个被控端的上线，异步IO在保证你添加一个监听器后不会暂用过度的系统资源。





























