## 密文加密

#### hash-（md5，sha-1等），base64等不需要提供其他信息的

可以直接在burp中设置

#### AES加密

因为aes加密需要提供的内容很多，所以我们我们需要借助插件帮忙

##### jsEncrypter

当对方对密文进行aes加密时，那么我们就可以利用jsEncrypter进行密文猜解

参考文章：https://www.jianshu.com/p/29b6827b9853

科普下aes加密：

[![bXypUs.png](https://s1.ax1x.com/2022/03/14/bXypUs.png)](https://imgtu.com/i/bXypUs)

一般情况下需要这五个：加密模式，填充，数据块，密码，偏移量

但是加密模式，填充，数据块一般都是默认的CBC，Pkcs7，128位

我们需要查看前端代码找到密码和偏移量，我们用fofa搜索

~~~
body="aes.min.js" && title="后台"
~~~

[![bXy0RP.png](https://s1.ax1x.com/2022/03/14/bXy0RP.png)](https://imgtu.com/i/bXy0RP)

就它了，http://123.56.141.8:8088/login.htm

查看源代码，发现了好东西

[![bX6wTJ.png](https://s1.ax1x.com/2022/03/14/bX6wTJ.png)](https://imgtu.com/i/bX6wTJ)

这里我们搜索aes发现红圈那里调用了一个函数，这就奇怪了，这个函数体代码在哪里捏？js被引用的文件我们可见的就那么几个，如果js里又引用js，不排除这种可能，那么我们可以用jsfinder或者dirsearch爆破找寻更多的js文件。这里他的函数就在这个tools.js里面

[![bX6NOU.png](https://s1.ax1x.com/2022/03/14/bX6NOU.png)](https://imgtu.com/i/bX6NOU)

[![bXcCcV.png](https://s1.ax1x.com/2022/03/14/bXcCcV.png)](https://imgtu.com/i/bXcCcV)

从上往下依次是密码，偏移量，加密模式，填充，我们到aes加密网站看一看

~~~
密码：SinoSoftadmin123
偏移量：Sino1@3adminSoft
加密模式：CBC
填充：Pkcs7
~~~

http://tool.chacuo.net/cryptaes

我们登录该网站抓包，http://123.56.141.8:8088/login.htm

账号;admin，密码：123456

[![bXcN9I.png](https://s1.ax1x.com/2022/03/14/bXcN9I.png)](https://imgtu.com/i/bXcN9I)

这里对password进行两次url得到了`25EUjoJwXeFVnrJ0TI9Q+w==`

这里在aes解密网站解码查看，得到明文

[![bXc5bF.png](https://s1.ax1x.com/2022/03/14/bXc5bF.png)](https://imgtu.com/i/bXc5bF)



安装burp扩展jsEncrypter.0.3.2

> 链接：https://pan.baidu.com/s/16Rmm4x4C42qFU1pM-3ywvw 
> 提取码：gw8e 

下载依赖phantomjs-2.1.1

> 链接：https://pan.baidu.com/s/12iaD1YbK6-RHlg6OGW1PIw 
> 提取码：kr5h 

这里我们下载下来后目录结构是这样放的

[![bXg95d.png](https://s1.ax1x.com/2022/03/14/bXg95d.png)](https://imgtu.com/i/bXg95d)

在这个phantomjs-2.1.1文件夹的bin目录下是这样的



没错，这里需要把那两个文章复制过来，打开aes.min.js文件，在里面复制aes加密代码

http://123.56.141.8:8088/js/encry/aes.min.js

在phantomjs_server.js文件里复制过来，添加上你的js代码如图

http://123.56.141.8:8088/js/tools.js

[![bXgrM6.png](https://s1.ax1x.com/2022/03/14/bXgrM6.png)](https://imgtu.com/i/bXgrM6)

这个蓝色的内容复制进来

[![bXgcZD.png](https://s1.ax1x.com/2022/03/14/bXgcZD.png)](https://imgtu.com/i/bXgcZD)

这里要注意两点：

第一点就是格式必须正确，不能有错误的空格，错误的换行

第二点就是那里的那个js_encrypt(payload)必须和CryptoJS.enc.Utf8.parse(payload)的payload填写一样，不一样你就自己改成一样的

然后我们burpsuite导入该插件

[![bX2pLT.png](https://s1.ax1x.com/2022/03/14/bX2pLT.png)](https://imgtu.com/i/bX2pLT)

我们在bin目录下敲入 `phantomjs.exe phantomjs_server.js`

[![bX2SyV.png](https://s1.ax1x.com/2022/03/14/bX2SyV.png)](https://imgtu.com/i/bX2SyV)

在插件这里点击connect连接，如果连接成功，我们点击test，如果成功了就会有下图显示

[![bX2PwF.png](https://s1.ax1x.com/2022/03/14/bX2PwF.png)](https://imgtu.com/i/bX2PwF)

这样我们只要把我们需要爆破的密码复制到左边再点一下test就会自动加密好，我们把这些密文作为字典去爆破

像这个示例里面因为有两次url编码，所以我们在替换后记得再进行两次编码





















































