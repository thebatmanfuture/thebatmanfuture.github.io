##### 1.在thinkphp中.env配置文件和config下的database.php配置文件有什么区别，如何在控制器中获取里面的值？

~~~php
两者都是用来设置数据库连接信息的，如果是在本地开发，.env的优先级高于database.php

.env配置文件主要用来存储环境变量，可以在本地开发环境和生产环境
config下的database.php配置文件一般在直接部署在生产环境

在控制器中获取.env配置文件和config下的database.php配置文件中的值，可以通过调用env函数或者config函数来实现

public function config()
{
    //        return Env::get('DATABASE.DATABASE');
    return config::get('database.connections.mysql.hostname');
}
~~~



##### 2.在多应用模式下的thinkphp6框架下，如果我想要访问index应用下的index控制器下的test方法，参数是id，值是123，请给出url

~~~
http://localhost/public/index.php/index/index/test/id/123
~~~

public是对外提供访问，index.php是入口文件，index是应用，index是控制器，test是方法

[![p9GbM1H.png](https://s1.ax1x.com/2023/05/02/p9GbM1H.png)](https://imgse.com/i/p9GbM1H)

某些情况下index.php可以省略只要在设置url重写，在httpd.conf下的mod_rewrite.so，index控制器也可以省略

[![p9Gb5uR.png](https://s1.ax1x.com/2023/05/02/p9Gb5uR.png)](https://imgse.com/i/p9Gb5uR)

再把allowoverride改成all，然后重启apache

[![p9GboHx.png](https://s1.ax1x.com/2023/05/02/p9GboHx.png)](https://imgse.com/i/p9GboHx)

.

##### 3.基础控制器，空控制器和多应用控制器是什么，在tp6中多层控制器和多级控制器是一个概念么？

基础控制器，在继承 `app\BaseController` 之后可以使用基础控制器的验证功能，并注入了`think\App`和`think\Request`对象

![07eabb0103f31a797b8c112d955bb558.png](https://s1.imagehub.cc/images/2023/01/27/07eabb0103f31a797b8c112d955bb558.png)

![13a0bd29dcc09e93071c4028438a3076.png](https://s1.imagehub.cc/images/2023/01/27/13a0bd29dcc09e93071c4028438a3076.png)

空控制器

给项目定义一个error的控制器类，来提醒报错

![909c880ad2ea2b0f850c0a0c64997dd8.png](https://s1.imagehub.cc/images/2023/01/27/909c880ad2ea2b0f850c0a0c64997dd8.png)

![51d1f41b65973cc25ccabef48f171d8a.png](https://s1.imagehub.cc/images/2023/01/27/51d1f41b65973cc25ccabef48f171d8a.png)

多级控制器和多层控制器不是一个概念，例如

app\admin\controller\User\config.php

即在admin层下的User级下的config控制器下的test方法，访问url是：

localhost/public/index.php/admin/user.config/test

##### 4.thinkphp框架内置的ORM，它和python，dotnet，以及java中的某些内置SQL查询语法是否防SQL注入

thinkphp框架使用内置查询方法如果在语句不写错的情况下，完全按照官方写法，大部分常见的是防SQL注入的，但是如果对官方语法修改了就会存在注入，例如where这个链式查询

~~~php
use think\facade\Db;
// 不存在注入
    public function sql($id)
    {
        $user = Db::table('ty_user')->where('id', $id)->find();
        return Db::getLastsql();
    }
//存在注入
    public function sqli($id)
    {
        $user = Db::table('ty_user')->where('id='.$id)->find();
        return Db::getLastsql();
    }
~~~

[![p9GvhvD.png](https://s1.ax1x.com/2023/05/02/p9GvhvD.png)](https://imgse.com/i/p9GvhvD)

[![p9GvfgO.png](https://s1.ax1x.com/2023/05/02/p9GvfgO.png)](https://imgse.com/i/p9GvfgO)

实际开发中真的有人会这么写的

[![p9Gvobd.png](https://s1.ax1x.com/2023/05/02/p9Gvobd.png)](https://imgse.com/i/p9Gvobd)

另外就是其他一些链式查询，比如alias，field，table，group by，having，join是存在注入的

在python中的orm语法是防注入的

~~~python
test = models.User.objects.filter(pk=delete_id).first()
~~~

在dotnet中中的linq语法是防注入的

~~~c#
List<string> names = new List<string> { "Alice", "Bob", "Charlie", "Dave" };
var result = from name in names
             where name.Length == 4
             select name;
~~~

在java中中的orm语法也是防注入的



##### 5.thinkphp6中利用return dump($user)，这样得到是结果是什么类型？

如果是select()，则是数据集对象Collection

~~~php
    public function sql($id)
    {
        $user = Db::table('ty_user')->where('id', $id)->select();
        return dump($user);
    }
~~~

如果是find()这种查询一条的是数组

~~~php
    public function sql($id)
    {
        $user = Db::table('ty_user')->where('id', $id)->find();
        return dump($user);
    }
~~~

可以通过->toArray()将数据集转换为数组

##### 6.在tp6中Db:table()和Db::name()的区别

前者主要用于指定数据表名称并返回一个QueryBuilder实例，而后者则是用于指定数据库资源名称

但是name是在某些情况下，我们可能需要连接多个数据库或使用不同的数据库连接配置进行查询

例如：

~~~php
use think\facade\Db;
// 查询user表中所有的记录
$data = Db::table('user')->select();

use think\facade\Db;
$config = [
    'type'     => 'mysql',
    'hostname' => '127.0.0.1',
    'database' => 'db2',
    'username' => 'root',
    'password' => '',
];
Db::setConfig('db2', $config);
$data = Db::name('user', [], 'db2')->select();
~~~



##### 7.tp6中数据分批处理是什么，游标查询是什么？

分批处理，将数据库中的内容每次取出100条

~~~php
Db::table('think_user')->chunk(100, function($users) {
    foreach ($users as $user) {
        // 处理结果集...
		if($user->status==0){
            return false;
        }
    }
});
~~~

游标查询

读取日志，或者数据库等较大文件时，一次读到内存5个g是不太现实的，游标查询利用了PHP的生成器特性，一次只读取一部分，可以大幅减少大量数据查询的内存开销问题，生成器是特殊的迭代器，所以直接利用foreach循环遍历取出即可

~~~php
$cursor = Db::table('user')->where('status', 1)->cursor();
foreach($cursor as $user){
	echo $user['name'];
}
~~~

##### 8.什么是软删除？

软删除指的是在数据库中将数据标记为已删除（例如添加一个“deleted_at”字段），而不是实际删除该数据



##### 9.什么是显式路由，什么是隐式路由并举例？

在ThinkPHP中，默认情况下会自动将URL中的路径解析为控制器和方法名，并直接执行对应的方法，这个过程称为“隐式路由”

而在Django中，路由必须显式地定义在urls.py文件中，如果没有定义对应的路由，就无法访问views.py下的方法，称显式路由

简单来说就是隐式路由可以不写，就能直接访问对应的控制器方法；而显式路由必须先定义路由才能指向方法访问

在thinkphp中config下的route.php中可以设置成强制路由

[![p9J9474.png](https://s1.ax1x.com/2023/05/02/p9J9474.png)](https://imgse.com/i/p9J9474)[![p9J9IAJ.png](https://s1.ax1x.com/2023/05/02/p9J9IAJ.png)](https://imgse.com/i/p9J9IAJ)



##### 10.什么是依赖注入？

依赖注入是指在一个对象中引用另一个对象的实例时，不直接在代码中创建它，而是通过容器将其注入进来，类之间的依赖关系由容器来负责，简单来讲a依赖b，但a不创建（或销毁）b，仅使用b，b的创建（或销毁）交给容器

~~~java
public class MyService {
    private MyDependency dependency;

    public MyService(MyDependency dependency) {
        this.dependency = dependency;
    }

    public void doSomething() {
        // 使用MyDependency对象执行一些操作
        dependency.doSomeWork();
    }
}
~~~



~~~java
public class MyContainer {
    public static void main(String[] args) {
        // 创建依赖实例
        MyDependency dependency = new MyDependencyImpl();

        // 将依赖实例注入到服务实例中
        MyService service = new MyService(dependency);

        // 使用服务对象执行操作
        service.doSomething();
    }
}
~~~

##### 11.尽可能多的列举请求对象和信息的获取

~~~php
<?php

namespace app\controller;

use think\Request;

class rely
{
    public function __construct(Request $request)
    {
        $this->request = $request;
    }
    // 第一种直接return返回
    public function index()
    {
        return $_GET['username'];
    }
	// 第二种通过依赖注入
    public function test(Request $request)
    {
        return $request->param('username');
    }
    //第三种，用构造函数保存下来
    public function get()
    {
        return $this->request->param('username');
    }    
    //第四种，门面模式facade
    use think\facade\Request;
    public function post()
    {
        return Request::param('username');
    }
	//第五种，助手函数
    public function get1()
    {
        return Request()->param('username');
    }    
}
~~~



##### 12.tp6模板中如何传值？

~~~php
    public function tem()
    {
        $arr123 = ['name'=>'Mr.lee', 'age'=>100];

        return View::fetch('output', [
            'arr' => $arr123
        ]);
    }
~~~

view/index/output.html

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
{$arr.name}
</body>
</html>
~~~

##### 13.python，java，c#可以多继承么

Python和C#是支持多继承的，而Java不支持。

~~~python
class Base1:
    def method1(self):
        print("Base1 Method 1")
        
class Base2:
    def method2(self):
        print("Base2 Method 2")
        
class MyClass(Base1, Base2):
    def method3(self):
        print("MyClass Method 3")

mc = MyClass()
mc.method1()  # Output: Base1 Method 1
mc.method2()  # Output: Base2 Method 2
mc.method3()  # Output: MyClass Method 3
~~~



~~~c#
using System;

public class Base1
{
    public void Method1()
    {
        Console.WriteLine("Base1 Method 1");
    }
}

public class Base2
{
    public void Method2()
    {
        Console.WriteLine("Base2 Method 2");
    }
}

public class MyClass : Base1, Base2
{
    public void Method3()
    {
        Console.WriteLine("MyClass Method 3");
    }
}

class Program
{
    static void Main(string[] args)
    {
        MyClass mc = new MyClass();
        mc.Method1(); // Output: Base1 Method 1
        mc.Method2(); // Output: Base2 Method 2
        mc.Method3(); // Output: MyClass Method 3
    }
}
~~~



##### 14.在面向对象编程中，实现代码复用的方式？

（多）继承和组合，这也是23种设计模式中适配器模式的类适配器和对象适配器原理

组合

~~~python
#	在B类中初始化实例A，再调用A类下的haha方法
class A:
    def haha():
        print("haha")
class B:
	def __init__():
		self.a = A()
        self.a.haha()
~~~

##### 15.什么是高内聚和低耦合？

高内聚指的是一个类或方法应该尽可能地完成单一任务，而不是做太多的事情；低耦合指的是各个类或方法之间的依赖关系应该尽可能地简单，降低彼此之间的相互影响

##### 16.TP框架中的M、D、I、S方法是什么？

~~~
M实例化参数是数据库的表名，某表
D实例化的是你自己在Model文件夹下面建立的模型文件，在Model中再执行数据库查询操作
I方法，输入方法例如$user = I('post.id','','trim');
S方法，读取数据缓存,而且是要设置 DATA_CACHE_TYPE 配置
~~~



### 安全相关

##### 如果你有一套基于thinkphp框架开发项目的代码，你如何找历史漏洞？

全局搜索THINK_VERSION，或者直接在thinkphp/base.php下找到tp版本，然后找对应的漏洞，以及漏洞造成原理分析判断

![image936d54b9d677c609.png](https://cdnjson.com/images/2023/05/18/image936d54b9d677c609.png)





















