### Flask

#### Flask session伪造

题目：[BUUCTF](https://buuoj.cn/challenges#[HCTF%202018]admin)

考点：flask中session是存储在客户端cookie中的，也就是存储在本地。flask仅仅对数据进行了签名。众所周知的是，签名的作用是防篡改，而无法防止被读取。而flask并没有提供加密操作，所以其session的全部内容都是可以在客户端读取的，这就可能造成一些安全问题

我们在登录成功后查看cookie，复制其中的session字符串值，在终端运行

![](https://s4.ax1x.com/2022/02/21/HxM3PU.png)

###### session解密脚本

~~~python
#!/usr/bin/env python3
import sys
import zlib
from base64 import b64decode
from flask.sessions import session_json_serializer
from itsdangerous import base64_decode

def decryption(payload):
    payload, sig = payload.rsplit(b'.', 1)
    payload, timestamp = payload.rsplit(b'.', 1)

    decompress = False
    if payload.startswith(b'.'):
        payload = payload[1:]
        decompress = True

    try:
        payload = base64_decode(payload)
    except Exception as e:
        raise Exception('Could not base64 decode the payload because of '
                         'an exception')

    if decompress:
        try:
            payload = zlib.decompress(payload)
        except Exception as e:
            raise Exception('Could not zlib decompress the payload before '
                             'decoding the payload')

    return session_json_serializer.loads(payload)

if __name__ == '__main__':
    print(decryption(sys.argv[1].encode()))
~~~



解密之后我们把name的值改为其他用户的名，比如admin，再使用加密脚本加密，但是正常情况下我们还需要知道SECRET_KEY，如果我们有源码可以在源码中查找，否则就是弱密钥猜解，比如123456，admin这种

###### [session加密脚本](https://github.com/noraj/flask-session-cookie-manager/archive/refs/heads/master.zip)

最后使用加密脚本对内容加密即可即可伪造他人身份。



### Tornado

#### 格式化输出窃取cookie_secret

题目：[BUUCTF](https://buuoj.cn/challenges#[%E6%8A%A4%E7%BD%91%E6%9D%AF%202018]easy_tornado)-这题我b站有讲过https://www.bilibili.com/video/BV1PF411n7kM?p=21

考点：当我们通过render渲染函数能够对传入的格式化字符输出，那么就可以窃取环境变量，其中泄露了cookie_secret，该cookie_secret是用来对登陆成功的用户的cookie值进行加密，如果我们得到cookie_secret，那么可以实现cookie欺骗，伪造他人身份身份

源码如下

~~~python
import tornado.web
import tornado.ioloop
from tornado.template import Template

# windows 系统下 tornado 使用SelectorEventLoop
import platform
import asyncio

print(platform.system())
asyncio.set_event_loop_policy(asyncio.WindowsSelectorEventLoopPolicy())

class IndexHandler(tornado.web.RequestHandler):
    def get(self,*args,**kwargs):
        msg = self.get_argument('msg')
        with open("error.html", 'w') as f:
            f.write("""<html>
                <head>
                <style>body{font-size: 30px;}</style>
                </head>
                <body>%s</body>
                </html>\n""" % msg)
            f.flush()
        self.render("error.html")
        # self.write(self.application.settings)


class SetCookieHandler(tornado.web.RequestHandler):
    def get(self, *args, **kwargs):
        self.set_secure_cookie('admin','123456',expires_days=3)

settings = {
    'cookie_secret':'batmanfuture'
}

app = tornado.web.Application([
    (r'/',IndexHandler),
    (r'/',SetCookieHandler)
],**settings)

app.listen(8888)

tornado.ioloop.IOLoop.instance().start()
~~~



这里msg={{handler.settings}}时，就会显示环境变量，其中就包括cookie_secret。

![](https://s4.ax1x.com/2022/02/21/HxluNV.png)











### Django 

#### Django debug page XSS漏洞

CVE-2017-12794

题目：[BUUCTF](https://buuoj.cn/challenges#[Django]CVE-2017-12794)

考点：django框架利用创建好的用户名未对XSS代码就行过滤导致攻击

##### 漏洞复现

访问`http://node3.buuoj.cn:28199/create_user/?username=<script>alert(1)</script>`创建用户，测试弹窗

[![bdwGH1.png](https://s4.ax1x.com/2022/03/05/bdwGH1.png)](https://imgtu.com/i/bdwGH1)

这里提示以已经创建好了用户，即username = <script>alert(1)</script>

触发漏洞，再次访问`http://node3.buuoj.cn:28199/create_user/?username=<script>alert(1)</script>`

因为已经创建过该用户，所以django不加以过滤的方式输出username导致XSS

[![bdwDud.png](https://s4.ax1x.com/2022/03/05/bdwDud.png)](https://imgtu.com/i/bdwDud)[![bdwg4f.png](https://s4.ax1x.com/2022/03/05/bdwg4f.png)](https://imgtu.com/i/bdwg4f)

##### POC编写

项目地址：https://hub.fastgit.xyz/thebatmanfuture/-Django-CVE-2017-1279475

































