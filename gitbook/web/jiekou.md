## 接口安全

WSDL（网络服务描述语言，Web Services Description Language）是一门基于 XML 的语言，用于描述Web Services 以及如何对它们进行访问。

~~~http
配合 shodan，fofa,zoomeye 搜索也不错哦~
inurl:jws?wsdl
inurl:asmx?wsdl
inurl:aspx?wsdl
inurl:ascx?wsdl
inurl:ashx?wsdl
inurl:dll?wsdl
inurl:exe?wsdl
inurl:php?wsdl
inurl:pl?wsdl
inurl:?wsdl
filetype:wsdl wsdl
~~~



依据表现形式的不同将接口分为了四类：

​	第一类，类库型API，依赖于编程语言，如微软基础类库。

​	第二类，操作系统型API，主要为使用者与计算机进行交互使用，有Windows API、Linux API。

​	第三类，远程应用型API，这类API不关心编程语言、平台等，便于应用及远程服务器进行交互，如JDBC驱动的数据库连接。

​	第四类，Web应用型API，通常使用HTTP协议，以一组服务的形式对外表现，如网站提供的登录接口。

接口渗透测试中的“接口”指的是第四类，其中又根据技术形式的不同，分为了RESTful API、SOAP API、GraphQL API、gRPC API、类XML-RPC及其他类型的API（此处并无统一分类方法，也无统一的名称，意会即可）。





#### 1、框架、库、软件、中间件

框架有：Springboot、Shiro、Struts2、Spring mvc……

库：Fastjson、Jackson

软件：Apache Log4j2

中间件：Tomcat、Weblogic、Apache、IIS

#### 2、输入未过滤、输出未验证导致的注入

测试过常见的exp后，开始对输入的参数进行注入测试，如SQL注入、XXE、XSS(较少)等，对于接口的测试，省事之处就在于无须再想方设法绕过前端的过滤，直接传参即可。

[![bbqfPS.png](https://s1.ax1x.com/2022/03/13/bbqfPS.png)](https://imgtu.com/i/bbqfPS)

#### 3、权限的绕过

可以从数据的安全性考虑，测试是否存在因未授权访问、越权操作、越权查看而导致的数据泄露。

##### 直接访问-构造高危接口

可通过直接访问的进行利用的漏洞的准确发现依赖于对系统的把握程度，如系统所使用框架、软件和中间件的默认、高危文件名称，如springboot的各种未授权访问。

另外，在测试过程中尝试理解文件夹命名的构造规律，如xx大学缩写是abc，而利用测试账号登录后查看个人信息的页面是abcUser.php，那可以尝试下abcAdmin、abcManager等尝试访问管理员信息页面，如果有重置密码reset_pass的接口，那么可以尝试下update_user，说不定可以找到修改用户密码的接口。如下例，在测试中，测试人员在忘记密码处的findAccount接口得到了可能标识身份的profile字段的值：

[![bbLZxH.png](https://s1.ax1x.com/2022/03/13/bbLZxH.png)](https://imgtu.com/i/bbLZxH)

后将接口findAccount.htm改为reset.htm，参数不变，访问该接口，发现可任意重置密码：

[![bbLMZt.png](https://s1.ax1x.com/2022/03/13/bbLMZt.png)](https://imgtu.com/i/bbLMZt)

##### 接口文档中的高危接口

测试人员在信息收集时得到了内部API接口说明文档，其中存在提供给管理员模拟用户登录的接口，可指定用户mid后返回经过授权的url：

[![bbLWe1.png](https://s1.ax1x.com/2022/03/13/bbLWe1.png)](https://imgtu.com/i/bbLWe1)

测试人员尝试随意构造mid后，访问返回的url直接登录了系统：

[![bbLho6.png](https://s1.ax1x.com/2022/03/13/bbLho6.png)](https://imgtu.com/i/bbLho6)

##### 参数篡改

###### 修改标识身份的参数

如果标识身份的参数是明文传输，例如是id、username等，那么可以通过遍历该参数获取平行账户的信息，例如下图中接口的usrno参数，判断为姓名的组合，可构造字典对该参数进行遍历，获取员工姓名、证件号码、联系电话等敏感信息。

[![bbLHQH.png](https://s1.ax1x.com/2022/03/13/bbLHQH.png)](https://imgtu.com/i/bbLHQH)

###### 修改标识资源的参数

在以卡号信息为索引查看个人信息的位置，如权限控制失效，则可遍历卡号越权查看他人身份信息，如下：

[![bbLXwt.png](https://s1.ax1x.com/2022/03/13/bbLXwt.png)](https://imgtu.com/i/bbLXwt)

###### 加密不严格的token

要注意，某些加密不严格的token，例如只是对用户名进行了base64、md5处理，这种白送的漏洞，一定不能错过。

###### 篡改值的属性

在某些场景下，授权机制依赖于某个ID值，如果将ID值篡改为对象，比如数组对象、JSON对象，则发生授权绕过。类似的攻击手法有：

数组绕过：{“id”:111} --> {“id”:[111]}

JSON 对象绕过：{“id”:111} --> {“id”:{“id”:111}}

两次传值绕过：/api?id=非法参数值&id=恶意参数值

正则匹配绕过：{"user_id":"*"}

……………………..

### 4、接口的滥用

指那些因未限制请求频率导致的爆破与遍历，如登录接口的爆破、手机号码的遍历，身份证号码的遍历、短信验证码爆破和短信、邮件的轰炸。

### 5、上传、下载

### 6、RESTful API

大部分的RESTful应用都采用token的认证方式，可以在token处多下功夫

另外，因其无状态的原则，HTTP header中的标志符往往比url中的标志符更容易发现安全问题，例如RESTful API大多数情况下会使用json格式进行传输，但并非全部，渗透测试时，试着将content-type修改为 "application/xml"，添加一段xml后发出请求，验证其是否支持SOAP协议，从而发现是否存在安全漏洞。

















