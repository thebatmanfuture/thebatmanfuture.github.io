## Windows 认证

### Kerberos

[![X5T42Q.png](https://s1.ax1x.com/2022/06/14/X5T42Q.png)](https://imgtu.com/i/X5T42Q)



[![X57VRe.png](https://s1.ax1x.com/2022/06/14/X57VRe.png)](https://imgtu.com/i/X57VRe)

~~~http
AS_REQ: Client向KDC发起AS_REQ,请求凭据是Client hash加密的时间戳
AS_REP: KDC使用Client hash进行解密，如果结果正确就返回用krbtgt hash加密的TGT票据，TGT里面包含PAC,PAC包含Client的sid，Client所在的组。
TGS_REQ: Client凭借TGT票据向KDC发起针对特定服务的TGS_REQ请求
TGS_REP: KDC使用krbtgt hash进行解密，如果结果正确，就返回用服务hash 加密的TGS票据(这一步不管用户有没有访问服务的权限，只要TGT正确，就返回TGS票据)
AP_REQ: Client拿着TGS票据去请求服务
AP_REP: 服务使用自己的hash解密TGS票据。如果解密正确，就拿着PAC去KDC那边问Client有没有访问权限，域控解密PAC。获取Client的sid，以及所在的组，再根据该服务的ACL，判断Client是否有访问服务的权限。
~~~

![IGVwnJ.png](https://z3.ax1x.com/2021/11/08/IGVwnJ.png)

![IGZtUI.png](https://z3.ax1x.com/2021/11/08/IGZtUI.png)



![](https://z3.ax1x.com/2021/11/08/IGMWGR.png)













### 白银票据

![](https://z3.ax1x.com/2021/11/08/IGlm9A.png)

###### 特点：

1. 不需要和KDC交互
2. 需要目标服务的ntlm hash

###### 在第三步中的Ticket组成：

Ticket=Server hash（server session key+client info+end time）

当拥有server hash时，就可以伪造一个不经过KDC认证的一个Ticket

所以一切来源都是server hash

##### 伪造

[mimikatz](https://hub.fastgit.org/gentilkiwi/mimikatz)

###### 操作：

![](https://z3.ax1x.com/2021/11/08/IG82gs.png)

**privilege::debug   提取权限**
**sekurlsa::logonpasswords   抓取密码**

mimikatz是从 lsass.exe进程中获取windows的账号及密码的，
这时，我们可以帮对方安装一个procdump64.exe（这是微软自己的工具，可以放心使用）

然后从 lsass.exe进程里导出一个 包含账号密码信息的lsass.dmp 文件，再把这个dmp文件传回来，最后在自己的环境下运行mimikatz，从 dmp文件里读取信息。

1、安装procdump64.exe

2、从procdump64.exe里导出lsass.dmp

**procdump64.exe -accepteula -ma lsass.exe lsass.dmp**

3、将lsass.dmp传回本地

4、使用本地的mimikatz.exe读取lsass.dmp

**mimikatz.exe "sekurlsa::minidump lsass.dmp" "sekurlsa::logonPasswords full" "exit"**



伪造票据：

**mimikatz "kerberos::golden /domain:<域名> /sid:<域SID> /target:<目标服务器主机名> /ptt" exit**

由于白银票据需要目标服务器的hash，所以无法生成对应域内所有服务器的票据，也不能通过TGT去申请，因此只能针对服务器上的某些服务去伪造，如下：

![](https://z3.ax1x.com/2021/11/08/IGJHXR.png)

其中DCSync是用于域同步，如果拿到域控的hash，也可以生成白银票据，将其账号内的网络对象信息全导出

##### 防御

1. 尽量保证服务器凭证不被窃取
2. 开启PAC，特权属性证书保护功能，PAC主要是规划服务器将票据发送给kerberos服务，由kerberos服务验证票据是否有效

开启方式：将注册表中的HKEY_MACHINE\SYSTEM\CurrentControl\Kerberos\Parameters中的ValidateKdcPacSignature设置为1

### 黄金票据

###### 特点：

1. 需要和DC通信
2. 需要krbtgt用户的hash（也就是KDC hash）

##### 伪造

mimikatz "kerberos::golden /domain:<域名> /sid<域SID> /rc4:\<KRBTGT NTLM Hash> /user:<任意用户名> /ptt" exit

##### 防御

经常更新krbtgt用户的密码，使得原有票据失败，最根本办法是不允许域管账号登录其他服务器，

### Windows Access Token

访问令牌，用来描述线程和进程安全上下文的一个对象，不同用户登录计算机后，都会生成一个Access Token，这个Token在用户创建线程或者进程时被使用，不断地拷贝

###### 种类：

两种（主令牌，模拟令牌）

一般情况下，用户双击一个程序，都会拷贝"explorer.exe"的Access Token。

当用户注销后，系统将会使主令牌切换位模拟令牌，不会将令牌清除，只有在重启机器才能消除

#### Windows Access Token-SID

安全标识符，唯一的字符串，可以代表一个账号，一个用户组，或者一次登录，通常还有一个SID固定列表，列入everyone这种内置账号，默认拥有固定的SID



### Enhanced Golden Tickets(增强型金票)

在Golden Ticket部分说明可利用 krbtgt 的密码 HASH 值生成金票，从而能够获取域控权限同时能够访问域内其他主机的任何服务。但是普通的金票不能够跨域使用，也就是说金票的权限被限制在当前域内

在一个域林中，域控权限不是终点，根域的域控权限才是域渗透的终点

普通的黄金票据被限制在当前域内，在2015年Black Hat USA中国外的研究者提出了突破域限制的增强版的黄金票据。通过域内主机在迁移时SIDHistory属性中保存的上一个域的SID值制作可以跨域的金票。这里没有迁移，直接拿根域的SID号做演示

如果知道根域的 SID 那么就可以通过子域的 KRBTGT 的 HASH 值，使用 mimikatz 创建具有 Enterprise Admins组权限（域林中的最高权限）的票据。环境与上文普通金票的生成相同

### 委派

委派在域环境中其实是一个很常见的功能，对于委派的利用相较于先前说的几种攻击方式较为“被动”，但是一旦利用也会有很大的危害。

先说一下委派是什么意思吧：在域中如果出现 A 使用 Kerberos 身份验证访问域中的服务 B，而 B 再利用 A 的身份去请求域中的服务 C ，这个过程就可以理解为委派

![img](https://img2018.cnblogs.com/blog/1561366/202001/1561366-20200104212135824-1443760404.png)

User访问主机 s2 上的 HTTP 服务，而 HTTP 服务需要请求其他主机的 SQLServer 数据库，但是 S2 并不知道 User 是否有权限访问 SQLServer ，这时 HTTP 服务会利用 User 的身份去访问 SQLServer，只要 User 有权限访问 SQLServer 服务就能访问成功。

而委派主要分为非约束委派（Unconstraineddelegation）和约束委派（Constrained delegation）两个方式，下面分别介绍两种方式如何实现。



参考链接：

https://www.136.la/jingpin/show-157277.html