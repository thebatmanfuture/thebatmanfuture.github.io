#### IDE

[IBAT](https://www.xiazaiba.com/html/27063.html)

#### 手册

[w3cschool批处理入门](https://m.w3cschool.cn/pclrmsc/tdsgnn.html)

#### 一.DOS命令

###### echo

~~~powershell
echo 123								//输出123
echo .									//输出换行
@echo off//								//关闭回显
echo Y|rd /s c:\abc						//输出默认字符Y表示继续
echo 123 > 1.txt	echo 123 >> 1.txt	//输入123到123.txt中，>>是追加写
echo < 1.txt							//从1.txt中获取命令执行
~~~

###### rem和::

~~~powershell
rem 123									//注释123且回显
::	123									//注释123且回不显
~~~

###### cd

~~~powershell
cd /d d:blog/blog						//切换目录
cd..									//后退一个路径
cd /									//退回到根路径
cd										//显示当前路径
~~~

###### dir

~~~powershell
dir										//显示目录
dir /a									//显示当前目录所有文件
dir c:/windows/system32					//显示c盘下system32的素有
~~~

###### attrib

~~~powershell
attrib +h +a +s +r 1.txt				//给1.txt文件加各种数学，+h是隐藏，+a是文件，+s是系统保护文件，+r只读文件
attrib					//查看文件的属性
~~~

###### del

~~~powershell
del 1.txt		//删除
del /p 1.txt	//删除会有提示
del /s 1.txt    //递归删除
~~~

###### copy

~~~powershell
copy c:\123.txt d:\abc.bat  复制文件
copy con 1.txt	屏幕输入的内容写入到1.txt中
copy 1.jpg/b+1.php 2.jpg	把1.php插入到1.jpg组合成2.jpg
~~~

###### md

~~~powershell
md /d c:\
~~~

###### rd

~~~powershell
rd /s /q d:\123
~~~

###### ren

~~~powershell
ren d:\123.txt 456.bat
~~~



###### move

~~~powershell
move d:\123.txt 456.bat
~~~

###### find

~~~powershell
find "abc" d:\abc.txt
find /i "abc" d:\abc.txt
find /c "abc" d:\abc.txt
~~~

#### 二.网络命令和符号

###### ping

~~~powershell
ping 1 -n 1 -w 10000 2>nul 1>nul	批处中可以用于延时10秒
~~~

###### ipconfig

~~~powershell
ipconfig /all
ipconfig /flushdns
~~~

###### arp

~~~powershell
arp -a 127.0.0.1	查看arp缓存，防止arp欺骗攻击
arp -s IP MAC		配置静态ip，将ip和mac地址绑定
arp -d IP			删除静态ip
~~~

###### start

~~~powershell
start title https://batmanfuture.cn/
~~~

###### shutdown

~~~powershell
at 12:00 shutdown -s	通过-s参数，在12：00执行关闭计算机
shutdown -r -t 60	60秒后重启计算机。这里是设置重启倒计时为60秒。如果没有设置时间则默认为30秒
shutdown -a	终止当前的关机进程
~~~

###### taskkill

~~~powershell
taskkill /im qq.exe	指定将终止的进程的图像名称
taskkill /f /im spoolsv.exe	指定将强制终止的进程
~~~

