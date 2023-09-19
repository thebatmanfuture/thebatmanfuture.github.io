### socket

应用层与传输层之间；本质是传输层的封装，便于应用层使用（api）

[![ItMq9H.png](https://z3.ax1x.com/2021/11/09/ItMq9H.png)](https://imgtu.com/i/ItMq9H)

[![It129g.png](https://z3.ax1x.com/2021/11/09/It129g.png)](https://imgtu.com/i/It129g)

[![It1qCF.png](https://z3.ax1x.com/2021/11/09/It1qCF.png)](https://imgtu.com/i/It1qCF)

[![It3K58.png](https://z3.ax1x.com/2021/11/09/It3K58.png)](https://imgtu.com/i/It3K58)

[![It3wPU.png](https://z3.ax1x.com/2021/11/09/It3wPU.png)](https://imgtu.com/i/It3wPU)

~~~
客户端：
	开始socket
	发送connect建立连接
	发送数据
	关闭连接
服务端：
	开始socket
	绑定本地地址，客户端随意(上马就是客户端主动连服务端)
	开启监听
	接受请求
	接受或者发送数据
	关闭连接
~~~



https://www.cnblogs.com/dalaoban/p/9331113.html

https://www.zhihu.com/question/29637351/answer/534704474

[![ItGiXd.png](https://z3.ax1x.com/2021/11/09/ItGiXd.png)](https://imgtu.com/i/ItGiXd)

~~~python
import socket


~~~



![img](https://images2015.cnblogs.com/blog/1036857/201612/1036857-20161210123107304-1582863963.png)

TCP

~~~python
服务端：
"""
# 服务端应该满足两个特点：
# 1、一直对外提供服务
# 2、并发地服务多个客户端
import subprocess
from socket import *

server=socket(AF_INET,SOCK_STREAM)
server.setsockopt(SOL_SOCKET,SO_REUSEADDR,1) #就是它，在bind前加
server.bind(('127.0.0.1',8082))
server.listen(5)

#  服务端应该做两件事
# 第一件事：循环地从板连接池中取出链接请求与其建立双向链接，拿到链接对象
while True:
    conn,client_addr=server.accept()

    # 第二件事：拿到链接对象，与其进行通信循环
    while True:
        try:
            cmd=conn.recv(16384)
            if len(cmd) == 0:break
            obj=subprocess.Popen(cmd.decode('gbk'),
                             shell=True,
                             stdout=subprocess.PIPE,
                             stderr=subprocess.PIPE
                             )

            stdout_res=obj.stdout.read()
            stderr_res=obj.stderr.read()
            print(len(stdout_res)+len(stderr_res))
            # conn.send(stdout_res+stderr_res) # ???
            conn.send(stdout_res)
            conn.send(stderr_res)

            # with open("1.mp4",mode='rb') as f:
            #     for line in f:
            #         conn.send(line)
        except Exception:
            break
    conn.close()
~~~

~~~python
客户端：
from socket import *

client=socket(AF_INET,SOCK_STREAM)
client.connect(('127.0.0.1',8082))

while True:
    cmd=input('请输入命令>>：').strip()
    if len(cmd) == 0:continue
    client.send(cmd.encode('gbk'))

    # 解决粘包问题思路：
    # 1、拿到数据的总大小total_size
    # 2、recv_size=0,循环接收，每接收一次，recv_size+=接收的长度
    # 3、直到recv_size=total_size，结束循环
    cmd_res=client.recv(16384) # 本次接收，最大接收1024Bytes
    print(cmd_res.decode('gbk')) # 强调：windows系统用gbk


# 粘包问题出现的原因
# 1、tcp是流式协议，数据像水流一样粘在一起，没有任何边界区分
# 2、收数据没收干净，有残留，就会下一次结果混淆在一起

# 解决的核心法门就是：每次都收干净，不要任何残留
~~~

UDP

~~~python
服务端：
import socket

server=socket.socket(socket.AF_INET,socket.SOCK_DGRAM)
server.bind(('127.0.0.1',8080))
res1=server.recvfrom(2) # b"hello"
print(res1)
res2=server.recvfrom(3) # b"world"
print(res2)

server.close()
~~~

~~~python
客户端：
import socket

client=socket.socket(socket.AF_INET,socket.SOCK_DGRAM)
client.sendto(b'hello',('127.0.0.1',8080))
client.sendto(b'world',('127.0.0.1',8080))

client.close()
~~~





























