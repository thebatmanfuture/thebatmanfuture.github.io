#### JSONP

###### 复习

假如我们请求了黑客的网站www.evil.com后，evil后调用www.normal.com的内容，正常情况下因为同源策略是允许访问的，但是很多网站都可以调用css，js，这是因为利用了其他方法：

这里有两种，第一种是response header，第二种是jsonp

第一种是在接口的服务端修改响应头

res.header("Access-Control-Allow-Origin","*")   //这里 * 是任意的域都可以进行访问，通常见于一些前端框架中对一些css，js的调用

第二种就是如下



`<img>`的src（获取图片），`<link>`的href（获取css），`<script>`的src（获取javascript）这三个都不符合同源策略，它们可以跨域获取数据。这里JSONP就是利用`<script>`的src来实现跨域获取数据的。

JSONP 是 JSON with padding（填充式 JSON 或参数式 JSON）的简写。

JSONP实现跨域请求的原理简单的说，就是动态创建`<script>`标签，然后利用`<script>`的src 不受同源策略约束来跨域获取数据。

JSONP 由两部分组成：回调函数和数据。回调函数是当响应到来时应该在页面中调用的函数。回调函数的名字一般是在请求中指定的。而数据就是传入回调函数中的 JSON 数据。

动态创建`<script>`标签，设置其src，回调函数在src中设置：

```html
var script = document.createElement("script");
script.src = "https://api.douban.com/v2/book/search?q=javascript&count=1&callback=handleResponse";
document.body.insertBefore(script, document.body.firstChild);
```

在页面中，返回的JSON作为参数传入回调函数中，我们通过回调函数来来操作数据。

```html
function handleResponse(response){
    // 对response数据进行操作代码
}
```

了解了JSONP的基本使用方法，我们在实现上面通过ajax调用豆瓣接口的需求，实现代码如下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>JSONP实现跨域2</title>
</head>
<body>
    <div id="mydiv">
        <button id="btn">点击</button>
    </div>
</body>
<script type="text/javascript">
    function handleResponse(response){
            console.log(response);
    }
</script>
<script type="text/javascript">
    window.onload = function() {

    var oBtn = document.getElementById('btn');

    oBtn.onclick = function() {     

        var script = document.createElement("script");
        script.src = "https://api.douban.com/v2/book/search?q=javascript&count=1&callback=handleResponse";
        document.body.insertBefore(script, document.body.firstChild);   
    };
};
</script>
</html>
```

参考链接：

https://www.bilibili.com/video/BV1Kt411E76z?from=search&seid=14170614582243114223&spm_id_from=333.337.0.0

https://blog.csdn.net/weixin_45116657/article/details/102972034

https://www.cnblogs.com/BOHB-yunying/p/12899979.html



