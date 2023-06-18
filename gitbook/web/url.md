#### 重定向漏洞

以下为常见的payload

当URL参数（payload）在服务器端没有得到正确的验证，导致⽤户被重定向到⼀个任意⽹站时，⽹站就

会受到Open Redirect的攻击。

虽然这对⽤户没有任何重⼤的威胁，但这个漏洞让⽹络钓⻥变得更加容易

~~~
/{payload}
?next={payload}
?url={payload}
?target={payload}
?rurl={payload}
?dest={payload}
?destination={payload}
?redir={payload}
?redirect_uri={payload}
?redirect_url={payload}
?redirect={payload}
/redirect/{payload}
/cgi-bin/redirect.cgi?{payload}
/out/{payload}
/out?{payload}
?view={payload}
/login?to={payload}
?image_url={payload}
?go={payload}
?return={payload}
?returnTo={payload}
?return_to={payload}
?checkout_url={payload}
?continue={payload}
?return_path={payload}
~~~

