[![5LdmjA.png](https://z3.ax1x.com/2021/10/28/5LdmjA.png)](https://imgtu.com/i/5LdmjA)

[![IX31QU.png](https://z3.ax1x.com/2021/11/21/IX31QU.png)](https://imgtu.com/i/IX31QU)

常见网络安全应急响应场景有勒索病毒、挖矿木马、Webshell、网页篡改、DDoS攻击、数据泄露、流量劫持

###### 1.1 已监听端⼝

netstat -ano | fifindstr LIST

1 左 1 列，程序协议； 2 左 2 列，本地监听地址和端⼝； 3 左 3 列，外部地址（留空）；4 左 4 列，状态为监听； 5 左 5 列，程序 pid。 

###### 1.2 已建⽴连接

netstat -ano | fifindstr EST

###### 1.3 系统进程





**参考链接：**

https://blog.csdn.net/weixin_43650289/article/details/113758030

https://www.freebuf.com/articles/es/159652.html



#### 系统排查

系统排查：在进行受害主机排查时，首先要对主机系统进行基本排查，方便对受害主机有一个初步的了解。

##### 系统基本信息

###### 1.Windows系统

在基础排查时，可以使用Microsoft系统信息工具（Msinfo32.exe）

运行中输入`msinfo32`，可以显示出本地计算机的硬件资源、组件和软件环境的信息。正在运行任务，服务，系统

驱动程序，加载的模块，启动程序，还可以通过在命令行中输入【systeminfo】

###### 2.Linux系统

CPU信息，操作系统信息，模块信息

【lscpu】命令，【uname-a】命令/【cat/proc/version】命令，【lsmod】命令

##### 用户信息

攻击者可能会建立相关账户（有时是隐藏或克隆账户），方便进行远程控制。攻击者会采用的方法主要有如下几种：**第1种**是直接建立一个新的账户（有时是为了混淆视听，账户名称与系统常用名称相似）；**第2种**是激活一个系统中的默认账户，但这个账户是不经常使用的；**第3种**是建立一个隐藏账户（在Windows系统中，一般在账户名称最后加$）。

###### 1.Windows系统

1）cmd命令行

**net user**		可直接收集用户账户信息（注意，此方法看不到以$结尾的隐藏账户）

**net user username**	需查看某个账户的详细信息（username为名字）

2）图形界面方法

在命令行中输入【**lusrmgr.msc**】命令

3）注册表方法

我不常用

4）wmic方法

在命令行中输入【**wmic useraccount**】命令，可以查看系统中的用户信息

###### 2.Linux系统

1）查看系统所有用户信息

查看系统所有用户信息可以在命令行中输入【cat/etc/passwd】命令，后续各项由冒号隔开，分别表示“用户名”“密码加密”“用户ID”“用户组ID”“注释”“用户主目录”“默认登录shell”，如图所示。查询的用户信息中，最后显示“bin/bash”的，表示账户状态为可登录；显示“sbin/nologin”的，表示账户状态为不可登录。

2）查看可登录的账户

在命令行中输入【cat /etc/passwd|grep '/bin/bash'】命令

 3）查看用户错误的登录信息

在命令行中输入【lastb】命令

4）查看所有用户最后的登录信息

在命令行中输入【lastlog】命令

5）查看用户最近登录信息

在命令行中输入【last】命令，可查看用户最近登录信息（数据源为/var/log/wtmp、/var/log/btmp、/var/log/utmp）

6）查看当前用户登录系统情况

在命令行中输入【who】命令，可查看当前用户登录系统情况

##### 启动项

启动项是病毒后门等实现持久化驻留的一种常用方法

###### 1.Windows系统

msconfig

###### 2.Linux系统

cat /etc/init.d/rc.local

cat /etc/rc.local

ls -alt/etc/init.d

##### 任务计划

###### 1.windows系统

PowerShell下输入【Get-ScheduledTask】命令，或者cmd中输入schtasks

###### 2.Linux系统

crontab-l

crontab-u root-l

##### 防火墙

netsh Firewall showstate

#### 进程排查

###### Windows系统

tasklist	tasklist/svc	tasklist/m	wmic process

###### Linux系统

ls-alt/proc/PID






























