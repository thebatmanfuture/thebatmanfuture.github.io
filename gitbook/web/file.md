#### 文件读取函数

file_get_contents，readfile，highlight_file，fopen，fread，fgetss，fgets，show_source，file，parse_ini_file

~~~php
<?php            
$filename=”test.txt”;
readfile($filename);
?>

<?php
$filename=”test.txt”;
echo file_get_contents($filename);
?>
~~~

#### 敏感信息

~~~js
#Windows：

C:\boot.ini //查看系统版本
C:\Windows\System32\inetsrv\MetaBase.xml //IIS配置文件
C:\Windows\repair\sam //存储系统初次安装的密码
C:\Program Files\mysql\my.ini //Mysql配置
C:\Program Files\mysql\data\mysql\user.MYD //Mysql root
C:\Windows\php.ini //php配置信息
C:\Windows\my.ini //Mysql配置信息

#Linux：

/root/.ssh/authorized_keys //如需登录到远程主机，需要到.ssh目录下，新建authorized_keys文件，并将id_rsa.pub内容复制进去
/root/.ssh/id_rsa //ssh私钥,ssh公钥是id_rsa.pub
/root/.ssh/id_ras.keystore //记录每个访问计算机用户的公钥
/root/.ssh/known_hosts
//ssh会把每个访问过计算机的公钥(public key)都记录在~/.ssh/known_hosts。当下次访问相同计算机时，OpenSSH会核对公钥。如果公钥不同，OpenSSH会发出警告， 避免你受到DNS Hijack之类的攻击。
/etc/passwd // 账户信息
/etc/shadow // 账户密码文件
/etc/my.cnf //mysql 配置文件
/etc/httpd/conf/httpd.conf // Apache配置文件
/root/.bash_history //用户历史命令记录文件
/root/.mysql_history //mysql历史命令记录文件
/proc/self/fd/fd[0-9]*(文件标识符)
/proc/mounts //记录系统挂载设备
/porc/config.gz //内核配置文件
/var/lib/mlocate/mlocate.db //全文件路径
/porc/self/cmdline //当前进程的cmdline参数`
~~~

### 攻击

用google语法搜索

~~~js
# 谷歌语法
inurl:”readfile.php?file=
inurl:”read.php?filename=
inurl:”download.php?file=
inurl:”down.php?file=
# 连接：
readfile.php?file=**.txt
download.php?file=**.rar
# 参数名：
&RealPath=、&readpath=、&FilePath=、&filepath=、&Path=、&path=、&Inputfile=、&inputfile=、&url=、&urls=、&Lang=、&dis=、&Data=、&data=、&readfile=、&ﬁlep=、&Src=、&src=、&menu=、META-INF= 、WEB-INF
~~~

### WAF绕过

#### 任意文件下载

（1）相对路径：所有WAF文件包含规则通常会检测连续的“../”。根据vfs解析路径的语法，解析到“//”文件路径不变，解析到“/./”文件路径依然。 通过避免连续的”../”，从而绕过WAF文件包含规则。Eg: ././..///./.././/../etc//passwd，它等价于../../../etc/passwd。

（2）绝对路径：例如 /etc/./passwd 与 /etc/passwd 是等价的。还可以通过组合“/./”、“//”进行绕过

~~~
/etc///.//././/passwd
/etc/passwd /etc././././passwd
~~~

这里两个的//变成一个/，这里/./可以看成是一个/

[![bQ7PeA.png](https://s4.ax1x.com/2022/03/01/bQ7PeA.png)](https://imgtu.com/i/bQ7PeA)















### 防御

- 过滤 `.` 点，使用户在url中不能回溯上级目录
- 正则严格判断用户输入的参数
- `php.ini`配置`open_basedir`限定文件访问范围






#### 文件删除

文件删除漏洞出现在有文件管理功能的应用上比较多，这些应用一般也都有文件上传和读取等功能，它的漏洞原理跟文件读取漏洞是差不多的，不过是利用的函数不一样而已，一般也是因为删除的文件名可以用../跳转，或者没有限制当前用户只能删除他该有权限删除的文件。常出现这个漏洞的函数是 unlink()，不过老版本下 session_destroy() 函数也可以删除文件。

[![bcSmh6.png](https://s1.ax1x.com/2022/03/08/bcSmh6.png)](https://imgtu.com/i/bcSmh6)

判断如果不是 sql 文件后，就直接在 databack 目录删除提交的文件名，代码中$filenames 函数从 GET 中提交，只要请求：`filenames=../../index.php` 即可删除 index.php 文件。















