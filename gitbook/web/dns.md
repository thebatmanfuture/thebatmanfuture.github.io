#### **DNS** ：Domain Name System

一个保存IP地址和域名相互**映射**关系的**分布式**数据库，重要的互联网基础设施，默认使用的TCP/UDP端口号是**53**

~~~
A       IP地址记录,记录一个域名对应的IP地址
AAAA    IPv6 地址记录，记录一个域名对应的IPv6地址
CNAME   别名记录，记录一个主机的别名 
MX      电子邮件交换记录，记录一个邮件域名对应的IP地址，如root@xxxx.com
NS      域名服务器记录 ,记录该域名由哪台域名服务器解析
PTR     反向记录，也即从IP地址到域名的一条记录
TXT     记录域名的相关文本信息
~~~



#### **域传送 **：DNS Zone Transfer

DNS服务器分为：**主服务器**、**备份服务器**和**缓存服务器**。
**域传送**是指后备服务器从主服务器拷贝数据，并用得到的数据更新自身数据库。
在主备服务器之间同步数据库，需要使用“DNS域传送”。

#### 原理

~~~
DNS服务器配置不当，导致匿名用户利用DNS域传送协议获取某个域的所有记录。
通过可以实现DNS域传送协议的程序，尝试匿名进行DNS域传送，获取记录。
~~~

##### 检查方法

###### NSlookup

~~~
1) 输入nslookup命令进入交互式shell;
2) server 命令参数设定查询将要使用的DNS服务器;
3) ls命令列出某个域中的所有域名;
4) exit命令退出
~~~

如图

[![bKC2uV.png](https://s4.ax1x.com/2022/02/28/bKC2uV.png)](https://imgtu.com/i/bKC2uV)

~~~
> nslookup
默认服务器:  public1.114dns.com
Address:  114.114.114.114

> server ring.cugb.edu.cn
默认服务器:  ring.cugb.edu.cn
Address:  202.204.105.1

> ls cugb.edu.cn
[ring.cugb.edu.cn]
 cugb.edu.cn.                   NS     server = ring.cugb.edu.cn
 cugb.edu.cn.                   A      127.0.0.1
 acm                            A      121.194.86.2
 bbs                            A      202.204.105.172
 bm                             A      202.204.105.179
 bsbm                           A      202.204.105.17
 bslt                           A      202.204.109.241
 cas                            A      202.204.105.97
 ce                             A      202.204.99.249
 chushi                         A      202.204.105.243
 cj                             A      202.204.96.111
 cms                            A      202.204.105.179
 computer                       A      202.204.96.202
 csc                            A      202.204.97.60
 cugblx                         A      202.204.105.173
.
.
.
.
 yx                             A      202.204.105.179
 zhsh                           A      202.204.105.198
 zzb                            A      202.204.105.243
>
~~~

可以用fofa搜port="53"，批量爬取ip地址和端口；针对某个目标域名，查询目标所处域dns服务器是否有域传送漏洞，从而获得**横向渗透**的机会和更多的可能性，如上图通过dns域传送漏洞泄露了内网中其他子域名的信息，

攻击者能获取的敏感主要包括：

1）网络的拓扑结构，服务器集中的IP地址段

2）数据库服务器的IP地址

3）测试服务器的IP地址，

4）VPN服务器地址泄露

5）其他敏感服务器

若是不存在漏洞的主机，则可能提示错误Query Refused:

[![bKCqu6.png](https://s4.ax1x.com/2022/02/28/bKCqu6.png)](https://imgtu.com/i/bKCqu6)



解决方案：区域传送是DNS常用的功能，区域传送的漏洞也不是没有办法解决的，严格限制允许区域传送的主机即可，例如一个主DNS服务器应该只允许它的从DNS服务器执行区域传送的功能。

###### nmap

```
nmap --script dns-zone-transfer --script-args dns-zone-transfer.domain=thnu.edu.cn -p 53 -Pn dns1.thnu.edu.cn
```

这里thnu.edu.cn和dns1.thnu.edu.cn是我们需要批量替换的地方，如果存在漏洞则如图

`nmap –script dns-zone-transfer`表示加载nmap文件夹下的脚本文件dns-zone-transfer.nse，扩展名.nse可省略

`–script-args dns-zone-transfer.domain=zonetransfer.me`向脚本传递参数，设置列出记录的域是nwpu.edu.cn

`-p` 53设置扫描53端口

`-Pn`:设置通过Ping发现主机是否存活

[![bKPiKP.png](https://s4.ax1x.com/2022/02/28/bKPiKP.png)](https://imgtu.com/i/bKPiKP)

###### dig

这里涉及dig 一个重要的命令`axfr`:

`axfr` 是q-type类型的一种: axfr类型是Authoritative Transfer的缩写，指请求传送某个区域的全部记录。

我们只要欺骗dns服务器发送一个axfr请求过去，　如果该dns服务器上存在该漏洞，就会返回所有的解析记录值

第一步: 找到解析域名的dns服务器(Server服务器), 我们可以发送一个ns类型解析请求过去

`dig thnu.edu.cn ns`

[![bKPBqK.png](https://s4.ax1x.com/2022/02/28/bKPBqK.png)](https://imgtu.com/i/bKPBqK)

第二步: 然后向该域名发送axfr 请求：

`dig axfr @dns1.thnu.edu.cn thnu.edu.cn`

[![bKPyIe.png](https://s4.ax1x.com/2022/02/28/bKPyIe.png)](https://imgtu.com/i/bKPyIe)