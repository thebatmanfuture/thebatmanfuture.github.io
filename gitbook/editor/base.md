#### ewebeditor

------

##### 关键路径(弱口令)

Admin_Login.asp 登录页面

Admin_Default.asp 管理首页

Admin_Style.asp 样式页面

Admin_UploadFile.asp 上传文件页面

Upload.asp 上传文件业

Admin_ModiPwd.asp 密码

eWebEditor.asp 数据库文件

/db/ewebeditor.mdb 默认数据库路径

ewebeditor 2.8.0版本以前为默认后台 路径 ：

ewebeditor/admin_login.asp

以后版本默认后台 路径：

admin/login.asp,或admin/editor/login_admin.asp

##### 数据库路径泄露

若后台使用默认username和password无法登录。

可以试试直接下载../db/ewebeditor.mdb或者../db/ewebeditor.asp,

username和password在eWebEditor_System表中，经过了md5加密

##### 目录遍历漏洞

选择上传文件管理，随意选择一个目录，输入路径：dir=..,可见，程序在实现上没有充分过滤用户输入的../之类的目录跳转符，导致恶意用户可以在url处通过目录跳转来遍历服务器上的任意文件。这里的目录跳转符可以是../，也可是../的ASCII编码或者是unicode编码等

##### 文件上传漏洞

###### 黑名单

(html | htm | php | php2 | hph3 | php4 | php5 | asp | aspx | ascx | jsp| cfm | bat | exe | com | dll | vbs | js | reg | cgi | htaccess | asis | sh)

###### 白名单

###### 验证content-type

###### 验证文件内容

验证文件中的危险函数如命令执行函数eval(),system()，这里就是webshell免杀知识点

##### SQL注入漏洞

默认表名：eWebEditor_System，默认列名：sys_UserName、sys_UserPass

1.ewebeditor以前版本都存在注入

2.2.7以下的版本注入

3.ewebeditor v2.1.6存在注入可以用union select添加上传后缀进行上传！

修改红色部分的路径，然后自动上传.cer文件！漏洞原因是因为sStyleName变量直接从style中读取，并没有过滤，所以可以包含任意字符！用select在ewebeditor_style表中查找s_name为sStyleName的记录，找不到就提示出错！在sStyleName变量中用union来构造记录，我们可以在sAllowExt中加入"|cer"、"|asa"等！

4.ewebeditor的upload.asp文件存在注入漏洞

##### session欺骗

asp版ewebeditor相关版本(5)

适用于一些设置不当的虚拟主机。当旁注得到一个webshell,而目标站存在ewebeditor却不能找到密码的时候可以尝试欺骗进入后台！

新建一个.asp文件，内容如下:然后访问这个文件，再访问ewebeditor/admin_default.asp！欺骗进入后台！不过很老了！

php版ewebeditor 3.8

php版本后台是调用../ewebeditor/admin/config.php 文件

1，找到登陆后台

进入后台后随便输入一个用户和密码 必须是出错的时候，

然后这时候你清空浏览器的url,然后输入

后三次回车,

2 然后输入正常情况才能访问的文件../ewebeditor/admin/default.php就可以进后台了

##### 后台跳过认证漏洞

1.访问后台登陆页面！随便输入帐号密码，返回错误！

2.然后清空浏览器在地址栏输入

3.然后再清空地址栏，在路径里输入后台登陆后的页面，比如: admin_default.asp admin/default.asp等。

##### 利用远程上传功能

1.打开编辑页面，然后图片，选择输入url比如:http://site.com/1.gif.asp!然后选择上传远程文件！自动就把1.gif.asp保存在上传目录内！(利用上面说的远程上传的方式！可以得到webshell！成功率取决于，虚拟主机的安全设置！)太老

##### 任意文件删除

此漏洞存在于Example\NewsSystem目录下的delete.asp文件中

有几个版本的ewebeditor上传类型是在security.asp文件控制的！直接删除该文件可以上传任意webshell！

#### Ueditor

------

#### 漏洞（一）：net.版本1.4.3文件上传

http://domain/controller.ashx?action=catchimage 参数为catchimage,

case:”catchimage”,这里会实例化类,在具体实现文件上传的类的方法里只验证了content-type类型

需准备一个图片马儿，远程shell地址需要指定扩展名为 1.gif?.aspx

采用远海在先知的漏洞说明

漏洞主要在controller.ashx 文件下

原因:

其第14行接收一个action参数

