### 威胁情报

#### 第三方平台（微步在线）

在作溯源反制时，对指定IP地址，域名，服务器等判断是否是跳板机还是真实攻击机起到重要作用。

微步在线：https://x.threatbook.cn/

比如https://x.threatbook.cn/v5/ip/45.146.164.110，IP地址45.146.164.110

[![bIKHRs.png](https://s1.ax1x.com/2022/03/11/bIKHRs.png)](https://imgtu.com/i/bIKHRs)

这里URL处可以批量写爬虫爬取信息

#### 自建威胁情报库

由于国内各个网络安全公司不会将自己的数据库分享，也是用与我一样的思路爬取国外信息为主

##### 黑名单IP（C＆C，恶意软件，垃圾邮件，网络爬虫）

~~~python
我们需要将下面的命令写入cron中，可用来每5分钟自动下载一次黑名单IP
"""
crontab -e
"""
在配置文件中添加下面两行
"""
*/5 * * * * cd && wget reputation.alienvault.com/reputation.data
*/5 * * * * cd && wget https://myip.ms/files/blacklist/general/latest_blacklist.txt
~~~

~~~python
c2=open("c2-ipmasterlist.txt","r")
bl=open("latest_blacklist.txt","r")
reputation=open("reputation.data","a")

#以行来分开下载的数据
for line in c2:
  ip = line.split(',')
  reputation.write(ip[0]+ ",c2\n")
for line in bl:
  ip = line.split()
  try:
    reputation.write(ip[0]+ "\n")
  except:
    pass
~~~

这里的话就是将其他两个里的都放入第三个文件中

##### 开启crontab日志

crontab默认情况下是不执行开启日子的，所以一开始写完后，不能执行，想查看日志，却找不到。所以先开启定时任务的日志来查看

修改rsyslog服务，将 /etc/rsyslog.d/50-default.conf 文件中的 #cron.* 和#daedon.* 前的 # 删掉；

[![bID8oQ.png](https://s1.ax1x.com/2022/03/11/bID8oQ.png)](https://imgtu.com/i/bID8oQ)

用以下命令重启rsyslog服务：

```
service rsyslog restart
```

然后再重启crontab服务：

```
service cron restart
```









