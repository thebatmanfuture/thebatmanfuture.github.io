### PHP语法

PHP 脚本有四种方式开头：

~~~php
<?php ?>
<? ?>
<%php %>
<?= ?>	相当于	<?php echo?>
~~~

PHP是一种弱类型语言，会根据变量的值，自动把变量转换为正确的数据类型，有四种不同的变量作用域，

- local		局部作用域
- global             全局作用域
- static
- parameter

当一个函数完成时，它的所有变量通常都会被删除。然而，有时候您希望某个局部变量不要被删除，使用 **static** 关键字

[![HiT6XT.png](https://s4.ax1x.com/2022/02/01/HiT6XT.png)](https://imgtu.com/i/HiT6XT)

参数作用域，参数是通过调用代码将值传递给函数的局部变量

[![HiTqBD.png](https://s4.ax1x.com/2022/02/01/HiTqBD.png)](https://imgtu.com/i/HiTqBD)

记住有echo，print，print_r，var_dump四种输出

PHP EOF(heredoc)是一种在命令行shell（如sh、csh、ksh、bash、PowerShell和zsh）和程序语言（像Perl、PHP、Python和Ruby）里定义一个字符串的方法

[![Hi7eCn.png](https://s4.ax1x.com/2022/02/01/Hi7eCn.png)](https://imgtu.com/i/Hi7eCn)

PHP有7种数据类型，String（字符串）, Integer（整型）, Float（浮点型）, Boolean（布尔型）, Array（数组）, Object（对象）, NULL（空值），我们注意对象，数组即可

[![Hi7uvV.png](https://s4.ax1x.com/2022/02/01/Hi7uvV.png)](https://imgtu.com/i/Hi7uvV)

[![Hi73E4.png](https://s4.ax1x.com/2022/02/01/Hi73E4.png)](https://imgtu.com/i/Hi73E4)

PHP类型比较：

- 松散比较：使用两个等号 == 比较，只比较值，不比较类型。
- 严格比较：用三个等号 === 比较，除了比较值，也比较类型。

PHP的if-else，while，switch，for循环

[![Hi7DVe.png](https://s4.ax1x.com/2022/02/01/Hi7DVe.png)](https://imgtu.com/i/Hi7DVe)

[![Hi7rUH.png](https://s4.ax1x.com/2022/02/01/Hi7rUH.png)](https://imgtu.com/i/Hi7rUH)

[![Hi7IaQ.png](https://s4.ax1x.com/2022/02/01/Hi7IaQ.png)](https://imgtu.com/i/Hi7IaQ)[![Hi75Vg.png](https://s4.ax1x.com/2022/02/01/Hi75Vg.png)](https://imgtu.com/i/Hi75Vg)

PHP数组：

自动分配ID号

~~~php
<?php
$cars=array("Volvo","BMW","Toyota");
echo "I like " . $cars[0] . ", " . $cars[1] . " and " . $cars[2] . ".";
?>
~~~

~~~php
1、获取数组的长度 - count() 函数
count($cars);
2、遍历数值数组
for循环罢了
3、PHP 关联数组
<?php
$age=array("Peter"=>"35","Ben"=>"37","Joe"=>"43");
echo "Peter is " . $age['Peter'] . " years old.";
?>
4、遍历关联数组
<?php
$age=array("Peter"=>"35","Ben"=>"37","Joe"=>"43");
foreach($age as $x=>$x_value)
{
    echo "Key=" . $x . ", Value=" . $x_value;
    echo "<br>";
}
?>
5、数组排序函数
sort() - 对数组进行升序排列
rsort() - 对数组进行降序排列
asort() - 根据关联数组的值，对数组进行升序排列
ksort() - 根据关联数组的键，对数组进行升序排列
arsort() - 根据关联数组的值，对数组进行降序排列
krsort() - 根据关联数组的键，对数组进行降序排列
~~~

### PHP 超级全局变量

$GLOBALS

[$_SERVER](https://www.cnblogs.com/wangshuazi/p/9765012.html)

~~~php
$_SERVER['REMOTE_ADDR'] //当前用户 IP
$_SERVER['REMOTE_HOST'] //当前用户主机名
$_SERVER['HTTP_USER_AGENT'] //当前请求的 User_Agent: 头部的内容
~~~

$_REQUEST

用于收集HTML表单提交的数据

~~~php
<?php 
$name = $_REQUEST['fname']; 
echo $name; 
?>
~~~

$_POST

~~~php
<?php 
$name = $_POST['fname']; 
echo $name; 
?>
~~~

 $_GET

~~~php
<?php 
echo "Study " . $_GET['subject'] . " @ " . $_GET['web'];
?>
~~~

$_FILES：接收传过来的文件

[![HiHtoQ.png](https://s4.ax1x.com/2022/02/01/HiHtoQ.png)](https://imgtu.com/i/HiHtoQ)

$_COOKIE

$_SESSION

函数

function xxx(){	}

PHP魔术变量

~~~
__LINE__
文件中的当前行号
__FILE__
文件的完整路径和文件名。如果用在被包含文件中，则返回被包含的文件名
__DIR__
文件所在的目录。如果用在被包括文件中，则返回被包括的文件所在的目录
__FUNCTION__
函数名称（PHP 4.3.0 新加）。自 PHP 5 起本常量返回该函数被定义时的名字
__CLASS__
类的名称（PHP 4.3.0 新加）。自 PHP 5 起本常量返回该类被定义时的名字
~~~

面向对象

~~~
类 − 定义了一件事物的抽象特点。类的定义包含了数据的形式以及对数据的操作。比如狗
对象 − 是类的实例。比如小白狗
成员变量 − 定义在类内部的变量。该变量的值对外是不可见的，但是可以通过成员函数访问，在类被实例化为对象后，该变量即可成为对象的属性。比如狗都听话，所以小白狗听话
成员函数 − 定义在类的内部，可用于访问对象的数据。小白狗的方法
继承 − 继承性是子类自动共享父类数据结构和方法的机制，这是类之间的一种关系。在定义和实现一个类的时候，可以在一个已经存在的类的基础之上来进行，把这个已经存在的类所定义的内容作为自己的内容，并加入若干新的内容。
父类 − 一个类被其他类继承，可将该类称为父类，或基类，或超类。
子类 − 一个类继承其他类称为子类，也可称为派生类。
多态 − 多态性是指相同的函数或方法可作用于多种类型的对象上并获得不同的结果。不同的对象，收到同一消息可以产生不同的结果，这种现象称为多态性。
封装 − 封装是指将现实世界中存在的某个客体的属性与行为绑定在一起，并放置在一个逻辑单元内。
构造函数 − 主要用来在创建对象时初始化对象， 即为对象成员变量赋初始值，总与new运算符一起使用在创建对象的语句中。
析构函数 − 析构函数(destructor) 与构造函数相反，当对象结束其生命周期时（例如对象所在的函数已调用完毕），系统自动执行析构函数。析构函数往往用来做"清理善后" 的工作（例如在建立对象时用new开辟了一片内存空间，应在退出前在析构函数中用delete释放）。
~~~

**$this** 代表自身的对象，**PHP_EOL** 为换行符

[![HFXGtS.png](https://s4.ax1x.com/2022/02/01/HFXGtS.png)](https://imgtu.com/i/HFXGtS)

PHP 构造函数

构造函数是一种特殊的方法。主要用来在创建对象时初始化对象， 即为对象成员变量赋初始值，在创建对象的语句中与 new 运算符一起使用

析构函数(destructor) 与构造函数相反，当对象结束其生命周期时（例如对象所在的函数已调用完毕），系统自动执行析构函数

函数首先调用construct，函数结构后调用destruct

继承

PHP 使用关键字 **extends** 来继承一个类，PHP 不支持多继承， Child_Site 类继承了 Site 类，并扩展了功能：

~~~php
<?php 
// 子类扩展站点类别
class Child_Site extends Site {
   var $category;

    function setCate($par){
        $this->category = $par;
    }
  
    function getCate(){
        echo $this->category . PHP_EOL;
    }
}
~~~

方法重写

如果从父类继承的方法不能满足子类的需求，可以对其进行改写，这个过程叫方法的覆盖（override），也称为方法的重写

~~~
function getUrl() {
   echo $this->url . PHP_EOL;
   return $this->url;
}
   
function getTitle(){
   echo $this->title . PHP_EOL;
   return $this->title;
}
~~~

访问控制

PHP 对属性或方法的访问控制，是通过在前面添加关键字 public（公有），protected（受保护）或 private（私有）来实现的。

- **public（公有）：**公有的类成员可以在任何地方被访问。
- **protected（受保护）：**受保护的类成员则可以被其自身以及其子类和父类访问。
- **private（私有）：**私有的类成员则只能被其定义所在的类访问。

### 方法的访问控制

~~~
// 声明一个公有的构造函数
    public function __construct() { }
// 声明一个公有的方法
    public function MyPublic() { }
~~~