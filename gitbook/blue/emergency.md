## 应急响应

主机类型的入侵，黑客一般是直接利用系统层漏洞直接获取系统权限，利用系统资源获取经济利益（挖矿/DDOS）、获取系统最高权限（安装后门/长期维持）。

被攻击者攻击沦陷的主机往往会出现以下现象： CPU使用率过高、CPU使用率过高、系统意外重启/宕机、服务器操作卡顿/网络丢包、非工作时间异常登录/被踢下线。

### 常见系统漏洞

常见系统漏洞类型可分为**系统弱口令**、**远程代码执行**、**本地提权漏洞**。

口令暴破：(RDP/SSH/MySQL/MSSQL/Redis/SMB/IPC 爆破；

远程溢出：(MS17-010/MS-08067/MS12-020 ；

本地提权漏洞(MS15-051/Centos 2.6.32/dirtycow)。

我们可以看一个例子：黑客利用msf利用MS17-010 漏洞直接获取系统权限

~~~
use auxiliary/scanner/smb/smb_ms17_010
set rhosts 10.211.55.17
set rport 445
run
~~~

[![bT3HbR.png](https://s1.ax1x.com/2022/03/12/bT3HbR.png)](https://imgtu.com/i/bT3HbR)

利用windows/smb/ms17_010_eternalblue模块获取权限

[![bT3LUx.png](https://s1.ax1x.com/2022/03/12/bT3LUx.png)](https://imgtu.com/i/bT3LUx)

直接返回了一个shell，成功利用ms17-010 漏洞获取到主机权限。

[![bT3jPK.png](https://s1.ax1x.com/2022/03/12/bT3jPK.png)](https://imgtu.com/i/bT3jPK)

#### Windows检测方法

系统基本信息，EventLog及PowerShell日志分析，注册表/隐藏用户，黑客常用临时目录。

##### 查看系统基本信息

系统性能: `taskmgr` 命令

进程状态: `tasklist`命令/或者使用工具PCHunter/火绒剑小工具

网络状态:`netstat –ano`

自启动:`msconfig/Autoruns`

系统用户：`net user`

##### windows 事件日志分析演示

根据事件ID（每个版本的windows事件日志id表示的事件略有不同）筛选安全日志,一般黑客退出系统前会手动清除日志，这时系统会记录一条ID为1102的“日志清除”记录。

[![bT8ZRS.png](https://s1.ax1x.com/2022/03/12/bT8ZRS.png)](https://imgtu.com/i/bT8ZRS)

指定源IP筛选安全日志进行定位攻击者

这里行首缩进必须是TAB符，或者不缩进，不能是空格。

~~~
<QueryList>

  <Query Id="0" Path="Security">

    <Select Path="Security">

    *[EventData[Data[@Name='IpAddress'] and(Data='10.211.55.2')]]

    </Select>

  </Query>

</QueryList>
~~~

[![bT8NM4.png](https://s1.ax1x.com/2022/03/12/bT8NM4.png)](https://imgtu.com/i/bT8NM4)

##### 注册表查找隐藏用户

有些攻击者在获取主机权限后，为了方便持续控制，会添加一个隐藏的用户在系统里。

注册表查找隐藏用户：

~~~
HKEY_LOCAL_MACHINE\SAM\SAM\Domains\Account\Users
~~~

[![bT82sH.png](https://s1.ax1x.com/2022/03/12/bT82sH.png)](https://imgtu.com/i/bT82sH)

这里我们看见存在一个hacker的隐藏用户。

[![bT8feA.png](https://s1.ax1x.com/2022/03/12/bT8feA.png)](https://imgtu.com/i/bT8feA)

##### 黑客常用的临时目录

普通用户默认可写可执行目录:

~~~powershell
C:\recycler\

C:\windows\temp\

C:\users\public\

C:\Documents and Settings\All Users\Application Data\Microsoft\Media Index\

MySQL:

@@plugin_dir;
~~~



这些目录都需要着重关注的，默认具有可写可执行的目录权限。黑客攻击成功后，为了获取更高的权限，可能会放置一些提权工具或者其他的攻击利用工具。

###### 删除顽固后门-特殊文件名

~~~
del /a:rh \\.\d:\aspwww\upload\lpt2.newfile.asp
~~~

此类文件名属系统设备名，是windows系统保留的文件名，无法通过右键或del加路径直接删除，常见的文件名有com1/lpt1/aux 等。

~~~
del /a:rh //(可选)
~~~

删除只读及隐藏属性的文件

[![bT8oJf.png](https://s1.ax1x.com/2022/03/12/bT8oJf.png)](https://imgtu.com/i/bT8oJf)

#### Linux

系统信息、可疑文件、异常进程、后门情况等，当我们接到一个linux系统应急分析任务，我们最先应该是确认系统命令是否被替换掉，在去进行排查，特别是现在很多勒索病毒、挖矿程序替换原有的命令进行隐藏是常用的手段之一

针对可疑文件可以使用stat进行创建修改时间、访问时间的详细查看，若修改时间距离事件日期接近，有线性关联，说明可能被篡改或者其他。

他。

[![bTG3md.png](https://s1.ax1x.com/2022/03/12/bTG3md.png)](https://imgtu.com/i/bTG3md)

如果不能确定该命令是否被替换，可对该文件进行md5 对比校验，找一台同版本的linux系统，进行对比md5 值，若md5值不一致，那么该命令则已经被替换了，需要我们自行传新的干净的命令文件去该出事服务器进行排查。

## 查看系统基本信息-Linux

系统性能:

```
 top/free -m/df -h/du -sh .
```

top

- 1: 列出CPU多核状态
- c: 显示进程绝对路径
- M: 按内存使用率排序
- P: 按CPU使用率排序
- us: 用户态使用率
- sy: 内核态使用率
- Id: 空闲率

[![bTG80A.png](https://s1.ax1x.com/2022/03/12/bTG80A.png)](https://imgtu.com/i/bTG80A)

## 查看系统基本信息-进程状态

进程状态: ps auxfww/lsof

```
ps auxfww
```

- aux: 显示所有进程
- ww: 多行显示进程
- f: 层级显示

[![bTGGTI.png](https://s1.ax1x.com/2022/03/12/bTGGTI.png)](https://imgtu.com/i/bTGGTI)

网络状态: netstat -antup/lsof –Pni

```
netstat -antup
```

- t: TCP
- u: UDP
- n: 不解析DNS
- p: 显示PID
- l: 不显示连接状态

[![bTGd1S.png](https://s1.ax1x.com/2022/03/12/bTGd1S.png)](https://imgtu.com/i/bTGd1S)

用户状态:

```
w;who;last;lastlog;cat /etc/passwd

w;who //当前在线用户

last;lastlog//最近登录

/etc/passwd  uid=0 // 用户
```

[![bTGap8.png](https://s1.ax1x.com/2022/03/12/bTGap8.png)](https://imgtu.com/i/bTGap8)[![bTGNff.png](https://s1.ax1x.com/2022/03/12/bTGNff.png)](https://imgtu.com/i/bTGNff)

文件修改:

```
file/stat/find/strings
```

#### 定时任务:

```
crontab -l;cat /var/spool/cron/root; ls -ladtr /etc/cron*
```

## 历史命令操作记录分析

```
~/.bash_history //分析攻击行为
```

这里由于黑客没有（忘记）执行history -c命令，导致操作记录依然存在。

[![bTGfcF.png](https://s1.ax1x.com/2022/03/12/bTGfcF.png)](https://imgtu.com/i/bTGfcF)

[![bTGhX4.png](https://s1.ax1x.com/2022/03/12/bTGhX4.png)](https://imgtu.com/i/bTGhX4)

#### ~/.viminfo查看文件编辑记录

从.viminfo文件中可以看到黑客用vim编辑过/var/www/html/backdoor.php、 /tmp/exp.c等文件。

[![bTG5nJ.png](https://s1.ax1x.com/2022/03/12/bTG5nJ.png)](https://imgtu.com/i/bTG5nJ)

## find查找当前用户可写目录文件

#### 使用find命令：

```
! -path "/sys/*"  //排除/sys/目录

-writable //当前用户具有写权限

find / ! -path “/sys/*” ! -path “/proc/*” -writable -ls 2>/dev/null（//以存在问题的账户进行登入）
```

[![bTGIB9.png](https://s1.ax1x.com/2022/03/12/bTGIB9.png)](https://imgtu.com/i/bTGIB9)

## 查找可疑文件-文件时间戳介绍

atime: 最近读取文件内容的时间

mtime: 最近修改文件内容的时间

ctime: 文件所有者、权限、时间戳发生改变的时间

修改文件内容一定会导致mtime及ctime发生改变

只修改文件属性并不会导致mtime发生改变，此时可以通过ctime判断文件属性修改时间，通常黑客伪造时间戳。

比如：黑客通过touch -t 伪造文件最后修改时间，但无法精确到秒数以下。

文件时间戳发生改变的时间也就是change time的时间，所以change time显示了黑客操作的时间。

[![bTGo7R.png](https://s1.ax1x.com/2022/03/12/bTGo7R.png)](https://imgtu.com/i/bTGo7R)

## 查找可疑文件-webshell修改文件时间戳

比如：黑客通过webshell伪造文件最后修改时间。

[![bTG7A1.png](https://s1.ax1x.com/2022/03/12/bTG7A1.png)](https://imgtu.com/i/bTG7A1)

黑客通过webshell修改文件最后修改时间，同样无法精确到秒数以下。

[![bTJSHA.png](https://s1.ax1x.com/2022/03/12/bTJSHA.png)](https://imgtu.com/i/bTJSHA)

一个真实的案例，change time显示了黑客操作的时间。

[![bTJ9AI.png](https://s1.ax1x.com/2022/03/12/bTJ9AI.png)](https://imgtu.com/i/bTJ9AI)

#### logtamper修改ssh登录日志

logtamper工具可以从/var/log/wtmp、/var/run/utmp、/var/log/lastlog中删除或伪造指定用户及IP的登录历史记录 (last)、当前在线记录(w)、上次登录记录(lastlog)，同时伪造文件时间戳。

[![bTJCNt.png](https://s1.ax1x.com/2022/03/12/bTJCNt.png)](https://imgtu.com/i/bTJCNt)

```
./wtmpclean -p root 192.168.1.106 //删除last/w命令中的记录，并尝试伪造文件时间戳（logtamper修改ssh登陆日志）
```

[![bTdyKf.png](https://s1.ax1x.com/2022/03/12/bTdyKf.png)](https://imgtu.com/i/bTdyKf)

此时通过stat命令依然可以看出最近修改时间是伪造的。

[![bTdrxP.png](https://s1.ax1x.com/2022/03/12/bTdrxP.png)](https://imgtu.com/i/bTdrxP)

## 定时任务查找后门

查看指定用户定时任务:

```
crontab  -l -u root
```

手动查看文件:

```
/var/spool/cron/root

/etc/crontab

/etc/cron.d/

/etc/cron.daily/

/etc/cron.hourly/
```

挖矿程序通常都会有守护进程、以及写入启动项进行隐藏自启动。

[![bTd6r8.png](https://s1.ax1x.com/2022/03/12/bTd6r8.png)](https://imgtu.com/i/bTd6r8)

## 黑客常用目录-寻找合适的提权目录

通常黑客通过SSH弱口令、Java反序列化、webshell等获得普通用户权限后，为了进一步提升权限，通常会上传执行一些提权程序。此时黑客需要寻找有写权限的目录，且该目录所在分区要具有exec权限。

常见的提权目录有:

```
/tmp/

/var/tmp/

/dev/shm/  //默认目录权限为777，且具有exec权限，当/tmp/等目录挂载为noexec时可用来本地提权。
```

## 黑客常用临时目录-分析提权过程

黑客先以trs用户创建sb.txt，后又以root身份创建两个临时文件 (怀疑是提权过程中生成的临时文件)。

结合系统内核版本(centos 2.6.32-xxx) 及更新时间可以判断黑客在9月1号21:57成功获取系统root权限

[![bTdIx0.png](https://s1.ax1x.com/2022/03/12/bTdIx0.png)](https://imgtu.com/i/bTdIx0)

## 黑客常用目录-隐藏目录及文件

#### 这几个文件需要特殊关注下，这些文件都是攻击者经常进行隐藏的方法之一：

1:以点开头的文件或目录

2:目录层级较深

3:后缀为.jpg的可执行文件

4:文件名隐藏

[![bTdTMV.png](https://s1.ax1x.com/2022/03/12/bTdTMV.png)](https://imgtu.com/i/bTdTMV)

## 查找系统后门

```
chkrootkit rkhunter（www.chkrootkit.org/rkhunter.sourceforge.net）

chkrootkit：
```

(迭代更新了20年)主要功能：

- 检测是否被植入后门、木马、rootkit
- 检测系统命令是否正常
- 检测登录日志

[![bTd7rT.png](https://s1.ax1x.com/2022/03/12/bTd7rT.png)](https://imgtu.com/i/bTd7rT)

rkhunter主要功能：

- 系统命令（Binary）检测，包括Md5 校验
- Rootkit检测
- 本机敏感目录、系统配置、服务及套间异常检测
- 三方应用版本检测

[![bTwkIH.png](https://s1.ax1x.com/2022/03/12/bTwkIH.png)](https://imgtu.com/i/bTwkIH)

#### RPM check检查：

系统完整性也可以通过rpm自带的-Va来校验检查所有的rpm软件包,有哪些被篡改了,防止rpm也被替换,上传一个安全干净稳定版本rpm二进制到服务器上进行检查

```
./rpm -Va > rpm.log
```

如果一切均校验正常将不会产生任何输出。如果有不一致的地方，就会显示出来。输出格式是8位长字符串, c 用以指配置文件, 接着是文件名. 8位字符的每一个 用以表示文件与RPM数据库中一种属性的比较结果 。 . (点) 表示测试通过。下面的字符表示对RPM软件包进行的某种测试失败：

- 5 MD5 校验码
- S 文件尺寸
- L 符号连接
- T 文件修改日期
- D 设备
- U 用户
- G 用户组
- M 模式e (包括权限和文件类型)

如下图可知ps, pstree, netstat, sshd等等系统关键进程被篡改了

[![bTwEid.png](https://s1.ax1x.com/2022/03/12/bTwEid.png)](https://imgtu.com/i/bTwEid)

#### 入侵处置注意事项

保护好现场环境，操作前截图保存当前状态，对可疑文件进行备份后再删除。

对可疑文件备份时保留文件原始目录结构及属性:

```
mkdir -p /root/backup/var/tmp/

cp -r -p /var/tmp/.backdoor/ /root/backup/var/tmp/

scp -r -p root@<IP>:/var/tmp/.backdoor/ ./backup/

pkill //杀死指定进程名、注销用户。

pkill backdoor

pkill -kill -t pts/0
```

打包指定后缀文件，如jsp/php等脚本文件，避免打包一些没必要的文件。

```
find /var/www -iname "*.php" -print0 | tar -czvf backup.tar.gz --null -T –
```

## 主机入侵处置实验

我方开发人员反馈，有一台服务器被他人入侵，并被盗取了相关资料并进行勒索需要用比特币进行资料赎回，事件发现时间为：2018/10/24 08:00 。

[![bTwVJA.png](https://s1.ax1x.com/2022/03/12/bTwVJA.png)](https://imgtu.com/i/bTwVJA)

通过与开发人员沟通了解： 该服务器为备份服务器、对外开放了远程桌面服务。我方与开发人员沟通、获取到相关账号便进行应急分析。

异常网络分析：

开放了多个高危端口服务，且发现异常与ip为172.20.10.4 的4444端口的通信连接，4444端口默认是攻击者使用msf主机攻击工具的通信连接的默认端口。

[![bTwZRI.png](https://s1.ax1x.com/2022/03/12/bTwZRI.png)](https://imgtu.com/i/bTwZRI)

异常进程分析：

[![bTwusf.png](https://s1.ax1x.com/2022/03/12/bTwusf.png)](https://imgtu.com/i/bTwusf)

异常账号分析：

[![bTwKL8.png](https://s1.ax1x.com/2022/03/12/bTwKL8.png)](https://imgtu.com/i/bTwKL8)

异常启动项分析：

[![bTwQeS.png](https://s1.ax1x.com/2022/03/12/bTwQeS.png)](https://imgtu.com/i/bTwQeS)

信息漏洞情况分析：

未安装漏洞补丁，高危风险

[![bTwldg.png](https://s1.ax1x.com/2022/03/12/bTwldg.png)](https://imgtu.com/i/bTwldg)

事件日志分析

win2008 server 事件id说明

审计登录事件

4634 – 帐户被注销

4647 – 用户发起注销

4624 – 帐户已成功登录

4625 – 帐户登录失败

发现ip：169.254.46.188 存在非法爆破行为。

[![bTw1oQ.png](https://s1.ax1x.com/2022/03/12/bTw1oQ.png)](https://imgtu.com/i/bTw1oQ)

在10/23 0：20 被该ip非法爆破dbapp账号，成功登入服务器。并通过排查最近7天时间段，只存在这一个非法爆破并且成功爆破登入进来的ip

[![bTw8ij.png](https://s1.ax1x.com/2022/03/12/bTw8ij.png)](https://imgtu.com/i/bTw8ij)

通过以上排查分析，可得到初步结论： 通过排查、各个线索综合分析，可初步判定该服务器存在多个高危漏洞，dbapp 账号被攻击者非法爆破入侵，存在多个紧急直接获取权限的漏洞，且被内网ip：172.20.10.4 使用msf 进行非法获取权限进行控制。




