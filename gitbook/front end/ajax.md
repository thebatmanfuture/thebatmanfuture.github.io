[![OVtNfP.jpg](https://s1.ax1x.com/2022/05/04/OVtNfP.jpg)](https://imgtu.com/i/OVtNfP)

### 简介

~~~
通过前端向服务器发送http请求
AJAX 全称为 Asynchronous JavaScript And XML，就是异步的 JS 和 XML
无刷新获取数据
~~~

~~~
AJAX的优点
1) 可以无需刷新页面而与服务器端进行通信。 2) 允许你根据用户事件来更新部分页面内容。
AJAX的缺点
1) 没有浏览历史，不能回退 2) 存在跨域问题(同源) 3) SEO 不友好

当我们发现网页存在"hello world"，查看源代码却未发现该字符串，说明该字符串是通过ajax请求拿到的
~~~

### 使用

~~~js
核心对象：XMLHttpRequest，AJAX 的所有操作都是通过该对象进行的
使用步骤
	创建 XMLHttpRequest 对象
	var xhr = new XMLHttpRequest();
	设置请求信息
	xhr.open(method, url); //可以设置请求头，一般不设置
~~~

[![OVamYn.png](https://s1.ax1x.com/2022/05/04/OVamYn.png)](https://imgtu.com/i/OVamYn)

~~~
这里创建一个xhr的ajax对象之后，用get方式去访问url，send发送请求，通过判断服务器返回的状态值来呈现不同的状态
~~~

~~~js
console.log(xhr.status);//状态码
console.log(xhr.statusText);//状态字符串
console.log(xhr.getAllResponseHeaders());//所有响应头
console.log(xhr.response);//响应体

xhr.setRequestHeader('name','atguigu');
xhr.send('a=100&b=200&c=300');
//这里设置请求头，设置post参数
~~~

[![OVd0Cn.png](https://s1.ax1x.com/2022/05/04/OVd0Cn.png)](https://imgtu.com/i/OVd0Cn)

### **jQuery** **中的** **AJAX** 使用

#### get 请求

~~~
$.get(url, [data], [callback], [type])
	url:请求的 URL 地址
	data:请求携带的参数
	callback:载入成功时回调函数
	type:设置返回内容格式，xml, html, script, json, text, _default
~~~

#### post 请求

和上面一样

[![OVwex0.png](https://s1.ax1x.com/2022/05/04/OVwex0.png)](https://imgtu.com/i/OVwex0)











## javascript

~~~http
告警框
alert('xss')
提示框
prompt
确认框
confirm
~~~





~~~http
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        p{
            background-image: url(https://src.sjtu.edu.cn/media/mugshot/11000/b6ef1207-b198-4ee5-8004-ee8396601bc8.png);
            width: 500px;
            height: 500px;
        }
        .name123 {
            color : red;
        }
    </style>
    <link rel="stylesheet" href="index.css">
    <script type="text/javascript">
        
    </script>
</head>
<body>
    <header>
        <a href="https://www.baidu.com">123</a><br>
        <p id="100">
            这是闪电侠
        </p>
        <p class="name123">这是蝙蝠123侠</p>
        <script>
            var test = document.getElementById("100");
            alert(test.innerHTML);
            var test1 = document.getElementsByClassName("name123");
            alert(test1[0].innerHTML);
            var test2 = document.getElementsByTagName("a");
            alert(test2[0].innerHTML);
        </script>
    </header>
</body>
</html>
~~~



~~~
JS修改html，css
js触发事件
~~~



~~~http
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        p{
            background-image: url(https://src.sjtu.edu.cn/media/mugshot/11000/b6ef1207-b198-4ee5-8004-ee8396601bc8.png);
            width: 500px;
            height: 500px;
        }
        .name123 {
            color : red;
        }
    </style>
    <link rel="stylesheet" href="index.css">
    <script type="text/javascript">
        
    </script>
</head>
<body>
    <button onclick="test()">点击我触发XSS</button>
    <script>
        function test(){
            alert('/xss/');
        }
    </script>
</body>
</html>
~~~





~~~http
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" href="index.css">
    <script type="text/javascript">
        
    </script>
</head>
<body>
    <p id="p1">看到我</p>
    <br>
    <button onclick="change()">惦记我修改界面</button>
    <script>
        function change(){
            document.getElementById("p1").innerHTML = "当你点击了之后成功修改了html页面";
        }
    </script>
</body>
</html>
~~~





~~~http
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" href="index.css">
    <script type="text/javascript">
        
    </script>
</head>
<body>
    
    <script>
        var p1 = window.innerHeight;
        var p2 = window.innerWidth;
        document.write("实际的高度是" + p1 + "宽度是" + p2);
    </script>
</body>
</html>
~~~







