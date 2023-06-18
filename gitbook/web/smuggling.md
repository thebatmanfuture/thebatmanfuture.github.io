#### http请求走私

在http1.1中，规定了Keep-Alive和Pipeline。

其中前者keep-alive，不关闭tcp链接，只需要进行一次TCP握手的过程

后者Pipeline，可以像流水线一样发送自己的HTTP请求，不需要等待服务器的响应，服务器那边接收到请求后，需要遵循先入先出机制，将请求和响应严格对应起来，再将响应发送给客户，浏览器默认是不启用`Pipeline`的，但是一般的服务器都提供了对`Pipleline`的支持

[![I1swqJ.png](https://z3.ax1x.com/2021/11/07/I1swqJ.png)](https://imgtu.com/i/I1swqJ)

##### CL不为0的GET请求

假设前端代理服务器允许GET请求携带请求体，而后端服务器不允许GET请求携带请求体，它会直接忽略掉GET请求中的`Content-Length`头，不进行处理。这就有可能导致请求走私。

```http
GET / HTTP/1.1\r\n
Host: example.com\r\n
Content-Length: 44\r\n
 
GET / secret HTTP/1.1\r\n
Host: example.com\r\n
\r\n
```

前端服务器收到该请求，通过读取`Content-Length`，判断这是一个完整的请求，然后转发给后端服务器，而后端服务器收到后，因为它不对`Content-Length`进行处理，由于`Pipeline`的存在，它就认为这是收到了两个请求，分别是

```http
第一个
GET / HTTP/1.1\r\n
Host: example.com\r\n
 
第二个
GET / secret HTTP/1.1\r\n
Host: example.com\r\n
```

##### CL-CL

假设中间的代理服务器和后端的源站服务器在收到类似的请求时，都不会返回400错误，但是中间代理服务器按照第一个`Content-Length`的值对请求进行处理，而后端源站服务器按照第二个`Content-Length`的值进行处理。

```http
POST / HTTP/1.1\r\n
Host: example.com\r\n
Content-Length: 8\r\n
Content-Length: 7\r\n
 
12345\r\n
a
```

从前面我们也知道了，代理服务器与源站服务器之间一般会重用TCP连接。

这时候正常用户的请求就拼接到了字母a的后面，当后端服务器接收完毕后，它实际处理的请求其实是

```http
aGET /index.html HTTP/1.1\r\n
Host: example.com\r\n
```

这时候用户就会收到一个类似于aGET request method not found的报错。这样就实现了一次HTTP走私攻击，而且还对正常用户的行为造成了影响，而且后续可以扩展成类似于CSRF的攻击方式。

但是两个Content-Length这种请求包还是太过于理想化了，一般的服务器都不会接受这种存在两个请求头的请求包。但是在RFC2616的第4.4节中，规定:如果收到同时存在Content-Length和Transfer-Encoding这两个请求头的请求包时，在处理的时候必须忽略Content-Length，这其实也就意味着请求包中同时包含这两个请求头并不算违规，服务器也不需要返回400错误。服务器在这里的实现更容易出问题。

.......................























参考文章：

https://blog.csdn.net/weixin_44058342/article/details/102503140



