![图片](http://inews.gtimg.com/newsapp_bt/0/13986300763/641)

后端根据action值进行方法调用。且漏洞产生位置为catchimage（远程文件抓取）。

漏洞（二）：XML文件上传导致存储型XSS

\----------------------------------------

上传路径如下

1，点击上传

![图片](http://inews.gtimg.com/newsapp_bt/0/13986300764/641)

上传内容如下

2，buprsuit抓包拦截

3，将uploadimage类型改为uploadfile，此时进方法为uploadfile,执行文件上传的操作,并修改文件后缀名为xml,最后复制上xml代码即可

4，访问listfile方法,http://domain/ueditor/php/controller.php?action=listfile即可查看文件的返回路径

5，访问上传的url文件,触发payload

漏洞（三）：SSRF漏洞

\----------------------------------------

1.该漏洞存在于1.4.3的jsp版本中。但1.4.3.1版本已经修复了该漏洞。

已知该版本ueditor的ssrf触发点：

2.构造poc

漏洞（四）：上传存储型xss漏洞

\----------------------------------------

1，没有过滤iframe2016-12-22

poc 如下

![图片](http://inews.gtimg.com/newsapp_bt/0/13986300845/641)

2，1.4.3版本

任意编辑内容，burp抓包替换如下即可

![图片](http://inews.gtimg.com/newsapp_bt/0/13986300846/641)

#### kindeditor

漏洞（一）：任意文件上传(html)文件漏洞

\----------------------------------------

影响版本如下:kindeditor版本

上传路径如下

修改content-type:text/plain 文件名为 html后缀即可

![图片](http://inews.gtimg.com/newsapp_bt/0/13986300919/641)

漏洞（二）：上传文件漏洞

\----------------------------------------

影响版本：KindEditor 3.5.2~4.1

1.打开编辑器，将一句话改名为1.jpg 上传图片，打开文件管理，进入“down”目录，跳至尾页，最后一个图片既是我们上传的一句话

![图片](http://inews.gtimg.com/newsapp_bt/0/13986300920/641)

2.打开谷歌浏览器的 审查元素

jpg修改asp

![图片](http://inews.gtimg.com/newsapp_bt/0/13986300921/641)

漏洞（三）：上传解析漏洞

\----------------------------------------

影响版本：

利用iis6.0 解析漏洞

漏洞（四）：列目录漏洞

\----------------------------------------

版本：KindEditor 3.4.2KindEditor 3.5.5

访问 可以爆出绝对路径

爆出绝对路径

D:AppServ/www67cms/kindeditor/php/file_manager_json.php

这时将遍历d:/AppServ/www/67cms/下的所有文件和文件名

#### Fckeditor

查看版本与文件上传地址

1.查看编辑器版本 fckeditor/_whatsnew.html

2.常用上传地址

3.FCKeditor中test 文件的上传地址

4.其他上传地址

一般很多站点都已删除_samples 目录，可以试试。

FCKeditor/editor/fckeditor.html 不可以上传文件，可以点击上传图片按钮再选择浏览服务器即可跳转至可上传文件页。

漏洞（一）：任意文件上传

\----------------------------------------

版本：php

在处理PHP 上传的地方并未对Media类型进行上传文件类型的控制，导致用户上传任意文件！

漏洞（二）：任意文件上传

\----------------------------------------

[shell.php和shell.php+空格是2 个不同的文件未测试。继续上传同名文件可变为shell.php;(1).jpg 也可以新建一个文件夹，只检测了第一级的目录，如果跳到二级目录就不受限制

漏洞（三）：列目录

\----------------------------------------

影响版本：

1.修改CurrentFolder 参数使用 ../../来进入不同的目录

2.根据返回的XML 信息可以查看网站所有的目录。

也可以直接浏览盘符：

JSP 版本：

漏洞（四）：爆路径漏洞

\----------------------------------------

漏洞（五）：突破建立文件夹

\----------------------------------------

漏洞（六）：FCKeditor被动限制策略所导致的过滤不严问题

\----------------------------------------

影响版本: FCKeditor x.x

FCKeditor v2.4.3中File 类别默认拒绝上传类型：

html|htm|php|php2|php3|php4|php5|phtml|pwml|inc|asp|aspx|ascx|jsp|cfm|cfc|pl|bat|exe|com|dll|vbs|js|reg|cgi|htaccess|asis|sh|shtml|shtm|phtm

Fckeditor 2.0

允许上传asa、cer、php2、php4、inc、pwml、pht 后缀的文件

上传后它保存的文件直接

用的$sFilePath = $sServerDir . $sFileName

而在apache 下

因为”Apache 文件名解析缺陷漏洞”也可以利用之，另建议其他上传漏洞中定义TYPE 变量时使用File 类别来上传文件,根据FCKeditor 的代码，其限制最为狭隘。

在上传时遇见可直接上传脚本文件固然很好，但有些版本可能无法直接上传可以利用在文件名后面加.点或空格绕过，也可以利用2003 解析漏洞建立xxx.asp文件夹或者上传xx.asp;.jpg!

漏洞（六）：jsp任意上传

\----------------------------------------

参考链接：

http://www.voidcn.com/article/p-aaraonso-bwy.html

https://xz.aliyun.com/t/8488

https://baijiahao.baidu.com/s?id=1633194896992314165&wfr=spider&for=pc