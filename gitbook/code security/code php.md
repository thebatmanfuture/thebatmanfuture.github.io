## 代码审计

### 基础

环境搭建：

![1e5fff983d5a250f9ed0718b95debf75.png](https://s1.imagehub.cc/images/2023/01/25/1e5fff983d5a250f9ed0718b95debf75.png)

#### SQL注入

##### 1）基础SQL注入demo代码

###### 数字型SQL注入

~~~php
<?php

# 创建连接
$conn = mysqli_connect("localhost", "root", "root", "php10");

# 查看是否连接成功
if ($conn) {
    echo "服务器连接成功!\n";
} else {
    echo mysqli_connect_error();
}
$id = $_GET['id'];

# SQL语句, 条件查询
$SQL = "SELECT * FROM msg WHERE id = $id ORDER BY id ";

# 执行SQL语句
$result = mysqli_query($conn, $SQL);

# 查看是否执行成功
if ($result) {
    echo "SQL语句执行成功!\n";
} else {
    echo mysqli_error($conn);
}

# 调试输出
$line =  mysqli_fetch_assoc($result);
echo "<br />";
echo $line['id'];
echo "<br />";
echo $line['content'];
echo "<br />";
echo $line['user'];
echo "<br />";
echo $line['intime'];
echo "<br />";



# 关闭连接
mysqli_close($conn);

?>
~~~

~~~http
http://localhost/123.php?id=1/**/and/**/extractvalue(1,concat(char(126),database()))
~~~

###### 防御

使用预处理语句和绑定参数的方式来执行SQL查询

~~~php
$stmt = mysqli_prepare($conn, "SELECT * FROM msg WHERE id = ? ORDER BY id ");
mysqli_stmt_bind_param($stmt, "s", id);
mysqli_stmt_execute($stmt);
$result = mysqli_stmt_get_result($stmt);
~~~

~~~php
<?php
error_reporting(E_ERROR);
# 创建连接
$conn = mysqli_connect("localhost", "root", "root", "php10");

# 查看是否连接成功
if ($conn) {
    echo "服务器连接成功!\n";
} else {
    echo mysqli_connect_error();
}
$id = $_GET['id'];

$stmt = mysqli_prepare($conn, "SELECT * FROM msg WHERE id = ? ORDER BY id ");
mysqli_stmt_bind_param($stmt, "s", $id);
mysqli_stmt_execute($stmt);
$result = mysqli_stmt_get_result($stmt);



echo "<br />";
echo $SQL;
echo "<br />";


# 查看是否执行成功
if ($result) {
    echo "SQL语句执行成功!\n";
} else {
    echo mysqli_error($conn);
}



# 调试输出
$line =  mysqli_fetch_assoc($result);
echo "<br />";
echo $line['id'];
echo "<br />";
echo $line['content'];
echo "<br />";
echo $line['user'];
echo "<br />";
echo $line['intime'];
echo "<br />";

# 关闭连接
mysqli_close($conn);

?>
~~~

~~~http

这里直接对id变量进行了预处理和参数绑定
~~~







#### 存储XSS

~~~
~~~

























#### 文件上传

~~~php
<?php
move_uploaded_file($_FILES["file"]["tmp_ name"]，＄_FILES ["file"]["name"])；
?>
// move_uploaded_file 函数直接把上传的临时文件 copy 到了新文件。
~~~

~~~php
$savefile = preg_replace ("/(php|phtml|php3|php4|jsp|exe|dll|asp|cer|asa|shtml|shtm|aspx|asax|cgi|fcgi|pl)(\.1$)/i"，"\\1\\2"， $savefile)；
~~~

只要是能上传的地方，就都可能存在漏洞。常见的就两种，一个是格式没有限制，或者过滤不完善，导致各种任意文件上传。第二个就是文件名没有进行sql注入检测，很多程序员会忘记这一点，导致sql注入。因为文件名也是要存到数据库中的。

#### 目录遍历/文件读取

如果程序员在开发中，直接将文件名或者文件路径在参数中传递，那么就可能导致攻击者利用../或者..\来进行跳转目录达到任意文件操作，比如任意文件读取。

另外，直接用文件名或者文件路径在参数中传递，还可能导致xss，因为，程序可能需要在页面中输出文件名字，这个时候，若文件名字也没有过滤，那么就会存在xss攻击。

#### 登录认证

认证又是基于cookie和session，程序又经常会把用户账号等认证信息放到cookie中，来保持用户可以长时间登录，如果这段cookie没有进行加密，就会导致任意用户登录的漏洞

#### SQL注入

##### 普通注入

关键字，比如 select from,mysql_connect，mysql_query，mysql_fetch_row 等，数据库的查询方式还有 update，insert，delete 只需要查找这些关键字

##### 宽字节注入

使用addslashes()、mysql_real_escape_string()、mysql_escape_string函数或者开启GPC的方式来防止注入

但是如果某处使用了urldecode或者rawurldecode函数，如果开启了GPC来过滤，那么提交`?id=1%25%27,%25`经过url解码为%，%27解码为单引号，那么成功引发注入







































