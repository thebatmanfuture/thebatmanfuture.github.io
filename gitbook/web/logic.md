## 逻辑漏洞

#### 越权

chui



![4cmK4P.png](https://z3.ax1x.com/2021/09/26/4cmK4P.png)

#### 登录框攻击思路

##### 用户名枚举

[![HVrfxg.png](https://s4.ax1x.com/2022/02/03/HVrfxg.png)](https://imgtu.com/i/HVrfxg)[![HVrcIP.png](https://s4.ax1x.com/2022/02/03/HVrcIP.png)](https://imgtu.com/i/HVrcIP)

##### 弱口令

##### 空口令

##### 登录认证绕过

修改返回包的static或者code等值即可登录成功，首先，使用正常账号修改密码，获取验证码通过时服务器返回数据，保存该信息；其次，错误登录抓包，服务器会返回验证码错误之类的信息，使用正确的返回包此信息进行替换

[![bjOJzV.png](https://s1.ax1x.com/2022/03/15/bjOJzV.png)](https://imgtu.com/i/bjOJzV)

##### 暴力破解

##### 图形验证码不失效、不切换

比如某些地方存在图形验证码为了防止爆破，使用过的验证码应该立即失效

否则A用户的验证码可以给B用户用

##### 验证码多用

一般来说短信验证码仅能使用一次，验证码和手机号未绑定，验证码一段时期内有效，那么就可能出现如下情况：

　　1、A手机的验证码，B可以拿来用

　　2、A手机在一定时间间隔内接到两个验证码，都可以用。

##### 短信验证码复用

第一次登录时使用的验证码，在注销退出后还是可以用来登录

##### 验证码爆破（短信验证码和邮箱验证码）

次数可控，4，5，6位，或者对方未对输入次数进行限制

##### 无效的验证码

有验证码模块，但验证模块与业务功能没有关联性，此为无效验证，一般在新上线的系统中比较常见。获取短信验证码后，随意输入验证码，直接输入两次密码，可成功更改用户密码，没有对短信验证码进行验证，可能导致CSRF等问题。

[![bjqXjS.png](https://s1.ax1x.com/2022/03/15/bjqXjS.png)](https://imgtu.com/i/bjqXjS)

##### 短信/邮箱轰炸

这个洞我挖edusrc挖到了四五次

绕过tips：我们还可以抓包尝试在请求包中将手机号修改，比如`15143375393`，我们可以变成`+8615143375393`

或者在前后面加上空格，点，冒号，\n等绕过`15143375393.  `

如果代码层的逻辑是这样子：
　　1、验证手机号是否已发送给验证码
　　2、去除用户输入的空格和其他特殊字符
　　3、重新发送验证码

 那么，可利用"\n"和空格可绕过，持续递增空格就可造成无限短信轰炸。

思路：有空就在手机号号码后面加一位进行FUZZ，可能会发现不一样的惊喜。

[![bjXClV.png](https://s1.ax1x.com/2022/03/15/bjXClV.png)](https://imgtu.com/i/bjXClV)

##### 短信验证码双发

短信验证码双发，很多情况，可以直接重置用户的密码，导致登陆成功。
一般存在如下几个形式，比如“phone=18622322323,234242&code=2342”可以使用“，”“；”“.”等符号分开。
或者直接如下案例：“phone=18622222222&code=1234&phone=13555555555”

[![bXmpt0.png](https://s1.ax1x.com/2022/03/14/bXmpt0.png)](https://imgtu.com/i/bXmpt0)

##### 恶意锁定

攻击者可以编写脚本批量锁定系统账号

##### 密码明文传输

##### 反射型XSS

比如登录处我们输入xss语句，登陆失败，对方会显示该用户名，那么可以实现反射型xss

###### 万能密码和sql注入

也就是sql注入

###### 任意用户密码修改/重置

越权

###### 目录遍历

可以查看源代码，选择js或者css文件，访问他们的文件夹

###### 敏感信息泄露

泄露口令，密钥，证书，会话标识，license，隐私数据，备份文件，配置文件，日志文件

###### 框架漏洞

比如s2，shiro，log4j2

###### sso认证缺陷

认证存在缺陷，可以越权到其他账号

###### ajax缓存

在页面源代码发现ajax缓存后，某些时候可以绕过登录框的次数限制，如图

[![HVyl7R.png](https://s4.ax1x.com/2022/02/03/HVyl7R.png)](https://imgtu.com/i/HVyl7R)

[![HVyY9K.png](https://s4.ax1x.com/2022/02/03/HVyY9K.png)](https://imgtu.com/i/HVyY9K)

###### JS文件

在页面源代码，会调用其他的js文件，我们可以通过（1）爆破（2）JSFinder来找其他js文件，可能发现不一样的内容

`JS`文件，一般 `JS`文件通常会`报出 CMS版本` ，和一些`验证判断响应值`

###### 禁用JS

有时在禁用js后会有

###### 扫描（目录扫描和前面提到的js扫描，端口扫描）

###### 任意用户注册（手机号）

　第一步，利用自己的手机号接收验证码进行验证，下一步跳转到一个设定密码的页面

　第二步，抓包，篡改手机号，使用任意手机号进行注册

###### 任意用户注册/任意密码重置（邮箱）

我们在注册账号时，输入账号，密码，邮箱，点击确定后会发送一封邮件到我们的邮箱，查看url：

http://x.xx.com/x.aspx?userName=aaa&Tomail=bbb&passWord=ccc

这里username，tomail，password就是我们对应的账号，邮箱和密码，点击链接完成注册

这样我们就得到了一个任意用户注册漏洞，密码找回也是一样，就是任意密码重置漏洞





###### 不完全的登录

###### url重定向

###### 验证码修改接收者

###### 万能验证码绕过

###### 验证码回显前端

　客户端验证是不安全的，可能导致任意账号注册、登录及重置任意用户密码等一系列问题

案例一：直接返回明文验证码

　　点击获取收集验证码，监听到两条json数据，可以发现验证码就藏在ticket里面，输入9360即可登陆成功。

[![bjLK41.png](https://s1.ax1x.com/2022/03/15/bjLK41.png)](https://imgtu.com/i/bjLK41)

案例二：返回密文验证码（这个up在挖黄淮学院时遇见过）

　　验证加密后返回客户端，用户解密即可获取验证码。

[![bjLbVJ.png](https://s1.ax1x.com/2022/03/15/bjLbVJ.png)](https://imgtu.com/i/bjLbVJ)

[![bjLxxK.png](https://s1.ax1x.com/2022/03/15/bjLxxK.png)](https://imgtu.com/i/bjLxxK)





###### 验证码不刷新

###### 验证码识别

###### cookie脆弱性/伪造

cookie内容存在某种规律，可以实现伪造其他用户身份

###### cookie混淆

可以在浏览器当前页面同时登录多个用户但是是共用一个cookie，修改信息时，可以将另一个账号信息修改

漏洞地址：http://yun.1905.com/index.php?m=Home&c=User&a=findPassword
1、输入我们自己的账户

[![bvk1ns.png](https://s1.ax1x.com/2022/03/15/bvk1ns.png)](https://imgtu.com/i/bvk1ns)

2、下发短信验证码

[![bvkBu9.png](https://s1.ax1x.com/2022/03/15/bvkBu9.png)](https://imgtu.com/i/bvkBu9)

3、输入收到的验证码

[![bvkDBR.png](https://s1.ax1x.com/2022/03/15/bvkDBR.png)](https://imgtu.com/i/bvkDBR)

4、来到设置新密码界面，保留该页面

[![bvkrH1.png](https://s1.ax1x.com/2022/03/15/bvkrH1.png)](https://imgtu.com/i/bvkrH1)

5、在浏览器新标签中输入目标账户，提交，回到4中保留的页面，提交

[![bvkyAx.png](https://s1.ax1x.com/2022/03/15/bvkyAx.png)](https://imgtu.com/i/bvkyAx)

6、密码重置成功

[![bvkwjJ.png](https://s1.ax1x.com/2022/03/15/bvkwjJ.png)](https://imgtu.com/i/bvkwjJ)

###### 图片验证码字段可以删除

图片验证码字段可以删除，或者在参数里面存在验证码设置，类似：
Username=admin&password=123456&code=nei9&set=true;
可以设置set=false，删除图片验证码值，发现可以继续登录，导致可以对登录进行爆破。



###### session会话固定

比如a用户登陆后的sessionid是123456，在退出之后再次登录时，sessionid还是123456，理论上来说应该销毁该session，重新构造一个新的sessionid；否则有可能被攻击者窃取，导致攻击者伪造身份

###### session会话注销

在a用户退出登录后，虽然session被改成新的值，但是原session可能并未被失效，仍可以使用，达到窃取用户身份登录

###### session会话超时

一般来讲，当用户的登录后，如果10分钟或者更长不执行操作，那么该session应该配置生命周期

###### 弱token缺陷-见pikachu靶场题目

###### 验证码识别

###### 验证绕过

在验证时，提前获取了下一步的请求数据包，抓包修改，尝试不输入验证码跳转到下一个页面



##### 任意用户注册

https://www.cnblogs.com/X-caiji/p/11209963.html

https://www.secpulse.com/archives/67080.html

https://blog.csdn.net/weixin_39997829/article/details/79788969
