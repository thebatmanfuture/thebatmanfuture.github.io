### 信息收集

#### 网络入口

外网出口，C段，Wi-Fi(SSID，认证信息)，VPN(厂商，登录方式)，邮件网关，手机APP，小程序后台，OA，边界网络设备



###### CMS 识别技术

在线识别，关键字

###### 源码获取技术

扫源码，源码下载，内部资料

###### 架构信息获取

http header

cookies

源代码，powered by discuz

特殊文件和文件夹

#### 域名信息

搭建习惯-目录型站点

搭建习惯-端口类站点

搭建习惯-子域名站点

搭建习惯-类似域名站点

搭建习惯-旁注,C 段站点

搭建习惯-搭建软件特征站点

##### Whois

https://tool.lu/whois/

##### 搜索引擎搜索

###### 谷歌语法

参考资料：

https://blog.csdn.net/weixin_44472319/article/details/88735539

https://www.fujieace.com/penetration-test/google-hacking.html

https://www.cnblogs.com/oneWhite/p/11570170.html

##### 第三方查询

[DNSDumpster](https://dnsdumpster.com/)

[Virustotal](https://www.virustotal.com/)

[CrtSearch](https://crt.sh/)

[threatminer](https://threatminer.org/)

[Censys](https://censys.io/ipv4)

##### 域名相关性

- [查询域名注册邮箱](https://tool.lu/whois/)
- [通过域名查询备案号](https://beian.miit.gov.cn/#/Integrated/index)
- [通过备案号查询域名](https://beian.miit.gov.cn/#/Integrated/index)
- [反查注册邮箱](https://tool.lu/whois/)
- [反查注册人](https://tool.lu/whois/)
- [通过注册人查询到的域名在查询邮箱](https://beian.miit.gov.cn/#/Integrated/index)
- [通过上一步邮箱去查询域名](https://beian.miit.gov.cn/#/Integrated/index)
- [查询以上获取出的域名的子域名](https://fofa.so/)

##### CDN绕过技术

先多点ping，判断是否存在CDN，https://ping.chinaz.com/batmanfuture.cn

使用户能以最快的速度，从最接近用户的地方获得所需的信息，彻底解决网络拥塞，提高响应速度

（1）子域名查询。子域名流量小，可能不做CDN

（2）邮件服务查询。大部分不会做CDN（正反向）

（3）国外地址请求。国外可能没有外设CDN，会默认寻找真实IP地址

（4）遗留文件，扫描全网。phpinfo找ip，fofa扫全网

（5）黑暗引擎搜索特定文件。fofa，shodan，zoomeye搜索特定文件，比如ico，hash

（6）dns 历史记录，以量打量。以前没做CDN时，dns解析到了正确的ip地址，ddos攻击，把cdn打光后就到真实ip地址

#### 端口信息

##### 常见端口

见http://gitbook.batmanfuture.cn/web%20security/web%20security.html

##### 端口扫描

###### 全扫描

扫描主机尝试使用三次握手与目标主机的某个端口建立正规的连接，若成功建立连接，则端口处于开放状态，反之处于关闭状态

###### 半扫描

半扫描也称SYN扫描，在半扫描中，仅发送SYN数据段，如果应答为RST，则端口处于关闭状态，若应答为SYN/ACK，则端口处于监听状态。

###### FIN扫描

FIN扫描是向目标发送一个FIN数据包，如果是开放的端口，会返回RST数据包，关闭的端口则不会返回数据包，可以通过这种方式来判断端口是否打开。

##### 批量搜索

###### fofa

[![IuakE4.png](https://z3.ax1x.com/2021/11/05/IuakE4.png)](https://imgtu.com/i/IuakE4)

###### zoomeye

[![IuafqU.png](https://z3.ax1x.com/2021/11/05/IuafqU.png)](https://imgtu.com/i/IuafqU)

###### shodan

https://www.cnblogs.com/greencollar/p/13810250.html

#### 站点信息

###### 判断网站操作系统

（1）TTL

1、WINDOWS NT/2000   TTL：128
2、WINDOWS 95/98     TTL：32
3、UNIX              TTL：255
4、LINUX             TTL：64
5、WIN7          TTL：64

（2）抓包分析

（3）nmap描扫，其中某些操作系统开放指定端口，比如iis中ftp端口号是21

（4）telnet连接测试，不过现在大家一般很少用这种

（5）windows不区分大小写，linux区分大小

###### 脚本语言的判断：

（1）根据传递的cookie值，比如 PHP 使用的会话ID是 PHPSESSID，JSP 使用的会话 ID 是 JSESSION

（2）Wappalyzer，火狐的插件

（3）响应包的server值，有时候网站的响应包会携带server信息，如下，这种情况多存在内网中。公网很少会携带这个server信息

###### 敏感信息泄露

在xctf中有详细记录备份文件和敏感信息泄露

###### 后端语言

看后缀

###### 前端框架

查看源代码，如jQuery / BootStrap / Vue / React / Angular等

###### 中间件

有关web服务器，中间件，容器之间的区别看[这篇文章](https://blog.csdn.net/qq_36119192/article/details/84501439)

~~~shell
web服务器：IIS、Apache、Nginx、Tomcat、Jboss、Jetty、Weblogic、Webshere、Glasshfish、Lighttpd等
web中间件：Tomcat、Jboss、Jetty、Weblogic、Webshere、Glasshfish等
web容器：IIS(asp容器)、Tomcat(servlet容器)、Jboss(EJB容器)
# web服务器>web中间件>web容器
~~~

如 Apache / Nginx / IIS 等

1. 查看header中的信息
2. 根据报错信息判断
3. 根据默认页面判断

###### Web容器

如Tomcat / Jboss / Weblogic等

###### 后端框架

根据Cookie判断

根据CSS / 图片等资源的hash值判断

根据URL路由判断				如 wp-admin

根据网页中的关键字判断

根据响应头中的X-Powered-By

###### CDN信息

常见的有Cloudflare、yunjiasu

###### ☆探测有没有WAF，如果有，什么类型的

有WAF，找绕过方式

1）找真实ip

2）找c段，进攻其他主机，绕过云waf

没有，进入下一步

###### 常见入口目标

关注度低的系统

业务线较长的系统

###### 快照

有专门的站点快照提供快照功能，如 Wayback Machine 和 [Archive.org](https://archive.org/) 等。

###### Github

在Github中，可能会存在源码泄露、AccessKey泄露、密码、服务器配置泄露等情况，常见的搜索技巧有：

- `@example.com password/pass/pwd/secret/credentials/token`
- `@example.com username/user/key/login/ftp/`
- `@example.com config/ftp/smtp/pop`
- `@example.com security_credentials/connetionstring`
- `@example.com JDBC/ssh2_auth_password/send_keys`

https://blog.csdn.net/qq_36119192/article/details/99690742

#### 社会工程学

###### 企业信息收集

一些网站如天眼查等，可以提供企业关系挖掘、工商信息、商标专利、企业年报等信息查询，可以提供企业的较为细致的信息。

###### 社工库

人员信息收集，拿到管理员的某个信息丢到社工库查询

###### 钓鱼

[gophish](https://blog.csdn.net/qq_42939527/article/details/107485116)

基于之前收集到的信息，可以使用Office/CHM/RAR/EXE等文件制作钓鱼邮件发送至目标，进一步收集信息。

其中Office可以使用Office漏洞、宏、OLE对象、PPSX等方式构造利用文件。

Exe可以使用特殊的Unicode控制字符来构建容易混淆的文件名。

如果前期信息收集获取到了运维等人员的邮箱，可以使用运维人员的邮箱发送，如果未收集到相关的信息，可以使用伪造发送源的方式发送邮件。

###### 其他信息

公司的公众号、企业号、网站，员工的网盘、百度文库等可能会存在一些敏感信息，如VPN/堡垒机账号、TeamViewer账号、网络设备默认口令、服务器默认口令等

#### 源码获取

![](https://z3.ax1x.com/2021/09/23/4ahn6H.png)

数据库配置文件，后台目录，模版目录，数据库目录

ASP,PHP,ASPX,JSP,JAVAWEB 等脚本类型

#### 站点分类-搭建习惯

目录型站点

端口类站点

子域名站点

类似域名站点

旁注,C 段站点（同服务器，不同站点）（不同服务器，不同站点）

搭建软件特征站点

#### 信息泄露

bak			xxx.php.bak

源码泄露www.zip等				扫描器

svn泄露				linux下使用
