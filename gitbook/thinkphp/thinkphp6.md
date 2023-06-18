## 基础

### 单应用模式

~~~
www  WEB部署目录（或者子目录）
├─app           应用目录
│  ├─controller      控制器目录
│  ├─model           模型目录
│  ├─ ...            更多类库目录
│  │
│  ├─common.php         公共函数文件
│  └─event.php          事件定义文件
│
├─config                配置目录
│  ├─app.php            应用配置
│  ├─cache.php          缓存配置
│  ├─console.php        控制台配置
│  ├─cookie.php         Cookie配置
│  ├─database.php       数据库配置
│  ├─filesystem.php     文件磁盘配置
│  ├─lang.php           多语言配置
│  ├─log.php            日志配置
│  ├─middleware.php     中间件配置
│  ├─route.php          URL和路由配置
│  ├─session.php        Session配置
│  ├─trace.php          Trace配置
│  └─view.php           视图配置
│
├─view            视图目录
├─route                 路由定义目录
│  ├─route.php          路由定义文件
│  └─ ...   
│
├─public                WEB目录（对外访问目录）
│  ├─index.php          入口文件
│  ├─router.php         快速测试文件
│  └─.htaccess          用于apache的重写
│
├─extend                扩展类库目录
├─runtime               应用的运行时目录（可写，可定制）
├─vendor                Composer类库目录
├─.example.env          环境变量示例文件
├─composer.json         composer 定义文件
├─LICENSE.txt           授权说明文件
├─README.md             README 文件
├─think                 命令行入口文件
~~~



在实际的部署中，请确保只有`public`目录可以对外访问。

默认安装后，`app`目录下会包含下面的文件。

~~~
├─app           应用目录
│  │
│  ├─BaseController.php    默认基础控制器类
│  ├─ExceptionHandle.php   应用异常定义文件
│  ├─common.php            全局公共函数文件
│  ├─middleware.php        全局中间件定义文件
│  ├─provider.php          服务提供定义文件
│  ├─Request.php           应用请求对象
│  └─event.php             全局事件定义文件
~~~



### 开启调试

#### 环境变量定义

可以在应用的根目录下定义一个特殊的`.env`环境变量文件，用于在开发过程中模拟环境变量配置（该文件建议在服务器部署的时候忽略），`.env`文件中的配置参数定义格式采用`ini`方式，例如：

```
APP_DEBUG =  true
```

> 默认安装后的根目录有一个`.example.env`环境变量示例文件，你可以直接改成`.env`文件后进行修改。

> 如果你的部署环境单独配置了环境变量（ 环境变量的前缀使用`PHP_`），那么请删除`.env`配置文件，避免冲突。

环境变量配置的参数会全部转换为大写，值为 `off`，`no` 和 `false` 等效于 布尔值`false`，值为 `yes` 、`on`和 `true` 等效于 布尔值的`true`。



如果想在主控制器中创建方法调用其他内容，则

~~~php
    public function config()
    {
        return Env::get('DATABASE.DATABASE');
    }
~~~

![99ea6171f5662d30114a7ca036d5d07b.png](https://s1.imagehub.cc/images/2023/01/27/99ea6171f5662d30114a7ca036d5d07b.png)

~~~
即调取ENV下的
~~~

![a1aab116826cd86bb878bcd52aa9af72.png](https://s1.imagehub.cc/images/2023/01/27/a1aab116826cd86bb878bcd52aa9af72.png)

~~~php
同理，如果像调用
    public function config()
    {
//        return Env::get('DATABASE.DATABASE');
        return config::get('database.connections.mysql.hostname');
    }
~~~

![00a4dd74ad128e000ca9d0f3a048a61a.png](https://s1.imagehub.cc/images/2023/01/27/00a4dd74ad128e000ca9d0f3a048a61a.png)

.env的优先级高与config下的



### URL访问模式

**配置文件 config/route.php中可以定义修改controller的控制器的目录名，所以代码审计需要先找到app下的这个文件**



PHP不建议用die，exit等中断代码执行，建议使用halt()助手函数

halt("中断输出");



`6.0`的URL访问受路由影响，如果在没有定义或匹配路由的情况下（并且没有开启强制路由模式的话），则是基于：

```
http://serverName/index.php（或者其它入口文件）/控制器/操作/参数/值…
```

![4d90e6e7aecd2a6481bf9f14d47057f9.png](https://s1.imagehub.cc/images/2023/01/27/4d90e6e7aecd2a6481bf9f14d47057f9.png)

~~~
如图，index.php就是入口文件，test是控制器，hello是控制器内的方法，value是方法调用传递的参数名，而123就是参数值
~~~

入口文件如下是public下的

![ede82bbd83d8b128aed66f642fc1ab94.png](https://s1.imagehub.cc/images/2023/01/27/ede82bbd83d8b128aed66f642fc1ab94.png)

### 控制器定义

控制器文件通常放在`controller`下面，类名和文件名保持大小写一致，并采用驼峰命名（首字母大写）。

如果要改变`controller`目录名，需要在`route.php`配置文件中设置：

```
'controller_layer'    =>    'controllers',
```

如果使用的是单应用模式，那么控制器的类的定义如下：

```
<?php
namespace app\controller;

class User 
{
    public function login()
    {
        return 'login';
    }
}
```

控制器类文件的实际位置则变成

```
app\controller\User.php
```

访问URL地址是（假设没有定义路由的情况下）

```
http://localhost/user/login
```

如果你的控制器是`HelloWorld`，并且定义如下：

```
<?php
namespace app\controller;

class HelloWorld 
{
    public function hello()
    {
        return 'hello，world！';
    }
}
```

控制器类文件的实际位置是

```
app\controller\HelloWorld.php
```

访问URL地址是（假设没有定义路由的情况下）

```
http://localhost/index.php/HelloWorld/hello
```

并且也可以支持下面的访问URL

```
http://localhost/hello_world/hello
```

![03725db63e20037cba3c81cb7ed443e4.png](https://s1.imagehub.cc/images/2023/01/27/03725db63e20037cba3c81cb7ed443e4.png)



### 基础.空.多级控制器

大多数情况下，我们建议给你的控制器继承一个基础控制器。

默认安装后，系统提供了一个`app\BaseController`基础控制器类，你可以对该基础控制器进行修改。



该基础控制器仅仅提供了控制器验证功能，并注入了`think\App`和`think\Request`对象，因此你可以直接在控制器中使用`app`和`request`属性调用`think\App`和`think\Request`对象实例，下面是一个例子：

![07eabb0103f31a797b8c112d955bb558.png](https://s1.imagehub.cc/images/2023/01/27/07eabb0103f31a797b8c112d955bb558.png)

![13a0bd29dcc09e93071c4028438a3076.png](https://s1.imagehub.cc/images/2023/01/27/13a0bd29dcc09e93071c4028438a3076.png)

空控制器

空控制器的概念是指当系统找不到指定的控制器名称的时候，系统会尝试定位当前应用下的空控制器(`Error`)类，利用这个机制我们可以用来定制错误页面和进行URL的优化。

例如，下面是单应用模式下，我们可以给项目定义一个`Error`控制器类。

![909c880ad2ea2b0f850c0a0c64997dd8.png](https://s1.imagehub.cc/images/2023/01/27/909c880ad2ea2b0f850c0a0c64997dd8.png)

![51d1f41b65973cc25ccabef48f171d8a.png](https://s1.imagehub.cc/images/2023/01/27/51d1f41b65973cc25ccabef48f171d8a.png)

多级控制器

[![ppCRiX4.png](https://s1.ax1x.com/2023/02/28/ppCRiX4.png)](https://imgse.com/i/ppCRiX4)

[![ppCR9pT.png](https://s1.ax1x.com/2023/02/28/ppCR9pT.png)](https://imgse.com/i/ppCR9pT)

### 连接数据库和模型

.env文件是在本地连接测试用

![619250ff07b96ef8f4604e80e752d3e9.png](https://s1.imagehub.cc/images/2023/01/27/619250ff07b96ef8f4604e80e752d3e9.png)

database.php是在部署之后使用，在本地时，优先级是.env大于database.php

![06f53d4192dab54db365922de116f4c5.png](https://s1.imagehub.cc/images/2023/01/27/06f53d4192dab54db365922de116f4c5.png)

![d576cdc1e06e33e9b25efa255a1bdf44.png](https://s1.imagehub.cc/images/2023/01/27/d576cdc1e06e33e9b25efa255a1bdf44.png)

。

这里我们创建一个datatest的控制器，用来测试

![872599a28b4d1bad1501272a3b1f3d32.png](https://s1.imagehub.cc/images/2023/01/27/872599a28b4d1bad1501272a3b1f3d32.png)

如图，在选择默认php10数据下调用

Db::table('msg')->select();调用php10库下的msg表，并且用select()全部显示输出，最后用json格式输出

![dc2b0b2a664a6416ee3f914f1a42e08e.png](https://s1.imagehub.cc/images/2023/01/27/dc2b0b2a664a6416ee3f914f1a42e08e.png)

​	如果想再调用其他数据库，需要在database.php中设置

![d1e6b81e99ee0445bdd275ba33565a6e.png](https://s1.imagehub.cc/images/2023/01/27/d1e6b81e99ee0445bdd275ba33565a6e.png)![aa027fbf499d0a60b437451b2e9cb586.png](https://s1.imagehub.cc/images/2023/01/27/aa027fbf499d0a60b437451b2e9cb586.png)![98fc0be261cd137ad1e440899a18f139.png](https://s1.imagehub.cc/images/2023/01/27/98fc0be261cd137ad1e440899a18f139.png)

![8d70c470868577410265fe630fad44c9.png](https://s1.imagehub.cc/images/2023/01/27/8d70c470868577410265fe630fad44c9.png)

![ca72e2f679edc5a6928f4a7ffdd4ff2c.png](https://s1.imagehub.cc/images/2023/01/27/ca72e2f679edc5a6928f4a7ffdd4ff2c.png)

。

模型

也可以在模型中自定义之后，直接指定表名

![21d445f7da7048ac45d2b116c51d420d.png](https://s1.imagehub.cc/images/2023/01/27/21d445f7da7048ac45d2b116c51d420d.png)![1a9dc32aeb9d03a107b27180ba68b9f4.png](https://s1.imagehub.cc/images/2023/01/27/1a9dc32aeb9d03a107b27180ba68b9f4.png)

![6bf2ce8a61bf8244ba54325da3111a6e.png](https://s1.imagehub.cc/images/2023/01/27/6bf2ce8a61bf8244ba54325da3111a6e.png)

同理，我可以将model中的mysql（对应database.php中的mysql），改成test，那么默认就是取test数据库下的那个

![aeeee640cddcd0612a40ec2289cec0ca.png](https://s1.imagehub.cc/images/2023/01/27/aeeee640cddcd0612a40ec2289cec0ca.png)

![16ac63091a8358a613bebe8377a60376.png](https://s1.imagehub.cc/images/2023/01/27/16ac63091a8358a613bebe8377a60376.png)![2aaefeaa0aad392a6931af3fc92f9e9c.png](https://s1.imagehub.cc/images/2023/01/27/2aaefeaa0aad392a6931af3fc92f9e9c.png)

。

### 数据库的数据查询

#### 查询单个数据

查询单个数据使用`find`方法：

```
// table方法必须指定完整的数据表名
Db::table('think_user')->where('id', 1)->find();
```

最终生成的SQL语句可能是：

```
SELECT * FROM `think_user` WHERE  `id` = 1 LIMIT 1
```

> `find`方法查询结果不存在，返回 `null`，否则返回结果数组

![c398c7117d2fd5795945405edd7615f0.png](https://s1.imagehub.cc/images/2023/01/27/c398c7117d2fd5795945405edd7615f0.png)

![2fa88f2b75bf458714933349921aeb07.png](https://s1.imagehub.cc/images/2023/01/27/2fa88f2b75bf458714933349921aeb07.png)

~~~sql
        return Db::getLastsql();
~~~

![8b2fae5d32531b96f3be006cf7b09dea.png](https://s1.imagehub.cc/images/2023/01/27/8b2fae5d32531b96f3be006cf7b09dea.png)

如果希望查询数据不存在的时候返回空数组，可以使用

```
// table方法必须指定完整的数据表名
Db::table('think_user')->where('id', 1)->findOrEmpty();
```

如果希望在没有找到数据后抛出异常可以使用

```
Db::table('think_user')->where('id', 1)->findOrFail();
```

如果没有查找到数据，则会抛出一个`think\db\exception\DataNotFoundException`异常。

> 如果没有任何的查询条件 并且也没有调用`order`方法的话 ，`find`查询不会返回任何结果。

#### 查询数据集

查询多个数据（数据集）使用`select`方法：

```
Db::table('think_user')->where('status', 1)->select();
```

最终生成的SQL语句可能是：

```
SELECT * FROM `think_user` WHERE `status` = 1
```

> `select` 方法查询结果是一个数据集对象，如果需要转换为数组可以使用

![94a8dfd689c5b61dfedc1262784340bf.png](https://s1.imagehub.cc/images/2023/01/27/94a8dfd689c5b61dfedc1262784340bf.png)

```
Db::table('think_user')->where('status', 1)->select()->toArray();
```

![aa1b71abebd159e30f7824b38381072b.png](https://s1.imagehub.cc/images/2023/01/27/aa1b71abebd159e30f7824b38381072b.png)![aa1b71abebd159e30f7824b38381072b.png](https://s1.imagehub.cc/images/2023/01/27/aa1b71abebd159e30f7824b38381072b.png)



如果希望在没有查找到数据后抛出异常可以使用

```
Db::table('think_user')->where('status',1)->selectOrFail();
```

如果没有查找到数据，同样也会抛出一个`think\db\exception\DataNotFoundException`异常。

如果设置了数据表前缀参数的话，可以使用

```
Db::name('user')->where('id', 1)->find();
Db::name('user')->where('status', 1)->select();
```

> 如果你的数据表没有设置表前缀的话，那么`name`和`table`方法效果一致。

在`find`和`select`方法之前可以使用所有的链式操作（参考链式操作章节）方法。

#### 值和列查询

查询某个字段的值可以用

```
// 返回某个字段的值
Db::table('think_user')->where('id', 1)->value('name');
```

> value 方法查询结果不存在，返回 null

![bed3c9454b3e021bb3996f62e9eefd51.png](https://s1.imagehub.cc/images/2023/01/27/bed3c9454b3e021bb3996f62e9eefd51.png)![acb9c39bd74a4c43a887ea3856967dec.png](https://s1.imagehub.cc/images/2023/01/27/acb9c39bd74a4c43a887ea3856967dec.png)

查询某一列的值可以用

```
// 返回数组
Db::table('think_user')->where('status',1)->column('name');
// 指定id字段的值作为索引
Db::table('think_user')->where('status',1)->column('name', 'id');
```

如果要返回完整数据，并且添加一个索引值的话，可以使用

```
// 指定id字段的值作为索引 返回所有数据
Db::table('think_user')->where('status',1)->column('*','id');
```

> `column`方法查询结果不存在，返回空数组

#### 数据分批处理

如果你需要处理成千上百条数据库记录，可以考虑使用`chunk`方法，该方法一次获取结果集的一小块，然后填充每一小块数据到要处理的闭包，该方法在编写处理大量数据库记录的时候非常有用。

比如，我们可以全部用户表数据进行分批处理，每次处理 100 个用户记录：

```php
Db::table('think_user')->chunk(100, function($users) {
    foreach ($users as $user) {
        //
    }
});
```

![17ff18dda2340ef46961946724b506c8.png](https://s1.imagehub.cc/images/2023/01/27/17ff18dda2340ef46961946724b506c8.png)![30d1ac577881d90a6ce407b9265e9716.png](https://s1.imagehub.cc/images/2023/01/27/30d1ac577881d90a6ce407b9265e9716.png)



你可以通过从闭包函数中返回`false`来中止对后续数据集的处理：

```php
Db::table('think_user')->chunk(100, function($users) {
    foreach ($users as $user) {
        // 处理结果集...
		if($user->status==0){
            return false;
        }
    }
});
```

也支持在`chunk`方法之前调用其它的查询方法，例如：

```php
Db::table('think_user')
->where('score','>',80)
->chunk(100, function($users) {
    foreach ($users as $user) {
        //
    }
});
```

`chunk`方法的处理默认是根据主键查询，支持指定字段，例如：

```php
Db::table('think_user')->chunk(100, function($users) {
    // 处理结果集...
    return false;
},'create_time');
```

并且支持指定处理数据的顺序。

```php
Db::table('think_user')->chunk(100, function($users) {
    // 处理结果集...
    return false;
},'create_time', 'desc');
```

> `chunk`方法一般用于命令行操作批处理数据库的数据，不适合WEB访问处理大量数据，很容易导致超时。

#### 游标查询

如果你需要处理大量的数据，可以使用新版提供的游标查询功能，该查询方式利用了PHP的生成器特性，可以大幅减少大量数据查询的内存开销问题。

```php
$cursor = Db::table('user')->where('status', 1)->cursor();
foreach($cursor as $user){
	echo $user['name'];
}
```

`cursor`方法返回的是一个生成器对象，`user`变量是数据表的一条数据（数组）。



### 数据库的链式查询

更多查询

如果多次使用数据库，每次静态都会生成一个实例，造成浪费。

![8ea6393d6dc2ce6ded3fe0bcbb6b4101.png](https://s1.imagehub.cc/images/2023/01/27/8ea6393d6dc2ce6ded3fe0bcbb6b4101.png)



### 数据库的新增

单数据新增

##### strict

`strict`方法用于设置是否严格检查字段名，用法如下：

```
// 关闭字段严格检查
Db::name('user')
    ->strict(false)
    ->insert($data);
```

注意，系统默认值是由数据库配置参数`fields_strict`决定，因此修改数据库配置参数可以进行全局的严格检查配置，如下：

```
// 关闭严格检查字段是否存在
'fields_strict'  => false,
```

###### 添加一条数据

可以使用`save`方法统一写入数据，自动判断是新增还是更新数据（以写入数据中是否存在主键数据为依据）。

```
$data = ['foo' => 'bar', 'bar' => 'foo'];
Db::name('user')->save($data);
```

或者使用 `insert` 方法向数据库提交数据

```
$data = ['foo' => 'bar', 'bar' => 'foo'];
Db::name('user')->insert($data);
```

> `insert` 方法添加数据成功返回添加成功的条数，通常情况返回 1

> 如果你的数据表里面没有`foo`或者`bar`字段，那么就会抛出异常。

如果不希望抛出异常，可以使用下面的方法：

```
$data = ['foo' => 'bar', 'bar' => 'foo'];
Db::name('user')->strict(false)->insert($data);
```

不存在字段的值将会直接抛弃。

如果是mysql数据库，支持`replace`写入，例如：

```
$data = ['foo' => 'bar', 'bar' => 'foo'];
Db::name('user')->replace()->insert($data);
```

添加数据后如果需要返回新增数据的自增主键，可以使用`insertGetId`方法新增数据并返回主键值：

```
$userId = Db::name('user')->insertGetId($data);
```

> `insertGetId` 方法添加数据成功返回添加数据的自增主键

###### 添加多条数据

添加多条数据直接向 Db 类的 `insertAll` 方法传入需要添加的数据（通常是二维数组）即可。

```
$data = [
    ['foo' => 'bar', 'bar' => 'foo'],
    ['foo' => 'bar1', 'bar' => 'foo1'],
    ['foo' => 'bar2', 'bar' => 'foo2']
];
Db::name('user')->insertAll($data);
```

> `insertAll`方法添加数据成功返回添加成功的条数

如果是mysql数据库，支持`replace`写入，例如：

```
$data = [
    ['foo' => 'bar', 'bar' => 'foo'],
    ['foo' => 'bar1', 'bar' => 'foo1'],
    ['foo' => 'bar2', 'bar' => 'foo2']
];
Db::name('user')->replace()->insertAll($data);
```

> 确保要批量添加的数据字段是一致的

如果批量插入的数据比较多，可以指定分批插入，使用`limit`方法指定每次插入的数量限制。

```
$data = [
    ['foo' => 'bar', 'bar' => 'foo'],
    ['foo' => 'bar1', 'bar' => 'foo1'],
    ['foo' => 'bar2', 'bar' => 'foo2']
    ...
];
// 分批写入 每次最多100条数据
Db::name('user')
    ->limit(100)
    ->insertAll($data);
```





### 数据库的修改删除

##### 更新数据

可以使用save方法更新数据

```
Db::name('user')
    ->save(['id' => 1, 'name' => 'thinkphp']);
```

或者使用`update`方法。

```
Db::name('user')
    ->where('id', 1)
    ->update(['name' => 'thinkphp']);
```

实际生成的SQL语句可能是：

```
UPDATE `think_user`  SET `name`='thinkphp'  WHERE  `id` = 1
```

> `update`方法返回影响数据的条数，没修改任何数据返回 0

![cf69f1b73fff340d8db38f811c9f2738.png](https://s1.imagehub.cc/images/2023/01/27/cf69f1b73fff340d8db38f811c9f2738.png)

![ac0da3275c4107d02912ec3f8d7bef5b.png](https://s1.imagehub.cc/images/2023/01/27/ac0da3275c4107d02912ec3f8d7bef5b.png)

支持使用`data`方法传入要更新的数据

```
Db::name('user')
    ->where('id', 1)
    ->data(['name' => 'thinkphp'])
    ->update();
```

> 如果`update`方法和`data`方法同时传入更新数据，则以`update`方法为准。

如果数据中包含主键，可以直接使用：

```
Db::name('user')
    ->update(['name' => 'thinkphp','id' => 1]);
```

实际生成的SQL语句和前面用法是一样的：

```
UPDATE `think_user`  SET `name`='thinkphp'  WHERE  `id` = 1
```

如果要更新的数据需要使用`SQL`函数或者其它字段，可以使用下面的方式：

```
Db::name('user')
    ->where('id',1)
    ->exp('name','UPPER(name)')
    ->update();
```

实际生成的SQL语句：

```
UPDATE `think_user`  SET `name` = UPPER(name)  WHERE  `id` = 1
```

![f1b5f8901f14d47edc1eb1e4674df201.png](https://s1.imagehub.cc/images/2023/01/27/f1b5f8901f14d47edc1eb1e4674df201.png)![c841e367e3aab64c29f59d66c1895685.png](https://s1.imagehub.cc/images/2023/01/27/c841e367e3aab64c29f59d66c1895685.png)







支持使用`raw`方法进行数据更新。

```
Db::name('user')
    ->where('id', 1)
    ->update([
        'name'		=>	Db::raw('UPPER(name)'),
        'score'		=>	Db::raw('score-3'),
        'read_time'	=>	Db::raw('read_time+1')
    ]);
```

##### 自增/自减

可以使用`inc/dec`方法自增或自减一个字段的值（ 如不加第二个参数，默认步长为1）。

```
// score 字段加 1
Db::table('think_user')
    ->where('id', 1)
    ->inc('score')
    ->update();

// score 字段加 5
Db::table('think_user')
    ->where('id', 1)
    ->inc('score', 5)
    ->update();

// score 字段减 1
Db::table('think_user')
    ->where('id', 1)
    ->dec('score')
    ->update();

// score 字段减 5
Db::table('think_user')
    ->where('id', 1)
    ->dec('score', 5)
    ->update();
```

最终生成的SQL语句可能是：

```
UPDATE `think_user`  SET `score` = `score` + 1  WHERE  `id` = 1 
UPDATE `think_user`  SET `score` = `score` + 5  WHERE  `id` = 1
UPDATE `think_user`  SET `score` = `score` - 1  WHERE  `id` = 1
UPDATE `think_user`  SET `score` = `score` - 5  WHERE  `id` = 1
```



##### 删除数据

```
// 根据主键删除
Db::table('think_user')->delete(1);
Db::table('think_user')->delete([1,2,3]);

// 条件删除    
Db::table('think_user')->where('id',1)->delete();
Db::table('think_user')->where('id','<',10)->delete();
```

![75fdf4123205e56d81320ab1b8dc232a.png](https://s1.imagehub.cc/images/2023/01/27/75fdf4123205e56d81320ab1b8dc232a.png)![fc34035f6d9e4907ba627f81541ca782.png](https://s1.imagehub.cc/images/2023/01/27/fc34035f6d9e4907ba627f81541ca782.png)

![e5dcf0a82914a4fab74ea6099788d41e.png](https://s1.imagehub.cc/images/2023/01/27/e5dcf0a82914a4fab74ea6099788d41e.png)







最终生成的SQL语句可能是：

```
DELETE FROM `think_user` WHERE  `id` = 1 
DELETE FROM `think_user` WHERE  `id` IN (1,2,3) 
DELETE FROM `think_user` WHERE  `id` = 1 
DELETE FROM `think_user` WHERE  `id` < 10
```

> `delete`方法返回影响数据的条数，没有删除返回 0

如果不带任何条件调用`delete`方法会提示错误，如果你确实需要删除所有数据，可以使用

```
// 无条件删除所有数据
Db::name('user')->delete(true);
```

最终生成的SQL语句是（删除了表的所有数据）：

```
DELETE FROM `think_user`
```

> 一般情况下，业务数据不建议真实删除数据，系统提供了软删除机制（模型中使用软删除更为方便）。

```
// 软删除数据 使用delete_time字段标记删除
Db::name('user')
	->where('id', 1)
	->useSoftDelete('delete_time',time())
    ->delete();
```

实际生成的SQL语句可能如下（执行的是`UPDATE`操作）：

```
UPDATE `think_user`  SET `delete_time` = '1515745214'  WHERE  `id` = 1
```

`useSoftDelete`方法表示使用软删除，并且指定软删除字段为`delete_time`，写入数据为当前的时间戳。



### 数据库的查询表达式

查询表达式支持大部分的SQL查询语法，也是`ThinkPHP`查询语言的精髓，查询表达式的使用格式：

```
where('字段名','查询表达式','查询条件');
```

除了`where`方法外，还可以支持`whereOr`，用法是一样的。为了更加方便查询，大多数的查询表达式都提供了快捷查询方法。

表达式不分大小写，支持的查询表达式有下面几种：

| 表达式             | 含义                              | 快捷查询方法                   |
| :----------------- | :-------------------------------- | :----------------------------- |
| =                  | 等于                              |                                |
| <>                 | 不等于                            |                                |
| >                  | 大于                              |                                |
| >=                 | 大于等于                          |                                |
| <                  | 小于                              |                                |
| <=                 | 小于等于                          |                                |
| [NOT] LIKE         | 模糊查询                          | `whereLike/whereNotLike`       |
| [NOT] BETWEEN      | （不在）区间查询                  | `whereBetween/whereNotBetween` |
| [NOT] IN           | （不在）IN 查询                   | `whereIn/whereNotIn`           |
| [NOT] NULL         | 查询字段是否（不）是NULL          | `whereNull/whereNotNull`       |
| [NOT] EXISTS       | EXISTS查询                        | `whereExists/whereNotExists`   |
| [NOT] REGEXP       | 正则（不）匹配查询（仅支持Mysql） |                                |
| [NOT] BETWEEN TIME | 时间区间比较                      | whereBetweenTime               |
| > TIME             | 大于某个时间                      | `whereTime`                    |
| < TIME             | 小于某个时间                      | `whereTime`                    |
| >= TIME            | 大于等于某个时间                  | `whereTime`                    |
| <= TIME            | 小于等于某个时间                  | `whereTime`                    |
| EXP                | 表达式查询，支持SQL语法           | `whereExp`                     |
| find in set        | FIND_IN_SET查询                   | `whereFindInSet`               |

表达式查询的用法示例如下：

#### 等于（=）

例如：

```
Db::name('user')->where('id','=',100)->select();
```

![8a422e35734902dee84ceef68aa27db0.png](https://s1.imagehub.cc/images/2023/01/27/8a422e35734902dee84ceef68aa27db0.png)![13396856578b56df517626c8e2ca93fc.png](https://s1.imagehub.cc/images/2023/01/27/13396856578b56df517626c8e2ca93fc.png)

和下面的查询等效

```
Db::name('user')->where('id',100)->select();
```

最终生成的SQL语句是：

```
SELECT * FROM `think_user` WHERE  `id` = 100
```

#### 不等于（<>）

例如：

```
Db::name('user')->where('id','<>',100)->select();
```

![352c8d62d47b1edf28588e2d19607ef0.png](https://s1.imagehub.cc/images/2023/01/27/352c8d62d47b1edf28588e2d19607ef0.png)![33240ab027d406e171dfa105a9c32e0c.png](https://s1.imagehub.cc/images/2023/01/27/33240ab027d406e171dfa105a9c32e0c.png)

最终生成的SQL语句是：

```
SELECT * FROM `think_user` WHERE  `id` <> 100
```

#### 大于（>）

例如：

```
Db::name('user')->where('id','>',100)->select();
```

最终生成的SQL语句是：

```
SELECT * FROM `think_user` WHERE  `id` > 100
```

#### 大于等于（>=）

例如：

```
Db::name('user')->where('id','>=',100)->select();
```

最终生成的SQL语句是：

```
SELECT * FROM `think_user` WHERE  `id` >= 100
```

#### 小于（<）

例如：

```
Db::name('user')->where('id', '<', 100)->select();
```

最终生成的SQL语句是：

```
SELECT * FROM `think_user` WHERE  `id` < 100
```

#### 小于等于（<=）

例如：

```
Db::name('user')->where('id', '<=', 100)->select();
```

最终生成的SQL语句是：

```
SELECT * FROM `think_user` WHERE  `id` <= 100
```

#### [NOT] LIKE： 同sql的LIKE

例如：

```
Db::name('user')->where('name', 'like', 'thinkphp%')->select();
```

![e9c933459a7a37059c1e11d90596b832.png](https://s1.imagehub.cc/images/2023/01/27/e9c933459a7a37059c1e11d90596b832.png)![ffa9a969f54476e2cbe0a519319ebe4a.png](https://s1.imagehub.cc/images/2023/01/27/ffa9a969f54476e2cbe0a519319ebe4a.png)

最终生成的SQL语句是：

```
SELECT * FROM `think_user` WHERE  `name` LIKE 'thinkphp%'
```

`like`查询支持使用数组

```
Db::name('user')->where('name', 'like', ['%think','php%'],'OR')->select();
```

实际生成的SQL语句为：

```
SELECT * FROM `think_user` WHERE  (`name` LIKE '%think' OR `name` LIKE 'php%')
```

为了更加方便，应该直接使用`whereLike`方法

```
Db::name('user')->whereLike('name','thinkphp%')->select();
Db::name('user')->whereNotLike('name','thinkphp%')->select();
```

![78695f7cf6ceb37fed5ee8174c65e905.png](https://s1.imagehub.cc/images/2023/01/27/78695f7cf6ceb37fed5ee8174c65e905.png)![3a6a9949e1a3d5147c7d2f96e0894f61.png](https://s1.imagehub.cc/images/2023/01/27/3a6a9949e1a3d5147c7d2f96e0894f61.png)

#### [NOT] BETWEEN ：同sql的[not] between

查询条件支持字符串或者数组，例如：

```
Db::name('user')->where('id','between','1,8')->select();
```

和下面的等效：

```
Db::name('user')->where('id','between',[1,8])->select();
```

最终生成的SQL语句都是：

```
SELECT * FROM `think_user` WHERE  `id` BETWEEN 1 AND 8
```

或者使用快捷查询方法：

```
Db::name('user')->whereBetween('id','1,8')->select();
Db::name('user')->whereNotBetween('id','1,8')->select();
```

#### [NOT] IN： 同sql的[not] in

查询条件支持字符串或者数组，例如：

```
Db::name('user')->where('id','in','1,5,8')->select();
```

和下面的等效：

```
Db::name('user')->where('id','in',[1,5,8])->select();
```

最终的SQL语句为：

```
SELECT * FROM `think_user` WHERE  `id` IN (1,5,8)
```

或者使用快捷查询方法：

```
Db::name('user')->whereIn('id','1,5,8')->select();
Db::name('user')->whereNotIn('id','1,5,8')->select();
```

> `[NOT] IN`查询支持使用闭包方式

#### [NOT] NULL ：

查询字段是否（不）是`Null`，例如：

```
Db::name('user')->where('name', null)
->where('email','null')
->where('name','not null')
->select();
```

实际生成的SQL语句为：

```
SELECT * FROM `think_user` WHERE  `name` IS NULL  AND `email` IS NULL  AND `name` IS NOT NULL
```

如果你需要查询一个字段的值为字符串`null`或者`not null`，应该使用：

```
Db::name('user')->where('title','=', 'null')
->where('name','=', 'not null')
->select();
```

推荐的方式是使用`whereNull`和`whereNotNull`方法查询。

```
Db::name('user')->whereNull('name')
->whereNull('email')
->whereNotNull('name')
->select();
```

#### EXP：表达式

支持更复杂的查询情况 例如：

```
Db::name('user')->where('id','in','1,3,8')->select();
```

可以改成：

```
Db::name('user')->where('id','exp',' IN (1,3,8) ')->select();
```

`exp`查询的条件不会被当成字符串，所以后面的查询条件可以使用任何SQL支持的语法，包括使用函数和字段名称。

推荐使用`whereExp`方法查询

```
Db::name('user')->whereExp('id', 'IN (1,3,8) ')->select();
```



### 数据库的时间查询

##### 时间比较

> 框架内置了常用的时间查询方法，并且可以自动识别时间字段的类型，所以无论采用什么类型的时间字段，都可以统一使用本章的时间查询用法。

##### 使用`whereTime`方法

`whereTime`方法提供了日期和时间字段的快捷查询，示例如下：

```
// 大于某个时间
Db::name('user')
    ->whereTime('birthday', '>=', '1970-10-1')
    ->select();
// 小于某个时间
Db::name('user')
    ->whereTime('birthday', '<', '2000-10-1')
    ->select();
// 时间区间查询
Db::name('user')
    ->whereTime('birthday', 'between', ['1970-10-1', '2000-10-1'])
    ->select();
// 不在某个时间区间
Db::name('user')
    ->whereTime('birthday', 'not between', ['1970-10-1', '2000-10-1'])
    ->select();
```

还可以使用下面的时间表达式进行时间查询

```
// 查询两个小时内的博客
Db::name('blog')
	->whereTime('create_time','-2 hours')
    ->select();
```

##### 查询某个时间区间

针对时间的区间查询，系统还提供了`whereBetweenTime/whereNotBetweenTime`快捷方法。

```
// 查询2017年上半年注册的用户
Db::name('user')
    ->whereBetweenTime('create_time', '2017-01-01', '2017-06-30')
    ->select();
    
// 查询不是2017年上半年注册的用户
Db::name('user')
    ->whereNotBetweenTime('create_time', '2017-01-01', '2017-06-30')
    ->select();
```

##### 查询某年

查询今年注册的用户

```
Db::name('user')
    ->whereYear('create_time')
    ->select();   
```

查询去年注册的用户

```
Db::name('user')
    ->whereYear('create_time', 'last year')
    ->select();   
```

查询某一年的数据使用

```
// 查询2018年注册的用户
Db::name('user')
    ->whereYear('create_time', '2018')
    ->select();    
```

##### 查询某月

查询本月注册的用户

```
Db::name('user')
    ->whereMonth('create_time')
    ->select();   
```

查询上月注册用户

```
Db::name('user')
    ->whereMonth('create_time','last month')
    ->select();   
```

查询2018年6月注册的用户

```
Db::name('user')
    ->whereMonth('create_time', '2018-06')
    ->select();    
```

##### 查询某周

查询本周数据

```
Db::name('user')
    ->whereWeek('create_time')
    ->select();    
```

查询上周数据

```
Db::name('user')
    ->whereWeek('create_time', 'last week')
    ->select();    
```

查询指定某天开始的一周数据

```
// 查询2019-1-1到2019-1-7的注册用户
Db::name('user')
    ->whereWeek('create_time', '2019-1-1')
    ->select();    
```

##### 查询某天

查询当天注册的用户

```
Db::name('user')
    ->whereDay('create_time')
    ->select();   
```

查询昨天注册的用户

```
Db::name('user')
    ->whereDay('create_time', 'yesterday')
    ->select();   
```

查询某天的数据使用

```
// 查询2018年6月1日注册的用户
Db::name('user')
    ->whereDay('create_time', '2018-06-01')
    ->select();    
```

##### 时间字段区间比较

可以支持对两个时间字段的区间比较

```
// 查询有效期内的活动
Db::name('event')
	->whereBetweenTimeField('start_time', 'end_time')
    ->select();
```

上面的查询相当于

```
// 查询有效期内的活动
Db::name('event')
	->whereTime('start_time', '<=', time())
    ->whereTime('end_time', '>=', time())
    ->select();
```

##### 自定义时间查询规则

你可以通过在数据库配置文件中设置`time_query_rule`添加自定义的时间查询规则，

```
'time_query_rule'    =>    [
    'hour'    =>    ['1 hour ago', 'now'],
],
```





### 聚合原生子查询

在应用中我们经常会用到一些统计数据，例如当前所有（或者满足某些条件）的用户数、所有用户的最大积分、用户的平均成绩等等，ThinkPHP为这些统计操作提供了一系列的内置方法，包括：

| 方法  | 说明                                     |
| :---- | :--------------------------------------- |
| count | 统计数量，参数是要统计的字段名（可选）   |
| max   | 获取最大值，参数是要统计的字段名（必须） |
| min   | 获取最小值，参数是要统计的字段名（必须） |
| avg   | 获取平均值，参数是要统计的字段名（必须） |
| sum   | 获取总分，参数是要统计的字段名（必须）   |

> 聚合方法如果没有数据，默认都是0，聚合查询都可以配合其它查询条件

获取用户数：

```
Db::table('think_user')->count();
```

![dff31a66e543b13d4a0a178910f1e8c1.png](https://s1.imagehub.cc/images/2023/01/27/dff31a66e543b13d4a0a178910f1e8c1.png)

![524a19a79269459aea0594e0cca2941d.png](https://s1.imagehub.cc/images/2023/01/27/524a19a79269459aea0594e0cca2941d.png)

实际生成的SQL语句是：

```
SELECT COUNT(*) AS tp_count FROM `think_user` LIMIT 1
```

或者根据字段统计：

```
Db::table('think_user')->count('id');
```

生成的SQL语句是：

```
SELECT COUNT(id) AS tp_count FROM `think_user` LIMIT 1
```

获取用户的最大积分：

```
Db::table('think_user')->max('score');
```

![a6bceec1b88f518eae149dafbc03f2b3.png](https://s1.imagehub.cc/images/2023/01/27/a6bceec1b88f518eae149dafbc03f2b3.png)![a4fb57689526e59a3b3e7954eddbb6ab.png](https://s1.imagehub.cc/images/2023/01/27/a4fb57689526e59a3b3e7954eddbb6ab.png)

。







生成的SQL语句是：

```
SELECT MAX(score) AS tp_max FROM `think_user` LIMIT 1
```

如果你要获取的最大值不是一个数值，可以使用第二个参数关闭强制转换

```
Db::table('think_user')->max('name',false);
```

获取积分大于0的用户的最小积分：

```
Db::table('think_user')->where('score', '>', 0)->min('score');
```

和max方法一样，min也支持第二个参数用法

```
Db::table('think_user')->where('score', '>', 0)->min('name',false);
```

获取用户的平均积分：

```
Db::table('msg')->avg('user');
```

![4a07df5b914bcc68510d326145786673.png](https://s1.imagehub.cc/images/2023/01/27/4a07df5b914bcc68510d326145786673.png)

生成的SQL语句是：

```
SELECT AVG(score) AS tp_avg FROM `think_user` LIMIT 1
```

统计用户的总成绩：

```
Db::table('think_user')->where('id',10)->sum('score');
```

生成的SQL语句是：

```
SELECT SUM(score) AS tp_sum FROM `think_user` LIMIT 1
```

如果你要使用`group`进行聚合查询，需要自己实现查询，例如：

```
Db::table('score')->field('user_id,SUM(score) AS sum_score')->group('user_id')->select();
```



#### 子查询

首先构造子查询SQL，可以使用下面三种的方式来构建子查询。

##### 使用`fetchSql`方法

fetchSql方法表示不进行查询而只是返回构建的SQL语句，并且不仅仅支持`select`，而是支持所有的CURD查询。

```
$subQuery = Db::table('think_user')
    ->field('id,name')
    ->where('id', '>', 10)
    ->fetchSql(true)
    ->select();
```

![d326ce3abd56149e0c557de89e1b056f.png](https://s1.imagehub.cc/images/2023/01/27/d326ce3abd56149e0c557de89e1b056f.png)![104119bd72ea86f015dbe57c61311958.png](https://s1.imagehub.cc/images/2023/01/27/104119bd72ea86f015dbe57c61311958.png)

生成的subQuery结果为：

```
SELECT `id`,`name` FROM `think_user` WHERE `id` > 10 
```

##### 使用`buildSql`构造子查询

```
$subQuery = Db::table('think_user')
    ->field('id,name')
    ->where('id', '>', 10)
    ->buildSql();
```

生成的subQuery结果为：

```
( SELECT `id`,`name` FROM `think_user` WHERE `id` > 10 )
```

调用`buildSql`方法后不会进行实际的查询操作，而只是生成该次查询的SQL语句（为了避免混淆，会在SQL两边加上括号），然后我们直接在后续的查询中直接调用。

然后使用子查询构造新的查询：

```
Db::table($subQuery . ' a')
    ->where('a.name', 'like', 'thinkphp')
    ->order('id', 'desc')
    ->select();
```

生成的SQL语句为：

```
SELECT * FROM ( SELECT `id`,`name` FROM `think_user` WHERE `id` > 10 ) a WHERE a.name LIKE 'thinkphp' ORDER BY `id` desc
```

##### 使用闭包构造子查询

`IN/NOT IN`和`EXISTS/NOT EXISTS`之类的查询可以直接使用闭包作为子查询，例如：

```
Db::table('think_user')
    ->where('id', 'IN', function ($query) {
        $query->table('think_profile')->where('status', 1)->field('id');
    })
    ->select();
```

生成的SQL语句是

```
SELECT * FROM `think_user` WHERE `id` IN ( SELECT `id` FROM `think_profile` WHERE `status` = 1 )
Db::table('think_user')
    ->whereExists(function ($query) {
        $query->table('think_profile')->where('status', 1);
    })->find();
```

生成的SQL语句为

```
SELECT * FROM `think_user` WHERE EXISTS ( SELECT * FROM `think_profile` WHERE `status` = 1 ) 
```

> 除了上述查询条件外，比较运算也支持使用闭包子查询





#### 原生查询

`Db`类支持原生`SQL`查询操作，主要包括下面两个方法：

> `V6.0.3+`版本开始，原生查询仅支持Db类操作，不支持在模型中调用原生查询方法（包括`query`和`execute`方法）。

##### `query`方法

`query`方法用于执行`SQL`查询操作，返回查询结果数据集（数组）。

![d79f8bf1b8dd9b6746d73d1d4d6008eb.png](https://s1.imagehub.cc/images/2023/01/27/d79f8bf1b8dd9b6746d73d1d4d6008eb.png)

![8faf417cf9822c4d50c7e0ad1aae90eb.png](https://s1.imagehub.cc/images/2023/01/27/8faf417cf9822c4d50c7e0ad1aae90eb.png)

使用示例：

```
Db::query("select * from think_user where status=:id", ['id' => 1]);
```

> 如果你当前采用了分布式数据库，并且设置了读写分离的话，`query`方法默认是在读服务器执行，而不管你的SQL语句是什么。

如果希望从主库读取，可以使用

```
Db::query("select * from think_user where status=:id", ['id' => 1], true);
```

##### `execute`方法

`execute`用于更新和写入数据的sql操作，如果数据非法或者查询错误则返回`false`，否则返回影响的记录数。

![202af87c336160b10995988f271dc985.png](https://s1.imagehub.cc/images/2023/01/27/202af87c336160b10995988f271dc985.png)![cc38c77090c4369f0455bff9b26bae50.png](https://s1.imagehub.cc/images/2023/01/27/cc38c77090c4369f0455bff9b26bae50.png)![a71004ef87f0d3dad2eca01de8f821e6.png](https://s1.imagehub.cc/images/2023/01/27/a71004ef87f0d3dad2eca01de8f821e6.png)

使用示例：

```
Db::execute("update think_user set name='thinkphp' where status=1");
```

> 如果你当前采用了分布式数据库，并且设置了读写分离的话，`execute`方法始终是在写服务器执行，而不管你的SQL语句是什么。

##### 参数绑定

支持在原生查询的时候使用参数绑定，包括问号占位符或者命名占位符，例如：

```
Db::query("select * from think_user where id=? AND status=?", [8, 1]);
// 命名绑定
Db::execute("update think_user set name=:name where status=:status", ['name' => 'thinkphp', 'status' => 1])
```



### 链式查询

使用字符串条件直接查询和操作，例如：

```
Db::table('think_user')->whereRaw('type=1 AND status=1')->select(); 
```

![28ab3f759a40effedea7d4e76353a05d.png](https://s1.imagehub.cc/images/2023/01/27/28ab3f759a40effedea7d4e76353a05d.png)![8ad06334a73954ae1d540bb7ac44de39.png](https://s1.imagehub.cc/images/2023/01/27/8ad06334a73954ae1d540bb7ac44de39.png)

最后生成的SQL语句是

```
SELECT * FROM think_user WHERE type=1 AND status=1
```

> 注意使用字符串查询条件和表达式查询的一个区别在于，不会对查询字段进行避免关键词冲突处理。

使用字符串条件的时候，如果需要传入变量，建议配合预处理机制，确保更加安全，例如：

```
Db::table('think_user')
->whereRaw("id=:id and username=:name", ['id' => 1 , 'name' => 'thinkphp'])
->select();
```

##### field

`field`方法主要作用是标识要返回或者操作的字段，可以用于查询和写入操作。

##### 用于查询

##### 指定字段

在查询操作中`field`方法是使用最频繁的。

```
Db::table('user')->field('id,title,content')->select();
```

![f802931142b015a322bd5c532da1281a.png](https://s1.imagehub.cc/images/2023/01/27/f802931142b015a322bd5c532da1281a.png)

![8cab6ad03a3ca7e9b9bb7b02cd0b2b02.png](https://s1.imagehub.cc/images/2023/01/27/8cab6ad03a3ca7e9b9bb7b02cd0b2b02.png)

这里使用field方法指定了查询的结果集中包含id,title,content三个字段的值。执行的SQL相当于：

```
SELECT id,title,content FROM user
```

可以给某个字段设置别名，例如：

```
Db::table('user')->field('id,nickname as name')->select();
```

执行的SQL语句相当于：

```
SELECT id,nickname as name FROM user
```

##### 使用SQL函数

可以在`fieldRaw`方法中直接使用函数，例如：

```
Db::table('user')->fieldRaw('id,SUM(score)')->select();
```

执行的SQL相当于：

```
SELECT id,SUM(score) FROM user
```

> 除了`select`方法之外，所有的查询方法，包括`find`等都可以使用`field`方法。

##### 使用数组参数

`field`方法的参数可以支持数组，例如：

```
Db::table('user')->field(['id','title','content'])->select();
```

最终执行的SQL和前面用字符串方式是等效的。

数组方式的定义可以为某些字段定义别名，例如：

```
Db::table('user')->field(['id','nickname'=>'name'])->select();
```

执行的SQL相当于：

```
SELECT id,nickname as name FROM user
```

##### 获取所有字段

如果有一个表有非常多的字段，需要获取所有的字段（这个也许很简单，因为不调用field方法或者直接使用空的field方法都能做到）：

```
Db::table('user')->select();
Db::table('user')->field('*')->select();
```

上面的用法是等效的，都相当于执行SQL：

```
SELECT * FROM user
```

但是这并不是我说的获取所有字段，而是显式的调用所有字段（对于对性能要求比较高的系统，这个要求并不过分，起码是一个比较好的习惯），下面的用法可以完成预期的作用：

```
Db::table('user')->field(true)->select();
```

`field(true)`的用法会显式的获取数据表的所有字段列表，哪怕你的数据表有100个字段。

##### 字段排除

如果我希望获取排除数据表中的`content`字段（文本字段的值非常耗内存）之外的所有字段值，我们就可以使用field方法的排除功能，例如下面的方式就可以实现所说的功能：

```
Db::table('user')->withoutField('content')->select();
```

则表示获取除了content之外的所有字段，要排除更多的字段也可以：

```
Db::table('user')->withoutField('user_id,content')->select();
//或者用
Db::table('user')->withoutField(['user_id','content'])->select();
```

> 注意的是 字段排除功能不支持跨表和join操作。

##### 用于写入

除了查询操作之外，`field`方法还有一个非常重要的安全功能--**字段合法性检测**。`field`方法结合数据库的写入方法使用就可以完成表单提交的字段合法性检测，如果我们在表单提交的处理方法中使用了：

```
Db::table('user')->field('title,email,content')->insert($data);
```

即表示表单中的合法字段只有`title`,`email`和`content`字段，无论用户通过什么手段更改或者添加了浏览器的提交字段，都会直接屏蔽。因为，其他所有字段我们都不希望由用户提交来决定，你可以通过自动完成功能定义额外需要自动写入的字段。

> 在开启数据表字段严格检查的情况下，提交了非法字段会抛出异常，可以在数据库设置文件中设置：

> ```
> // 关闭严格字段检查
> 'fields_strict'	=>	false,
> ```



#### 事务操作

> `MySQL` 的 `MyISAM` 不支持事务处理，需要使用 `InnoDB` 引擎。

场景，当我们执行了

~~~php
    public function things()
    {
        Db::table(ty_user)->where('id=1')->find(1);
        Db::table(ty_user1)->where('id=1')->delete(1);
    }
~~~

这里我们的第二个ty_user写错了，那么只会执行第一行操作，这样是不安全的，如下可处理

原生模式

~~~php
Db::transaction(function () {
    Db::table('think_user')->find(1);
    Db::table('think_user')->delete(1);
});
~~~

手动模式

~~~php
// 启动事务
Db::startTrans();
try {
    Db::table('think_user')->find(1);
    Db::table('think_user')->delete(1);
    // 提交事务
    Db::commit();
} catch (\Exception $e) {
    // 回滚事务
    Db::rollback();
}
~~~



#### 数据库的数据集和代码提示

代码提示

将tp5的db.php中的提示拿到tp6的DbManager.php中

[![ppCvNcQ.png](https://s1.ax1x.com/2023/02/28/ppCvNcQ.png)](https://imgse.com/i/ppCvNcQ)[![ppCvUXj.png](https://s1.ax1x.com/2023/02/28/ppCvUXj.png)](https://imgse.com/i/ppCvUXj)

~~~php
/**
 * Class DbManager
 * @package think
 * @mixin BaseQuery
 * @mixin Query
 * @method Query table(string $table) static 指定数据表（含前缀）
 * @method Query name(string $name) static 指定数据表（不含前缀）
 * @method Query where(mixed $field, string $op = null, mixed $condition = null) static 查询条件
 * @method Query join(mixed $join, mixed $condition = null, string $type = 'INNER') static JOIN查询
 * @method Query union(mixed $union, boolean $all = false) static UNION查询
 * @method Query limit(mixed $offset, integer $length = null) static 查询LIMIT
 * @method Query order(mixed $field, string $order = null) static 查询ORDER
 * @method Query cache(mixed $key = null , integer $expire = null) static 设置查询缓存
 * @method mixed value(string $field) static 获取某个字段的值
 * @method array column(string $field, string $key = '') static 获取某个列的值
 * @method Query view(mixed $join, mixed $field = null, mixed $on = null, string $type = 'INNER') static 视图查询
 * @method mixed find(mixed $data = null) static 查询单个记录
 * @method mixed select(mixed $data = null) static 查询多个记录
 * @method integer insert(array $data, boolean $replace = false, boolean $getLastInsID = false, string $sequence = null) static 插入一条记录
 * @method integer insertGetId(array $data, boolean $replace = false, string $sequence = null) static 插入一条记录并返回自增ID
 * @method integer insertAll(array $dataSet) static 插入多条记录
 * @method integer update(array $data) static 更新记录
 * @method integer delete(mixed $data = null) static 删除记录
 * @method boolean chunk(integer $count, callable $callback, string $column = null) static 分块获取数据
 * @method mixed query(string $sql, array $bind = [], boolean $master = false, bool $pdo = false) static SQL查询
 * @method integer execute(string $sql, array $bind = [], boolean $fetch = false, boolean $getLastInsID = false, string $sequence = null) static SQL执行
 * @method Paginator paginate(integer $listRows = 15, mixed $simple = null, array $config = []) static 分页查询
 * @method mixed transaction(callable $callback) static 执行数据库事务
 * @method void startTrans() static 启动事务
 * @method void commit() static 用于非自动提交状态下面的查询提交
 * @method void rollback() static 事务回滚
 * @method boolean batchQuery(array $sqlArray) static 批处理执行SQL语句
 * @method string quote(string $str) static SQL指令安全过滤
 * @method string getLastInsID($sequence = null) static 获取最近插入的ID
 */
~~~

##### 数据集

> 模型的`select`查询方法返回数据集对象 `think\model\Collection`，该对象继承自`think\Collection`，因此具有数据库的[数据集类](https://www.kancloud.cn/manual/thinkphp6_0/1037576)的所有方法，而且还提供了额外的模型操作方法

~~~
转换为纯数组    
    public function things()
    {
        $user = Db::table('ty_user')->select();
        return dump($user->toArray());
    }
~~~

[![ppCxK8U.png](https://s1.ax1x.com/2023/02/28/ppCxK8U.png)](https://imgse.com/i/ppCxK8U)

~~~
打乱
    public function things()
    {
        $user = Db::table('ty_user')->select();
        return dump($user->shuffle());
    }
~~~

![](https://s3.bmp.ovh/imgs/2023/02/28/9c5ac8448eea5434.png)![](https://s3.bmp.ovh/imgs/2023/02/28/a804696ee1f18893.png)

~~~
where 
    public function things()
    {
        $user = Db::table('ty_user')->select();
        return dump($user->whereIn('id',[1,5,6]));
    }
~~~

![](https://s3.bmp.ovh/imgs/2023/02/28/a0e6cc1b41001c78.png)

![](https://s3.bmp.ovh/imgs/2023/02/28/2500edf99abc88e6.png)

### 模型

![e8838ae25d8093745dc45f0e6d67d287.png](https://s1.imagehub.cc/images/2023/01/27/e8838ae25d8093745dc45f0e6d67d287.png)![cb65be5da60198d80069ee020064fd08.png](https://s1.imagehub.cc/images/2023/01/27/cb65be5da60198d80069ee020064fd08.png)



###### 起别名

~~~php
use app\model\ty_user as test123;

    public function test12()
    {
        return json(test123::select());
    }
~~~



这里模型下的文件名和表名是一样的

定义一个模型类很简单，例如下面是一个`User`模型：

```php
<?php
namespace app\model;

use think\Model;

class User extends Model
{
}
```

> 请确保你已经在数据库配置文件中配置了数据库连接信息，如不清楚请参考数据库一章

模型会自动对应数据表，模型类的命名规则是除去表前缀的数据表名称，采用驼峰法命名，并且首字母大写

![a15240644ff66a31571144fb2092c8f2.png](https://s1.imagehub.cc/images/2023/01/27/a15240644ff66a31571144fb2092c8f2.png)![e5d9daa1acc8e1c1feaa3c5aeae9b124.png](https://s1.imagehub.cc/images/2023/01/27/e5d9daa1acc8e1c1feaa3c5aeae9b124.png)

[![909e69f245a4c0cc7e71decbfc6aec57.png](https://s1.imagehub.cc/images/2023/01/27/909e69f245a4c0cc7e71decbfc6aec57.png)](https://www.imagehub.cc/image/OFxiO)

#### 模型设置

默认主键为`id`，如果你没有使用`id`作为主键名，需要在模型中设置属性：

```php
<?php
namespace app\model;

use think\Model;

class User extends Model
{
    protected $pk = 'uid';
}
```

如果你想指定数据表甚至数据库连接的话，可以使用：

```php
<?php
namespace app\model;

use think\Model;

class User extends Model
{
    // 设置当前模型对应的完整数据表名称
    protected $table = 'think_user';
    
    // 设置当前模型的数据库连接
    protected $connection = 'db_config';
}
```

> `connection`属性使用用配置参数名（需要在数据库配置文件中的`connections`参数中添加对应标识）。

常用的模型设置属性包括（以下属性都不是必须设置）：

| 属性       | 描述                                                       |
| :--------- | :--------------------------------------------------------- |
| name       | 模型名（相当于不带数据表前后缀的表名，默认为当前模型类名） |
| table      | 数据表名（默认自动获取）                                   |
| suffix     | 数据表后缀（默认为空）                                     |
| pk         | 主键名（默认为`id`）                                       |
| connection | 数据库连接（默认读取数据库配置）                           |
| query      | 模型使用的查询类名称                                       |
| field      | 模型允许写入的字段列表（数组）                             |
| schema     | 模型对应数据表字段及类型                                   |
| type       | 模型需要自动转换的字段及类型                               |
| strict     | 是否严格区分字段大小写（默认为true）                       |
| disuse     | 数据表废弃字段（数组）                                     |

> 模型不支持对数据表的前缀单独设置，并且也不推荐使用数据表的前缀设计，应该用不同的库区分。当你的数据表没有前缀的时候，`name`和`table`属性的定义是没有区别的，定义任何一个即可。

##### 新增

模型数据的新增和数据库的新增数据有所区别，数据库的新增只是单纯的写入给定的数据，而模型的数据写入会包含修改器、自动完成以及模型事件等环节，数据库的数据写入参考数据库章节。

###### 添加一条数据

第一种是实例化模型对象后赋值并保存：

```
$user           = new User;
$user->name     = 'thinkphp';
$user->email    = 'thinkphp@qq.com';
$user->save();
```

> `save`方法成功会返回`true`，并且只有当`before_insert`事件返回`false`的时候返回`false`，一旦有错误就会抛出异常。所以无需判断返回类型。

![0e03bce250369c338a3814f3b4b6040a.png](https://s1.imagehub.cc/images/2023/01/28/0e03bce250369c338a3814f3b4b6040a.png)

![e754b403bb8f7ea78cb1ccf983477bd4.png](https://s1.imagehub.cc/images/2023/01/28/e754b403bb8f7ea78cb1ccf983477bd4.png)







也可以直接传入数据到`save`方法批量赋值：

```
$user = new User;
$user->save([
    'name'  =>  'thinkphp',
    'email' =>  'thinkphp@qq.com'
]);
```

默认只会写入数据表已有的字段，如果你通过外部提交赋值给模型，并且希望指定某些字段写入，可以使用：

```
$user = new User;
// post数组中只有name和email字段会写入
$user->allowField(['name','email'])->save($_POST);
```

最佳的建议是模型数据赋值之前就进行数据过滤，例如：

```
$user = new User;
// 过滤post数组中的非数据表字段数据
$data = Request::only(['name','email']);
$user->save($data);
```

> `save`方法新增数据返回的是写入的记录数（通常是`1`），而不是自增主键值。

###### `Replace`写入

`save`方法可以支持`replace`写入。

```
$user           = new User;
$user->name     = 'thinkphp';
$user->email    = 'thinkphp@qq.com';
$user->replace()->save();
```

###### 获取自增ID

如果要获取新增数据的自增ID，可以使用下面的方式：

```
$user           = new User;
$user->name     = 'thinkphp';
$user->email    = 'thinkphp@qq.com';
$user->save();
// 获取自增ID
echo $user->id;
```

这里其实是获取模型的主键，如果你的主键不是`id`，而是`user_id`的话，其实获取自增ID就变成这样：

```
$user           = new User;
$user->name     = 'thinkphp';
$user->email    = 'thinkphp@qq.com';
$user->save();
// 获取自增ID
echo $user->user_id;
```

> 不要在同一个实例里面多次新增数据，如果确实需要多次新增，可以使用后面的静态方法处理。

###### 批量增加数据

支持批量新增，可以使用：

```
$user = new User;
$list = [
    ['name'=>'thinkphp','email'=>'thinkphp@qq.com'],
    ['name'=>'onethink','email'=>'onethink@qq.com']
];
$user->saveAll($list);
```

> saveAll方法新增数据返回的是包含新增模型（带自增ID）的数据集对象。

`saveAll`方法新增数据默认会自动识别数据是需要新增还是更新操作，当数据中存在主键的时候会认为是更新操作。

###### 静态方法

还可以直接静态调用`create`方法创建并写入：

```
$user = User::create([
    'name'  =>  'thinkphp',
    'email' =>  'thinkphp@qq.com'
]);
echo $user->name;
echo $user->email;
echo $user->id; // 获取自增ID
```

> 和`save`方法不同的是，`create`方法返回的是当前模型的对象实例。

`create`方法默认会过滤不是数据表的字段信息，可以在第二个参数可以传入允许写入的字段列表，例如：

```
// 只允许写入name和email字段的数据
$user = User::create([
    'name'  =>  'thinkphp',
    'email' =>  'thinkphp@qq.com'
], ['name', 'email']);
echo $user->name;
echo $user->email;
echo $user->id; // 获取自增ID
```

支持`replace`操作，使用下面的方法：

```
$user = User::create([
    'name'  =>  'thinkphp',
    'email' =>  'thinkphp@qq.com'
], ['name','email'], true);
```







#### 模型初始化

模型支持初始化，只需要定义`init`方法，例如：

```
<?php
namespace app\model;

use think\Model;

class User extends Model
{
    // 模型初始化
    protected static function init()
    {
        //TODO:初始化内容
    }
}
```

> `init`必须是静态方法，并且只在第一次实例化的时候执行，并且只会执行一次

#### 模型操作

> 在模型中除了可以调用数据库类的方法之外（换句话说，**数据库的所有查询构造器方法模型中都可以支持**），可以定义自己的方法，所以也可以把模型看成是数据库的增强版。

模型的操作方法无需和数据库查询一样调用必须首先调用`table`或者`name`方法，因为模型会按照规则自动匹配对应的数据表，例如：

```
Db::name('user')->where('id','>',10)->select();
```

改成模型操作的话就变成

```
User::where('id','>',10)->select();
```

虽然看起来是相同的查询条件，但一个最明显的区别是**查询结果的类型**不同。第一种方式的查询结果是一个（二维）数组，而第二种方式的查询结果是包含了模型（集合）的数据集。不过，在大多数情况下，这二种返回类型的使用方式并无明显区别。

模型操作和数据库操作的另外一个显著区别是模型支持包括获取器、修改器、自动时间写入在内的一系列自动化操作和事件，简化了数据的存取操作，但随之而来的是性能有所下降（其实并没下降，而是自动帮你处理了一些原本需要手动处理的操作），后面会逐步领略到模型的这些特色功能。

#### 动态切换后缀

新版模型增加了一个数据表后缀属性，可以用于多语言或者数据分表的模型查询，省去为多个相同结构的表定义多个模型的麻烦。

默认的数据表后缀可以在模型类里面直接定义`suffix`属性。

```
<?php
namespace app\model;

use think\Model;

class Blog extends Model
{
    // 定义默认的表后缀（默认查询中文数据）
    protected $suffix = _cn';
}
```

> 你在模型里面定义的`name`和`table`属性无需包含后缀定义

模型提供了动态切换方法`suffix`和`setSuffix`，例如：

```
// suffix方法用于静态查询
$blog = Blog::suffix('_en')->find();
$blog->name = 'test';
$blog->save();

// setSuffix用于动态设置
$blog = new Blog($data);
$blog->setSuffix('_en')->save();
```

#### 模型方法依赖注入

自动绑定自动实例化

如果你需要对模型的方法支持依赖注入，可以把模型的方法改成闭包的方式，例如，你需要对获取器方法增加依赖注入

```
public function getTestFieldAttr($value,$data) {
    return $this->invoke(function(Request $request)  use($value,$data) {
        return $data['name'] . $request->action();
    });
}
```

不仅仅是获取器方法，在任何需要依赖注入的方法都可以改造为调用`invoke`方法的方式，`invoke`方法第二个参数用于传入需要调用的（数组）参数。

如果你需要直接调用某个已经定义的模型方法（假设已经使用了依赖注入），可以使用

```
protected function bar($name, Request $request) {
    // ...
}

protected function invokeCall(){
    return $this->invoke('bar',['think']);
}
```

#### 获取器和修改器

~~~php
    public function getAttr()
    {
        $user = UserModel::find(10);
//        return $user->username;
        $user->username;
    }
~~~

~~~php
    public function getUsernameAttr($username)
    {
        echo $username;
    }
~~~

![](https://s3.bmp.ovh/imgs/2023/02/28/420ea97d7050fbcf.png)

.

#### 查询范围

~~~php
<?php
namespace app\model;
use think\Model;

class User extends Model
{
	public function scopeEmail($query, $email)
    {
    	$query->where('email', 'like', '%' . $email . '%');
    }
    public function scopeScore($query, $score)
    {
    	$query->where('score', '>', $score);
    }   
}
~~~

##### 全局查询范围

支持在模型里面设置`globalScope`属性，定义全局的查询范围

~~~php
<?php
namespace app\model;
use think\Model;

class User extends Model
{
    // 定义全局的查询范围
    protected $globalScope = ['status'];
    public function scopeStatus($query)
    {
        $query->where('status',1);
    }
}
~~~

##### 搜索器

搜索器的作用是用于封装字段（或者搜索标识）的查询条件表达式，一个搜索器对应一个特殊的方法（该方法必须是`public`类型），方法命名规范为：

~~~php
<?php
namespace app\model;
use think\Model;
class User extends Model 
{
    public function searchNameAttr($query, $value, $data)
    {
        $query->where('name','like', $value . '%');
    }
    public function searchCreateTimeAttr($query, $value, $data)
    {
        $query->whereBetweenTime('create_time', $value[0], $value[1]);
    }    
}
~~~

##### 自动时间戳

![](https://s3.bmp.ovh/imgs/2023/02/28/fa2afe02167ebfbc.png)

##### JSON字段



xxx



#### 模型和数据库的事件

初始化

~~~php
class DataTest extends BaseController
{
    public function index()
    {
        return "456";

    }
~~~

~~~php
    public function initialize()
    {
        echo 123;
    }
~~~

![](https://s3.bmp.ovh/imgs/2023/02/28/ad50d45b2dd9015e.png)

.

##### 数据库事件

数据库操作的回调也称为查询事件，是针对数据库的CURD操作而设计的回调方法，主要包括：

| 事件          | 描述                   |
| :------------ | :--------------------- |
| before_select | `select`查询前回调     |
| before_find   | `find`查询前回调       |
| after_insert  | `insert`操作成功后回调 |
| after_update  | `update`操作成功后回调 |
| after_delete  | `delete`操作成功后回调 |



































### 路由

要使用`Route`类注册路由必须首先在路由定义文件开头添加引用（后面不再重复说明）

```
use think\facade\Route;
```



~~~http
配置文件 config/route.php
定义文件 route/app.php
~~~

##### 注册路由

最基础的路由定义方法是：

> Route::rule('路由表达式', '路由地址', '请求类型');

例如注册如下路由规则（假设为单应用模式）：

```
// 注册路由到News控制器的read操作
Route::rule('new/:id','News/read');
```

![](https://s3.bmp.ovh/imgs/2023/01/28/c9d6533476854071.png)

![](https://s3.bmp.ovh/imgs/2023/01/28/d6593151db188724.png)

我们访问：

```
http://localhost:8000/index.php/address/details/id/1.html
http://localhost:8000/index.php/address/details/id/1
http://localhost:8000/index.php/address/details?id=1
```

会自动路由到：

```
http://localhost:8000/index.php/address/details/id/1
```

![](https://s3.bmp.ovh/imgs/2023/01/28/297064fe42ee9423.png)![](https://s3.bmp.ovh/imgs/2023/01/28/e7aa64dad232c2d1.png)



~~~http
http://localhost:8000/think
~~~

![](https://s3.bmp.ovh/imgs/2023/01/29/05f5762910364bf4.png)![](https://s3.bmp.ovh/imgs/2023/01/29/09066e2db85928b1.png)



##### 闭包

~~~php
Route::get('think', function () {
    return 'hello,ThinkPHP6!';
});
~~~

[![ppi5T1I.png](https://s1.ax1x.com/2023/03/01/ppi5T1I.png)](https://imgse.com/i/ppi5T1I)

~~~php
Route::get('think2', function ($name) {
    return 'hello '.$name;
});
//http://localhost:8000/think2/name/123
~~~

[![ppi5qnf.png](https://s1.ax1x.com/2023/03/01/ppi5qnf.png)](https://imgse.com/i/ppi5qnf)

~~~php
Route::rule('sc/:name/:uid', 'datatest/hello2', 'POST|GET')->pattern(['id'=>'\d+', 'uid'=>'\d+']);
~~~

~~~php
    public function hello2($name, $uid)
    {
        return "名字name是: ".$name."uid是: ".$uid;
    }
~~~

[![ppi46eS.png](https://s1.ax1x.com/2023/03/01/ppi46eS.png)](https://imgse.com/i/ppi46eS)

.

~~~php
Route::pattern(['id'=>'\d+', 'uid'=>'\d+']);
Route::rule('sc/:name/:uid', 'datatest/hello2');
~~~

[![ppi4bo4.png](https://s1.ax1x.com/2023/03/01/ppi4bo4.png)](https://imgse.com/i/ppi4bo4)

.

~~~php
Route::rule('sc-:name-:uid', 'datatest/hello2', 'POST|GET');
~~~

[![ppi59eO.png](https://s1.ax1x.com/2023/03/01/ppi59eO.png)](https://imgse.com/i/ppi59eO)

~~~php
Route::rule('ids_:name', 'datatest/hello', 'POST|GET');
~~~

[![ppi5e6P.png](https://s1.ax1x.com/2023/03/01/ppi5e6P.png)](https://imgse.com/i/ppi5e6P)

.

~~~php
Route::rule('ids-<name>', 'datatest/hello', 'POST|GET');
~~~

[![ppi5MTg.png](https://s1.ax1x.com/2023/03/01/ppi5MTg.png)](https://imgse.com/i/ppi5MTg)

.

~~~php
Route::rule('ids-<test>-<name>', 'data:test/hello', 'POST|GET');
~~~

这里的<test>就是我们控制器后半部分不知道的通过方式传入

[![ppi50k4.png](https://s1.ax1x.com/2023/03/01/ppi50k4.png)](https://imgse.com/i/ppi50k4)









静态方法和动态方法都可以

~~~php
    public function hello($name)
    {
        return '123: '. $name;
    }
    public static function hello($name)
    {
        return '123: '. $name;
    }
~~~

完整形式和缩写形式

~~~php
//缩写
Route::get('hello/:name', 'index/hello');
//完整形式

~~~

[![ppFlKzT.png](https://s1.ax1x.com/2023/03/02/ppFlKzT.png)](https://imgse.com/i/ppFlKzT)

~~~php
http://localhost:8000/hello/123
~~~

[![ppFlmiq.png](https://s1.ax1x.com/2023/03/02/ppFlmiq.png)](https://imgse.com/i/ppFlmiq)



或者

~~~php
Route::get('hello/:name', 'app\controller\index@hello');
~~~

[![ppFl8eJ.png](https://s1.ax1x.com/2023/03/02/ppFl8eJ.png)](https://imgse.com/i/ppFl8eJ)

重定向

```json
Route::get('hello/:name', 'http://localhost:8000/hello/123');
```

后缀检测

~~~php
Route::get('hello/:name', 'app/controller/index/hello')->ext('ht');
~~~

[![ppFlUW6.png](https://s1.ax1x.com/2023/03/02/ppFlUW6.png)](https://imgse.com/i/ppFlUW6)



分组路由

~~~php
Route::group(function(){
    Route::rule('sc-:name', 'datatest/hello', 'POST|GET');
    Route::rule('dc-:name-:uid', 'datatest/hello2', 'POST|GET');

});
~~~

[![ppF3Z80.png](https://s1.ax1x.com/2023/03/02/ppF3Z80.png)[![ppF3e2V.png](https://s1.ax1x.com/2023/03/02/ppF3e2V.png)](https://imgse.com/i/ppF3e2V)

全局Miss路由

~~~php
Route::miss('public/miss');
~~~

[![ppF8SiR.png](https://s1.ax1x.com/2023/03/02/ppF8SiR.png)](https://imgse.com/i/ppF8SiR)

[![ppF3xo9.png](https://s1.ax1x.com/2023/03/02/ppF3xo9.png)](https://imgse.com/i/ppF3xo9)

[![ppF89Rx.png](https://s1.ax1x.com/2023/03/02/ppF89Rx.png)](https://imgse.com/i/ppF89Rx)

分组miss路由

~~~php
Route::group(function(){
    Route::rule('sc-:name', 'datatest/hello', 'POST|GET');
    Route::rule('dc-:name-:uid', 'datatest/hello2', 'POST|GET');
    Route::miss('miss');

});
~~~

[![ppF8FsO.png](https://s1.ax1x.com/2023/03/02/ppF8FsO.png)](https://imgse.com/i/ppF8FsO)

并且原来的访问地址会自动失效。

可以在`rule`方法中指定请求类型（不指定的话默认为任何请求类型有效），例如：

```
Route::rule('new/:id', 'News/update', 'POST');
```

> 请求类型参数不区分大小写。

表示定义的路由规则在`POST`请求下才有效。如果要定义`GET`和`POST`请求支持的路由规则，可以用：

```
Route::rule('new/:id','News/read','GET|POST');
```

不过通常我们更推荐使用对应请求类型的快捷方法，包括：

| 类型   | 描述         | 快捷方法            |
| :----- | :----------- | :------------------ |
| GET    | GET请求      | get                 |
| POST   | POST请求     | post                |
| PUT    | PUT请求      | put                 |
| DELETE | DELETE请求   | delete              |
| PATCH  | PATCH请求    | patch               |
| HEAD   | HEAD请求     | head （`V6.0.13+`） |
| *      | 任何请求类型 | any                 |

快捷注册方法的用法为：

> Route::快捷方法名('路由表达式', '路由地址');

使用示例如下：

```
Route::get('new/<id>','News/read'); // 定义GET请求路由规则
Route::post('new/<id>','News/update'); // 定义POST请求路由规则
Route::put('new/:id','News/update'); // 定义PUT请求路由规则
Route::delete('new/:id','News/delete'); // 定义DELETE请求路由规则
Route::any('new/:id','News/read'); // 所有请求都支持的路由规则
```

注册多个路由规则后，系统会依次遍历注册过的满足请求类型的路由规则，一旦匹配到正确的路由规则后则开始执行最终的调度方法，后续规则就不再检测。

##### 规则表达式

规则表达式通常包含静态规则和动态规则，以及两种规则的结合，例如下面都属于有效的规则表达式：

```
Route::rule('/', 'index'); // 首页访问路由
Route::rule('my', 'Member/myinfo'); // 静态地址路由
Route::rule('blog/:id', 'Blog/read'); // 静态地址和动态地址结合
Route::rule('new/:year/:month/:day', 'News/read'); // 静态地址和动态地址结合
Route::rule(':user/:blog_id', 'Blog/read'); // 全动态地址
```

> 规则表达式的定义以`/`为参数分割符（无论你的`PATH_INFO`分隔符设置是什么，请确保在定义路由规则表达式的时候统一使用`/`进行URL参数分割，除非是使用组合变量的情况）。

修改首页

```
Route::rule('/','test/index');
```

![](https://s3.bmp.ovh/imgs/2023/01/28/1b94a43997e068cc.png)![](https://s3.bmp.ovh/imgs/2023/01/28/8fe288b39f4d31e0.png)![](https://s3.bmp.ovh/imgs/2023/01/28/23469eaa618f1c43.png)

每个参数中可以包括动态变量，例如`:变量`或者`<变量>`都表示动态变量（新版推荐使用第二种方式，更利于混合变量定义），并且会自动绑定到操作方法的对应参数。

> 你的URL访问`PATH_INFO`分隔符使用`pathinfo_depr`配置，但无论如何配置，都不影响路由的规则表达式的路由分隔符定义。

```
public function url()
{
    return url('Address/details',['id'=>5]);
}
```

注解函数

![](https://s3.bmp.ovh/imgs/2023/01/28/0dbd6cc1726ab3e7.png)![](https://s3.bmp.ovh/imgs/2023/01/28/06a19844a172e54e.png)![](https://s3.bmp.ovh/imgs/2023/01/28/88137c601e6bb2a1.png)![](https://s3.bmp.ovh/imgs/2023/01/28/7b6b1458745f58a8.png)



##### 可选变量

支持对路由参数的可选定义，例如：



```
Route::get('blog/:year/[:month]','Blog/archive');
// 或者
Route::get('blog/<year>/<month?>','Blog/archive');
```

变量用`[ ]`包含起来后就表示该变量是路由匹配的可选变量。

以上定义路由规则后，下面的URL访问地址都可以被正确的路由匹配：

```
http://serverName/index.php/blog/2015
http://serverName/index.php/blog/2015/12
```

采用可选变量定义后，之前需要定义两个或者多个路由规则才能处理的情况可以合并为一个路由规则。

> 可选参数只能放到路由规则的最后，如果在中间使用了可选参数的话，后面的变量都会变成可选参数。

##### 完全匹配

规则匹配检测的时候默认只是对URL从头开始匹配，只要URL地址开头包含了定义的路由规则就会匹配成功，如果希望URL进行完全匹配，可以在路由表达式最后使用`$`符号，例如：

```
Route::get('new/:cate$', 'News/category');
```

这样定义后

```
http://serverName/index.php/new/info
```

会匹配成功,而

```
http://serverName/index.php/new/info/2
```

则不会匹配成功。

如果是采用

```
Route::get('new/:cate', 'News/category');
```

方式定义的话，则两种方式的URL访问都可以匹配成功。

如果需要全局进行URL完全匹配，可以在路由配置文件中设置

```
// 开启路由完全匹配
'route_complete_match'   => true,
```

开启全局完全匹配后，如果需要对某个路由关闭完全匹配，可以使用

```
Route::get('new/:cate', 'News/category')->completeMatch(false);
```

##### 额外参数

在路由跳转的时候支持额外传入参数对（额外参数指的是不在URL里面的参数，隐式传入需要的操作中，有时候能够起到一定的安全防护作用，后面我们会提到）。例如：

```
Route::get('blog/:id','blog/read')
    ->append(['status' => 1, 'app_id' =>5]);
```

上面的路由规则定义中`status`和`app_id`参数都是URL里面不存在的，属于隐式传值。可以针对不同的路由设置不同的额外参数。

> 如果`append`方法中的变量和路由规则存在冲突的话，append方法传入的优先。

##### 路由标识

如果你需要快速的根据路由生成URL地址，可以在定义路由的时候指定生成标识（但要确保唯一）。

例如

```
// 注册路由到News控制器的read操作
Route::rule('new/:id','News/read')
    ->name('new_read');
```

生成路由地址的时候就可以使用

```
url('new_read', ['id' => 10]);
```

如果不定义路由标识的话，系统会默认使用路由地址作为路由标识，例如可以使用下面的方式生成

```
url('News/read', ['id' => 10]);
```

##### 强制路由

在路由配置文件中设置

```
'url_route_must'		=>  true,
```

将开启强制使用路由，这种方式下面必须严格给每一个访问地址定义路由规则（**包括首页**），否则将抛出异常。

首页的路由规则采用`/`定义即可，例如下面把网站首页路由输出`Hello,world!`

```
Route::get('/', function () {
    return 'Hello,world!';
});
```









路由地址表示定义的路由表达式最终需要路由到的实际地址（或者响应对象）以及一些需要的额外参数，支持下面几种方式定义：

##### 路由到控制器/操作

这是最常用的一种路由方式，把满足条件的路由规则路由到相关的控制器和操作，然后由系统调度执行相关的操作，格式为：

> ###### 控制器/操作

解析规则是从操作开始解析，然后解析控制器，例如：

```
// 路由到blog控制器
Route::get('blog/:id','Blog/read');
```

Blog类定义如下：

```
<?php
namespace app\index\controller;

class Blog
{
    public function read($id)
    {
        return 'read:' . $id;
    }
}
```

路由地址中支持多级控制器，使用下面的方式进行设置：

```
Route::get('blog/:id','group.Blog/read');
```

表示路由到下面的控制器类，

```
index/controller/group/Blog
```

还可以支持路由到动态的应用、控制器或者操作，例如：

```
// action变量的值作为操作方法传入
Route::get(':action/blog/:id', 'Blog/:action');
```

#### 路由到类的方法

这种方式的路由可以支持执行任何类的方法，而不局限于执行控制器的操作方法。

路由地址的格式为（动态方法）：

> ##### \完整类名@方法名

或者（静态方法）

> ##### \完整类名::方法名

例如：

```
Route::get('blog/:id','\app\index\service\Blog@read');
```

执行的是 `\app\index\service\Blog`类的`read`方法。
也支持执行某个静态方法，例如：

```
Route::get('blog/:id','\app\index\service\Blog::read');
```

#### 重定向路由

可以直接使用`redirect`方法注册一个重定向路由

```php
Route::redirect('blog/:id', 'http://blog.thinkphp.cn/read/:id', 302);
```

#### 路由到模板

支持路由直接渲染模板输出。

```
// 路由到模板文件
Route::view('hello/:name', 'index/hello');
```

表示该路由会渲染当前应用下面的`view/index/hello.html`模板文件输出。

模板文件中可以直接输出当前请求的`param`变量，如果需要增加额外的模板变量，可以使用：

```
Route::view('hello/:name', 'index/hello', ['city'=>'shanghai']);
```

在模板中可以输出`name`和`city`两个变量。

```
Hello,{$name}--{$city}！
```

#### 路由到闭包

我们可以使用闭包的方式定义一些特殊需求的路由，而不需要执行控制器的操作方法了，例如：

```
Route::get('hello', function () {
    return 'hello,world!';
});
```

可以通过闭包的方式支持路由自定义响应输出，例如：

```
Route::get('hello/:name', function () {
    response()->data('Hello,ThinkPHP')
    ->code(200)
    ->contentType('text/plain');
});
```

#### 参数传递

闭包定义的时候支持参数传递，例如：

```
Route::get('hello/:name', function ($name) {
    return 'Hello,' . $name;
});
```

规则路由中定义的动态变量的名称 就是闭包函数中的参数名称，不分次序。

因此，如果我们访问的URL地址是：

```
http://serverName/hello/thinkphp
```

则浏览器输出的结果是：

```
Hello,thinkphp
```

#### 依赖注入

~~~php
class Inject
{
    protected $one;
    public function __construct(One $one)
    {
        $this->one = $one;
    }
    public function index()
    {
        return $this->one->username;
    }
}
~~~

~~~php
class One extends Model
{
    public $username = "mr.Lee";
}
~~~



可以在闭包中使用依赖注入，例如：

```
Route::rule('hello/:name', function (Request $request, $name) {
    $method = $request->method();
    return '[' . $method . '] Hello,' . $name;
});
```

![](https://s3.bmp.ovh/imgs/2023/01/31/88eb4776e941b2b5.png)![](https://s3.bmp.ovh/imgs/2023/01/31/8e8b6d7333e3c3a6.png)![](https://s3.bmp.ovh/imgs/2023/01/31/524e586d6cdeb327.png)

#### 路由到调度对象（`V6.0.3+`）

`V6.0.3+`版本开始，可以支持路由到一个自定义的路由调度对象。

```
// 路由到自定义调度对象
Route::get('blog/:id',\app\route\BlogDispatch::class);
namespace app\route;

use think\route\Dispatch;
use think\route\Rule;
use think\Request;

class BlogDispatch extends Dispatch
{
    public function exec()
    {
        // 自定义路由调度
    }
}
```

具体调度类的实现可以参考内置的几个调度类的实现。

#### 路由参数

路由分组及规则定义支持指定路由参数，这些参数主要完成路由匹配检测以及后续行为。

> 路由参数可以在定义路由规则的时候直接传入（批量），推荐使用方法配置更加清晰。

| 参数           | 说明                              | 方法名        |
| :------------- | :-------------------------------- | :------------ |
| ext            | URL后缀检测，支持匹配多个后缀     | ext           |
| deny_ext       | URL禁止后缀检测，支持匹配多个后缀 | denyExt       |
| https          | 检测是否https请求                 | https         |
| domain         | 域名检测                          | domain        |
| complete_match | 是否完整匹配路由                  | completeMatch |
| model          | 绑定模型                          | model         |
| cache          | 请求缓存                          | cache         |
| ajax           | Ajax检测                          | ajax          |
| pjax           | Pjax检测                          | pjax          |
| json           | JSON检测                          | json          |
| validate       | 绑定验证器类进行数据验证          | validate      |
| append         | 追加额外的参数                    | append        |
| middleware     | 注册路由中间件                    | middleware    |
| filter         | 请求变量过滤                      | filter        |
| match          | 路由闭包检测（`V6.0.12+`）        | match         |

#### 伪静态

[![ppFyG0s.png](https://s1.ax1x.com/2023/03/02/ppFyG0s.png)](https://imgse.com/i/ppFyG0s)

~~~php
    'url_html_suffix'       => 'html|htm',
~~~

#### 预处理 + 参数绑定（防SQL）





用法举例：

```
Route::get('new/:id', 'News/read')
    ->ext('html')
    ->https();
```

> 这些路由参数可以混合使用，只要有任何一条参数检查不通过，当前路由就不会生效，继续检测后面的路由规则。

如果你需要批量设置路由参数，也可以使用`option`方法。

```
Route::get('new/:id', 'News/read')
    ->option([
        'ext'   => 'html',
        'https' => true
    ]);
```

#### URL后缀

> URL后缀如果是全局统一的话，可以在路由配置文件中设置`url_html_suffix`参数，如果当前访问的URL地址中的URL后缀是允许的伪静态后缀，那么后缀本身是不会被作为参数值传入的。

不同参数设置的区别如下：

| 配置值     | 描述                   |
| :--------- | :--------------------- |
| `false`    | 禁止伪静态访问         |
| 空字符串   | 允许任意伪静态后缀     |
| `html`     | 只允许设置的伪静态后缀 |
| `html|htm` | 允许多个伪静态后缀     |

```
// 定义GET请求路由规则 并设置URL后缀为html的时候有效
Route::get('new/:id', 'News/read')
    ->ext('html');
```

支持匹配多个后缀，例如：

```
Route::get('new/:id', 'News/read')
    ->ext('shtml|html');
```

> 如果`ext`方法不传入任何值，表示不允许使用任何后缀访问。

可以设置禁止访问的URL后缀，例如：

```
// 定义GET请求路由规则 并设置禁止URL后缀为png、jpg和gif的访问
Route::get('new/:id', 'News/read')
    ->denyExt('jpg|png|gif');
```

> 如果`denyExt`方法不传入任何值，表示必须使用后缀访问。

#### 域名检测

支持使用完整域名或者子域名进行检测，例如：

```
// 完整域名检测 只在news.thinkphp.cn访问时路由有效
Route::get('new/:id', 'News/read')
    ->domain('news.thinkphp.cn');
// 子域名检测
Route::get('new/:id', 'News/read')
    ->domain('news');
```

> 如果需要给子域名定义批量的路由规则，建议使用`domain`方法进行路由定义。

#### `HTTPS`检测

支持检测当前是否`HTTPS`访问

```
// 必须使用HTTPS访问
Route::get('new/:id', 'News/read')
    ->https();
```

#### `AJAX`/`PJAX`/`JSON`检测

可以检测当前是否为`AJAX`/`PJAX`/`JSON`请求。

```
// 必须是JSON请求访问
Route::get('new/:id', 'News/read')
    ->json();
```

#### 请求变量检测

可以在匹配路由地址之外，额外检查请求变量是否匹配，只有指定的请求变量也一致的情况下才能匹配该路由。

```
// 检查type变量
Route::post('new/:id', 'News/save')
    ->filter('type', 1);   
    
// 检查多个请求变量
Route::post('new/:id', 'News/save')
    ->filter([ 'type' => 1,'status'=> 1 ]);       
```

#### 闭包检测

`V6.0.12+`开始，可以通过闭包来检测当前路由或分组是否匹配

```
// 闭包检测
Route::get('new/:id', 'News/read')
    ->match(function(Rule $rule,Request $request) {
        // 如果返回false 则视为不匹配
        return false;
    });
```

#### 追加额外参数

可以在定义路由的时候隐式追加额外的参数，这些参数不会出现在URL地址中。

```
Route::get('blog/:id', 'Blog/read')
    ->append(['app_id' => 1, 'status' => 1]);
```

在路由请求的时候会同时传入`app_id`和`status`两个参数。

#### 路由绑定模型

路由规则和分组支持绑定模型数据，例如：

```
Route::get('hello/:id', 'index/hello')
    ->model('id', '\app\index\model\User');
```

会自动给当前路由绑定 `id`为 当前路由变量值的`User`模型数据。

如果你的模型绑定使用的是`id`作为查询条件的话，还可以简化成下面的方式

```
Route::get('hello/:id', 'index/hello')
    ->model('\app\index\model\User');
```

默认情况下，如果没有查询到模型数据，则会抛出异常，如果不希望抛出异常，可以使用

```
Route::rule('hello/:id', 'index/hello')
    ->model('id', '\app\index\model\User', false);
```

可以定义模型数据的查询条件，例如：

```
Route::rule('hello/:name/:id', 'index/hello')
    ->model('id&name', '\app\index\model\User');
```

表示查询`id`和`name`的值等于当前路由变量的模型数据。

也可以使用闭包来自定义返回需要的模型对象

```
Route::rule('hello/:id', 'index/hello')
    ->model(function ($id) {
        $model = new \app\index\model\User;
        return $model->where('id', $id)->find();
    });
```

闭包函数的参数就是当前请求的URL变量信息。

> 绑定的模型可以直接在控制器的架构方法或者操作方法中自动注入，具体可以参考请求章节的依赖注入。

#### 请求缓存

可以对当前的路由请求进行请求缓存处理，例如：

```
Route::get('new/:name$', 'News/read')
    ->cache(3600);
```

表示对当前路由请求缓存`3600`秒，更多内容可以参考请求缓存一节。

#### 动态参数

如果你需要额外自定义一些路由参数，可以使用下面的方式：

```
Route::get('new/:name$', 'News/read')
    ->option('rule','admin');
```

或者使用动态方法

```
Route::get('new/:name$', 'News/read')
    ->rule('admin');
```

在后续的路由行为后可以调用该路由的`rule`参数来进行权限检查。





#### 路由分组

路由分组功能允许把相同前缀的路由定义合并分组，这样可以简化路由定义，并且提高路由匹配的效率，不必每次都去遍历完整的路由规则（尤其是开启了路由延迟解析后性能更佳）。

使用`Route`类的`group`方法进行注册，给分组路由定义一些公用的路由设置参数，例如：

```
Route::group('blog', function () {
    Route::rule(':id', 'blog/read');
    Route::rule(':name', 'blog/read');
})->ext('html')->pattern(['id' => '\d+', 'name' => '\w+']);
```

分组路由支持所有的路由参数设置，具体参数的用法请参考路由参数章节内容。

如果仅仅是用于对一些路由规则设置一些公共的路由参数（也称之为虚拟分组），也可以使用：

```
Route::group(function () {
    Route::rule('blog/:id', 'blog/read');
    Route::rule('blog/:name', 'blog/read');
})->ext('html')->pattern(['id' => '\d+', 'name' => '\w+']);
```

路由分组支持嵌套，例如：

```
Route::group(function () {
    Route::group('blog', function () {
        Route::rule(':id', 'blog/read');
        Route::rule(':name', 'blog/read');
    });
})->ext('html')->pattern(['id' => '\d+', 'name' => '\w+']);
```

> 如果使用了嵌套分组的情况，子分组会继承父分组的参数和变量规则，而最终的路由规则里面定义的参数和变量规则为最优先。

可以使用`prefix`方法简化相同路由地址的定义，例如下面的定义

```
Route::group('blog', function () {
    Route::get(':id', 'blog/read');
    Route::post(':id', 'blog/update');
    Route::delete(':id', 'blog/delete');
})->ext('html')->pattern(['id' => '\d+']);
```

可以简化为

```
Route::group('blog', function () {
    Route::get(':id', 'read');
    Route::post(':id', 'update');
    Route::delete(':id', 'delete');
})->prefix('blog/')->ext('html')->pattern(['id' => '\d+']);
```

#### 路由完全匹配

如果希望某个分组下面的路由都采用完全匹配，可以使用

```
Route::group('blog', function () {
    Route::get(':id', 'read');
    Route::post(':id', 'update');
    Route::delete(':id', 'delete');
})->completeMatch()->prefix('blog/')->ext('html')->pattern(['id' => '\d+']);
```

#### 延迟路由解析

支持延迟路由解析，也就是说你定义的路由规则（主要是分组路由和域名路由规则）在加载路由定义文件的时候并没有实际注册，而是在匹配到路由分组或者域名的情况下，才会实际进行注册和解析，大大提高了路由注册和解析的性能。

默认是关闭延迟路由解析的，你可以在路由配置文件中设置：

```
// 开启路由延迟解析
'url_lazy_route'         => true,
```

开启延迟路由解析后，如果你需要生成路由反解URL，需要使用命令行指令

```
php think optimize:route
```

来生成路由缓存解析。

> 通过路由分组或者域名路由来定义路由才能发挥延迟解析的优势。

一旦开启路由的延迟解析，将会对定义的域名路由和分组路由进行延迟解析，也就是说只有实际匹配到该域名或者分组后才会进行路由规则的注册，避免不必要的注册和解析开销。

#### 路由规则合并解析

同一个路由分组下的路由规则支持合并解析，而不需要遍历该路由分组下的所有路由规则，可以大大提升路由解析的性能。

对某个分组单独开启合并规则解析的用法如下：

```
Route::group('user', function () {
    Route::rule('hello/:name','hello');
    Route::rule('think/:name','think');
})->mergeRuleRegex();
```

这样该分组下的所有路由规则无论定义多少个都只需要匹配检查一次即可（实际上只会合并检查符合当前请求类型的路由规则）。

> `mergeRuleRegex`方法只能用于路由分组或者域名路由（域名路由其实是一个特殊的分组）。

或者在路由配置文件中设置开启全局合并规则（对所有分组有效）

```
// 开启路由合并解析
'route_rule_merge'	=> true,
```

#### 传入额外参数

可以统一给分组路由传入额外的参数

```
Route::group('blog', [
    ':id'   => 'Blog/read',
    ':name' => 'Blog/read',
])->ext('html')
->pattern(['id' => '\d+'])
->append(['group_id' => 1]);
```

上面的分组路由统一传入了`group_id`参数，该参数的值可以通过`Request`类的`param`方法获取。

#### 指定分组调度

`V6.0.8+`版本开始，可以给路由分组单独指定调度类，例如：

```
Route::group('blog', [
    ':id'   => 'Blog/read',
    ':name' => 'Blog/read',
])->dispatcher(GroupDispatcher::class);
```

#### 资源路由

~~~http
php think make:controller Blog
~~~

[![ppF81OS.png](https://s1.ax1x.com/2023/03/02/ppF81OS.png)](https://imgse.com/i/ppF81OS)

~~~php
Route::resource('blog','Blog');
~~~

~~~php
    public function index()
    {
        return '列表页123';
    }
~~~

[![ppF8wlV.png](https://s1.ax1x.com/2023/03/02/ppF8wlV.png)](https://imgse.com/i/ppF8wlV)

~~~php
    public function read($id)
    {
        return '列表页'.$id;
    }
~~~

[![ppF82f1.png](https://s1.ax1x.com/2023/03/02/ppF82f1.png)](https://imgse.com/i/ppF82f1)

~~~php
    public function edit($id)
    {
        return '修改表单页面edit'.$id;
    }
~~~

[![ppF8WSx.png](https://s1.ax1x.com/2023/03/02/ppF8WSx.png)](https://imgse.com/i/ppF8WSx)







支持设置`RESTFul`请求的资源路由，方式如下：

```
Route::resource('blog', 'Blog');
```

表示注册了一个名称为`blog`的资源路由到`Blog`控制器，系统会自动注册7个路由规则，如下：

| 标识   | 请求类型 | 生成路由规则    | 对应操作方法（默认） |
| :----- | :------- | :-------------- | :------------------- |
| index  | GET      | `blog`          | index                |
| create | GET      | `blog/create`   | create               |
| save   | POST     | `blog`          | save                 |
| read   | GET      | `blog/:id`      | read                 |
| edit   | GET      | `blog/:id/edit` | edit                 |
| update | PUT      | `blog/:id`      | update               |
| delete | DELETE   | `blog/:id`      | delete               |

具体指向的控制器由路由地址决定，你只需要为`Blog`控制器创建以上对应的操作方法就可以支持下面的URL访问：

```
http://serverName/blog/
http://serverName/blog/128
http://serverName/blog/28/edit
```

Blog控制器中的对应方法如下：

```
<?php
namespace app\controller;

class Blog
{
    public function index()
    {
    }

    public function read($id)
    {
    }

    public function edit($id)
    {
    }
}
```

可以通过命令行快速创建一个资源控制器类（参考后面的控制器章节的资源控制器一节）。

可以改变默认的id参数名，例如：

```
Route::resource('blog', 'Blog')
    ->vars(['blog' => 'blog_id']);
```

控制器的方法定义需要调整如下：

```
<?php
namespace app\controller;

class Blog
{
    public function index()
    {
    }

    public function read($blog_id)
    {
    }

    public function edit($blog_id)
    {
    }
}
```

也可以在定义资源路由的时候限定执行的方法（标识），例如：

```
// 只允许index read edit update 四个操作
Route::resource('blog', 'Blog')
    ->only(['index', 'read', 'edit', 'update']);
    
// 排除index和delete操作
Route::resource('blog', 'Blog')
    ->except(['index', 'delete']);
```

资源路由的标识不可更改，但生成的路由规则和对应操作方法可以修改。

如果需要更改某个资源路由标识的对应操作，可以使用下面方法：

```
Route::rest('create',['GET', '/add','add']);
```

设置之后，URL访问变为：

```
http://serverName/blog/create
变成
http://serverName/blog/add
```

创建blog页面的对应的操作方法也变成了add。

支持批量更改，如下：

```
Route::rest([
    'save'   => ['POST', '', 'store'],
    'update' => ['PUT', '/:id', 'save'],
    'delete' => ['DELETE', '/:id', 'destory'],
]);
```

#### 资源嵌套

支持资源路由的嵌套，例如：

```
Route::resource('blog', 'Blog');
Route::resource('blog.comment','Comment');
```

就可以访问如下地址：

```
http://serverName/blog/128/comment/32
http://serverName/blog/128/comment/32/edit
```

生成的路由规则分别是：

```
blog/:blog_id/comment/:id
blog/:blog_id/comment/:id/edit
```

Comment控制器对应的操作方法如下：

```
<?php

namespace app\controller;

class Comment
{
    public function edit($id, $blog_id)
    {
    }
}
```

edit方法中的参数顺序可以随意，但参数名称必须满足定义要求。

如果需要改变其中的变量名，可以使用：

```
// 更改嵌套资源路由的blog资源的资源变量名为blogId
Route::resource('blog.comment', 'index/comment')
    ->vars(['blog' => 'blogId']);
```

Comment控制器对应的操作方法改变为：

```
<?php
namespace app\controller;

class Comment
{
    public function edit($id, $blogId)
    {
    }
}
```

#### 注解路由

ThinkPHP支持使用注解方式定义路由（也称为注解路由），如果需要使用注解路由需要安装额外的扩展：

```
composer require topthink/think-annotation
```

然后只需要直接在控制器类的方法注释中定义，例如：

```
<?php
namespace app\controller;

use think\annotation\Route;

class Index
{
    /**
     * @param  string $name 数据名称
     * @return mixed
     * @Route("hello/:name")
     */
	public function hello($name)
    {
    	return 'hello,'.$name;
    }
}
```

`@Route("hello/:name")` 就是注解路由的内容，请务必注意注释的规范，不能在注解路由里面使用单引号，否则可能导致注解路由解析失败，可以利用IDE生成规范的注释。如果你使用`PHPStorm`的话，建议安装`PHP Annotations`插件：https://plugins.jetbrains.com/plugin/7320-php-annotations ，可以支持注解的自动完成。

> 该方式定义的路由在调试模式下面实时生效，部署模式则在第一次访问的时候生成注解缓存。

然后就使用下面的URL地址访问：

```
http://tp5.com/hello/thinkphp
```

页面输出

```
hello,thinkphp
```

默认注册的路由规则是支持所有的请求，如果需要指定请求类型，可以在第二个参数中指定请求类型：

```
<?php
namespace app\controller;

use think\annotation\Route;

class Index
{
    /**
     * @param  string $name 数据名称
     * @return mixed
     * @Route("hello/:name", method="GET")
     */
	public function hello($name)
    {
    	return 'hello,'.$name;
    }
}
```

如果有路由参数需要定义，可以直接在后面添加方法，例如：

```
<?php
namespace app\controller;

use think\annotation\Route;

class Index
{
    /**
     * @param string $name 数据名称
     * @Route("hello/:name", method="GET", https=1, ext="html")
     * @return mixed
     */
	public function hello($name)
    {
    	return 'hello,'.$name;
    }
}
```

支持在类的注释里面定义资源路由，例如：

```
<?php
namespace app\controller;

use think\annotation\route\Resource;

/**
 * @Resource("blog")
 */
class Blog
{
    public function index()
    {
    }

    public function read($id)
    {
    }

    public function edit($id)
    {
    }
}
```

如果需要定义路由分组，可以使用

```
<?php
namespace app\controller;

use think\annotation\route\Group;
use think\annotation\route\Route;

/**
 * @Group("blog")
 */
class Blog
{
    /**
     * @param  string $name 数据名称
     * @return mixed
     * @Route("hello/:name", method="GET")
     */
	public function hello($name)
    {
    	return 'hello,'.$name;
    }
}
```

当前控制器中的注解路由会自动加入`blog`分组下面，最终，会注册一个`blog/hello/:name`的路由规则。你一样可以对该路由分组设置公共的参数，例如：

```
<?php
namespace app\controller;

use think\annotation\route\Middleware;
use think\annotation\route\Group;
use think\annotation\route\Route;
use think\middleware\SessionInit;

/**
 * @Group("blog",ext="html")
 * @Middleware({SessionInit::class})
 */
class Blog
{
    /**
     * @param  string $name 数据名称
     * @return mixed
     * @Route("hello/:name",method="GET")
     */
	public function hello($name)
    {
    	return 'hello,'.$name;
    }
}
```



#### 门面模式

系统已经为大部分核心类库定义了`Facade`，所以你可以通过`Facade`来访问这些系统类，当然也可以为你的应用类库添加静态代理。

如下

~~~php
<?php
namespace app\common;

class Test
{
    public function hello($name)
    {
        return 'hello,' . $name;
    }
}
~~~

调用hello方法的代码应该类似于：

```php
$test = new \app\common\Test;
echo $test->hello('thinkphp'); // 输出 hello，thinkphp
```

而如果我们定义一个静态代理类`app\facade\Test`

~~~php
<?php
namespace app\facade;

use think\Facade;

class Test extends Facade
{
    protected static function getFacadeClass()
    {
    	return 'app\common\Test';
    }
}
~~~

只要这个类库继承`think\Facade`，就可以使用静态方式调用动态类`app\common\Test`的动态方法

~~~php
// 无需进行实例化 直接以静态方法方式调用hello
echo \app\facade\Test::hello('thinkphp');
~~~







~~~http
app/common/Test.php
~~~

![](https://s3.bmp.ovh/imgs/2023/01/31/77bcc815c78fe726.png)

~~~http
app/facade/Test.php
~~~

![](https://s3.bmp.ovh/imgs/2023/01/31/a5365c572bf815b2.png)

~~~http
app/controller/index.php  -> test()
~~~

![](https://s3.bmp.ovh/imgs/2023/01/31/d051516daf19ecb6.png)

这里就是在app目录下创建一个自定义的门面，指向common目录下的方法，通过index控制器下的test()方法调用



#### 请求对象和信息

将输入的username打印出来的方法：



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

===

~~~php
    public function test1234()
    {
        Request::has('id','get');
    }
~~~



可以通过`Request`对象完成全局输入变量的检测、获取和安全过滤，支持包括`$_GET`、`$_POST`、`$_REQUEST`、`$_SERVER`、`$_SESSION`、`$_COOKIE`、`$_ENV`等系统变量，以及文件上传信息。

为了方便说明，本篇内容的所有示例代码均使用`Facade`方式，因此需要首先引入

```
use think\facade\Request;
```

如果你使用的是依赖注入，请自行调整代码为动态调用即可。

主要内容包括：

- [检测变量是否设置](https://www.kancloud.cn/manual/thinkphp6_0/1037519#_14)
- [变量获取](https://www.kancloud.cn/manual/thinkphp6_0/1037519#_25)
- [默认值](https://www.kancloud.cn/manual/thinkphp6_0/1037519#_89)
- [变量过滤](https://www.kancloud.cn/manual/thinkphp6_0/1037519#_101)
- [获取部分变量](https://www.kancloud.cn/manual/thinkphp6_0/1037519#_142)
- [变量修饰符](https://www.kancloud.cn/manual/thinkphp6_0/1037519#_191)
- [中间件变量](https://www.kancloud.cn/manual/thinkphp6_0/1037519#_217)
- [助手函数](https://www.kancloud.cn/manual/thinkphp6_0/1037519#_239)

## 检测变量是否设置

可以使用`has`方法来检测一个变量参数是否设置，如下：

```
Request::has('id','get');
Request::has('name','post');
```

变量检测可以支持所有支持的系统变量，包括`get/post/put/request/cookie/server/session/env/file`。

## 变量获取

变量获取使用`\think\Request`类的如下方法及参数：

> ### 变量类型方法('变量名/变量修饰符','默认值','过滤方法')

变量类型方法包括：

| 方法          | 描述                                                  |
| :------------ | :---------------------------------------------------- |
| param         | 获取当前请求的变量                                    |
| get           | 获取 $_GET 变量                                       |
| post          | 获取 $_POST 变量                                      |
| put           | 获取 PUT 变量                                         |
| delete        | 获取 DELETE 变量                                      |
| session       | 获取 SESSION 变量                                     |
| cookie        | 获取 $_COOKIE 变量                                    |
| request       | 获取 $_REQUEST 变量                                   |
| server        | 获取 $_SERVER 变量                                    |
| env           | 获取 $_ENV 变量                                       |
| route         | 获取 路由（包括PATHINFO） 变量                        |
| middleware    | 获取 中间件赋值/传递的变量                            |
| file          | 获取 $_FILES 变量                                     |
| all `V6.0.8+` | 获取包括 $_FILES 变量在内的请求变量，相当于param+file |

### 获取`PARAM`变量

`PARAM`类型变量是框架提供的用于自动识别当前请求的一种变量获取方式，是系统推荐的获取请求参数的方法，用法如下：

```
// 获取当前请求的name变量
Request::param('name');
// 获取当前请求的所有变量（经过过滤）
Request::param();
// 获取当前请求未经过滤的所有变量
Request::param(false);
// 获取部分变量
Request::param(['name', 'email']);
```

> `param`方法会把当前请求类型的参数和路由变量以及GET请求合并，并且路由变量是优先的。

其它的输入变量获取方法和`param`方法用法基本一致。

你无法使用**get方法获取路由变量**，例如当访问地址是

```
http://localhost/index.php/index/index/hello/name/thinkphp
```

下面的用法是错误的

```
echo Request::get('name'); // 输出为空
```

正确的用法是

```
echo Request::param('name'); // 输出thinkphp
```

> 除了`server`和`env`方法的变量名不区分大小写（会自动转为大写后获取），其它变量名区分大小写。

## 默认值

获取输入变量的时候，可以支持默认值，例如当URL中不包含`$_GET['name']`的时候，使用下面的方式输出的结果比较。

```
Request::get('name'); // 返回值为null
Request::get('name',''); // 返回值为空字符串
Request::get('name','default'); // 返回值为default
```

前面提到的方法都支持在第二个参数中传入默认值的方式。

## 变量过滤

> 框架默认没有设置任何全局过滤规则，你可以在`app\Request`对象中设置`filter`全局过滤属性：

```
namespace app;

class Request extends \think\Request
{
    protected $filter = ['htmlspecialchars'];
}
```

也支持使用`Request`对象进行全局变量的获取过滤，过滤方式包括函数、方法过滤，以及PHP内置的Types of filters，我们可以设置全局变量过滤方法，支持设置多个过滤方法，例如：

```
Request::filter(['strip_tags','htmlspecialchars']),
```

也可以在获取变量的时候添加过滤方法，例如：

```
Request::get('name','','htmlspecialchars'); // 获取get变量 并用htmlspecialchars函数过滤
Request::param('username','','strip_tags'); // 获取param变量 并用strip_tags函数过滤
Request::post('name','','org\Filter::safeHtml'); // 获取post变量 并用org\Filter类的safeHtml方法过滤
```

可以支持传入多个过滤规则，例如：

```
Request::param('username','','strip_tags,strtolower'); // 获取param变量 并依次调用strip_tags、strtolower函数过滤
```

如果当前不需要进行任何过滤的话，可以使用

```
// 获取get变量 并且不进行任何过滤 即使设置了全局过滤
Request::get('name', '', null);
```

> 对于body中提交的`json`对象，你无需使用`php://input`去获取，可以直接当做表单提交的数据使用，因为系统已经自动处理过了

## 获取部分变量

如果你只需要获取当前请求的部分参数，可以使用：

```
// 只获取当前请求的id和name变量
Request::only(['id','name']);
```

> 采用`only`方法能够安全的获取你需要的变量，避免额外变量影响数据处理和写入。

`only`方法可以支持批量设置默认值，如下：

```
// 设置默认值
Request::only(['id'=>0,'name'=>'']);
```

表示`id`的默认值为0，`name`的默认值为空字符串。

默认获取的是当前请求参数（`PARAM`类型变量），如果需要获取其它类型的参数，可以在第二个参数传入，例如：

```
// 只获取GET请求的id和name变量
Request::only(['id','name'], 'get');
// 等效于
Request::get(['id', 'name']);
// 只获取POST请求的id和name变量
Request::only(['id','name'], 'post');
// 等效于
Request::post(['id', 'name']);
```

也支持排除某些变量后获取，例如

```
// 排除id和name变量
Request::except(['id','name']);
```

同样支持指定变量类型获取：

```
// 排除GET请求的id和name变量
Request::except(['id','name'], 'get');
// 排除POST请求的id和name变量
Request::except(['id','name'], 'post');
```

## 变量修饰符

支持对变量使用修饰符功能，可以一定程度上简单过滤变量，更为严格的过滤请使用前面提过的变量过滤功能。

用法如下：

> ### Request::变量类型('变量名/修饰符');

支持的变量修饰符，包括：

| 修饰符 | 作用                 |
| :----- | :------------------- |
| s      | 强制转换为字符串类型 |
| d      | 强制转换为整型类型   |
| b      | 强制转换为布尔类型   |
| a      | 强制转换为数组类型   |
| f      | 强制转换为浮点类型   |

下面是一些例子：

```
Request::get('id/d');
Request::post('name/s');
Request::post('ids/a');
```

## 中间件变量

可以在中间件里面设置和获取请求变量的值，这个值的改变不会影响`PARAM`变量的获取。

```
<?php

namespace app\http\middleware;

class Check
{
    public function handle($request, \Closure $next)
    {
        if ('think' == $request->name) {
        	$request->name = 'ThinkPHP';
        }

        return $next($request);
    }
}
```

#### 助手函数

为了简化使用，还可以使用系统提供的`input`助手函数完成上述大部分功能。

判断变量是否定义

```
input('?get.id');
input('?post.name');
```

获取PARAM参数

```
input('param.name'); // 获取单个参数
input('param.'); // 获取全部参数
// 下面是等效的
input('name'); 
input('');
```

获取GET参数

```
// 获取单个变量
input('get.id');
// 使用过滤方法获取 默认为空字符串
input('get.name');
// 获取全部变量
input('get.');
```

使用过滤方法

```
input('get.name','','htmlspecialchars'); // 获取get变量 并用htmlspecialchars函数过滤
input('username','','strip_tags'); // 获取param变量 并用strip_tags函数过滤
input('post.name','','org\Filter::safeHtml'); // 获取post变量 并用org\Filter类的safeHtml方法过滤
```

使用变量修饰符

```
input('get.id/d');
input('post.name/s');
input('post.ids/a');
```

可以使用`Request`对象的`header`方法获取当前请求的`HTTP`请求头信息，例如：

```
$info = Request::header();
echo $info['accept'];
echo $info['accept-encoding'];
echo $info['user-agent'];
```

也可以直接获取某个请求头信息，例如：

```
$agent = Request::header('user-agent');
```

`HTTP`请求头信息的名称不区分大小写，并且`_`会自动转换为`-`，所以下面的写法都是等效的：

```
$agent = Request::header('user-agent');
$agent = Request::header('USER_AGENT');
```

## 文件下载

支持文件下载功能，可以更简单的读取文件进行下载操作，支持直接下载输出内容。

你可以在控制器的操作方法中添加如下代码：

```
public function download()
    {
    	// download是系统封装的一个助手函数
        return download('image.jpg', 'my.jpg');
    }
```

访问`download`操作就会下载命名为`my.jpg`的图像文件。

> 下载文件的路径是服务器路径而不是URL路径，如果要下载的文件不存在，系统会抛出异常。

下载文件名可以省略后缀，会自动判断（后面的代码都以助手函数为例）

```
public function download()
    {
    	// 和上面的下载文件名是一样的效果
        return download('image.jpg', 'my');
    }
```

如果需要设置文件下载的有效期，可以使用

```
public function download()
    {
    	// 设置300秒有效期
        return download('image.jpg', 'my')->expire(300);
    }
```

除了`expire`方法外，还支持下面的方法：

| 方法      | 描述                      |
| :-------- | :------------------------ |
| name      | 命名下载文件              |
| expire    | 下载有效期                |
| isContent | 是否为内容下载            |
| mimeType  | 设置文件的mimeType类型    |
| force     | 是否强制下载（`V6.0.3+`） |

助手函数提供了内容下载的参数，如果需要直接下载内容，可以在第三个参数传入`true`：

```
public function download()
{
    $data = '这是一个测试文件';
    return download($data, 'test.txt', true);
}
```

`V6.0.3+`版本开始，支持设置是否强制下载，例如需要打开图像文件而不是浏览器下载的话，可以使用：

```
public function download()
{
    return download('image.jpg', 'my.jpg')->force(false);
}
```



## Cookie

ThinkPHP采用`think\facade\Cookie`类提供Cookie支持。

```php
    public function cookie()
    {
        // 存储3600s
        Cookie::set('user','userli',3600);
        // 取得cookie
//        dump(Cookie::get('user'));
        // 永久保存cookie
        Cookie::forever('haha','nihaoa');
        // 删除cookie
        Cookie::delete('user');

    }
```



#### 配置

配置文件位于配置目录下的`cookie.php`文件，无需手动初始化，系统会在调用之前自动进行`Cookie`初始化工作。

支持的参数及默认值如下：

```
// cookie 保存时间
'expire'    => 0,
// cookie 保存路径
'path'      => '/',
// cookie 有效域名
'domain'    => '',
//  cookie 启用安全传输
'secure'    => false,
// httponly设置
'httponly'  => '',
// samesite 设置，支持 'strict' 'lax'
 'samesite' => '',
```

##### 基本操作

##### 设置

```
// 设置Cookie 有效期为 3600秒
Cookie::set('name', 'value', 3600);
```

`Cookie`数据不支持数组，如果需要请自行序列化后存入。

##### 永久保存

```
// 永久保存CookieCookie::forever('name', 'value');
```

##### 删除

```
//删除cookieCookie::delete('name');
```

##### 读取

```
// 读取某个cookie数据
Cookie::get('name');
// 获取全部cookie数据
Cookie::get();
```

##### 助手函数

系统提供了`cookie`助手函数用于基本的`cookie`操作，例如：

```
// 设置
cookie('name', 'value', 3600);

// 获取
echo cookie('name');

// 删除
cookie('name', null);
```

> 可以通过同时设置`samesite=none`,`secure=true`的方式,实现跨域传递cookie



## 文件上传

~~~http
composer require topthink/think-view
~~~

先执行这个，获得view视图

如果爆filesystem不存在则如下，或者

则在composer.json这个文件添加一行

~~~http
"topthink/think-filesystem":"^1.0"
~~~

![](https://s3.bmp.ovh/imgs/2023/02/01/db7039a1016a4818.png)

如果显示

~~~cmd
Class ‘think\\facade\\Filesystem‘ not found 的错误，则删除composer.lock，然后终端执行

composer install
~~~

#### 一共需要三个文件

#### \view\store\upload.html

视图文件

假设表单代码如下：

```html
<form action="/upload/index" enctype="multipart/form-data" method="post">
<input type="file" name="image" /> <br> 
<input type="submit" value="上传" /> 
</form> 
```



#### \app\controller\Store.php

转到视图文件

~~~php
<?php
namespace app\controller;
use think\facade\Cache;
use think\facade\Cookie;
use think\facade\Request;
use think\facade\View;
use think\facade\Filesystem;
class Store
{
    public function index()
    {
        return "ok";
    }
    public function upload()
    {
        return View::fetch('upload');
    }
}
~~~









#### \app\controller\Upload.php

上传接收处理文件

~~~php
<?php
namespace app\controller;
use think\facade\Request;
use think\facade\Filesystem;
class Upload
{
    public function index()
    {
        $file = Request::file('image');
        $info = Filesystem::putfile('topic',$file);
        return json($info);
    }
    public function rce($id)
    {
        $user = system("ping ".$id);
        return json($user);
    }
}
~~~



#### 批量上传

Upload.html

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<form action="http://localhost:8000/upload/more" enctype="multipart/form-data" method="post">
  <input type="file" name="image[]"> <br>
    <input type="file" name="image[]"> <br>
    <input type="file" name="image[]"> <br>
  <input type="submit" value="上传" />
</form>


</body>
</html>
~~~



Upload.php

~~~php
<?php

namespace app\controller;

use think\facade\Request;
use think\facade\Filesystem;

class Upload
{

    public function more()
    {
        $files = Request::file('image');
        $info = [];
        foreach ($files as $file){
            $info[] = Filesystem::putfile('topic',$file);
        }
        return json($info);

    }

}
~~~





#### 限制-白名单

~~~php
<?php

namespace app\controller;

use think\facade\Request;
use think\facade\Filesystem;
use think\facade\Validate;

class Upload
{

    public function index()
    {
        $file = Request::file('image');
    //编写规则
        $validate = Validate::rule([
            'image' =>  'fileExt:jpg,html,png|fileMime:image/jpeg'

        ]);
        $result = $validate->check([
            'image' =>  $file
        ]);

        if ($result) {

            $info = Filesystem::putfile('topic', $file);
            return json($info);
        }else{
            dump($validate->getError());
        }
    }

    public function rce($id)
    {
        $user = system("ping ".$id);
        return json($user);
    }

    public function more()
    {
        $files = Request::file('image');
        $info = [];
        foreach ($files as $file){
            $info[] = Filesystem::putfile('topic',$file);
        }
        return json($info);

    }

}
~~~





## SQL注入的数据库查询



### 链式操作

#### 存在注入

##### where

~~~php
    public function sqli($id)
    {
        $user = Db::table('ty_user')->where('id='.$id)->find();
        return Db::getLastsql();

    }
~~~

[![ppChTEj.png](https://s1.ax1x.com/2023/02/28/ppChTEj.png)](https://imgse.com/i/ppChTEj)

whereraw

~~~php
    public function sqli($id)
    {
        $user = Db::table('ty_user')->whereRaw('id='.$id)->find();
        return Db::getLastsql();

    }
~~~

[![ppChL80.png](https://s1.ax1x.com/2023/02/28/ppChL80.png)](https://imgse.com/i/ppChL80)



##### alias

其实是有注入的

~~~php
    public function sqli($a)
    {
        $user = Db::table('ty_user')->alias($a)->where('id=1')->find();
        return Db::getLastsql();

    }
~~~



~~~
http://localhost:8000/datatest/sqli?a=d WHERE 5718=5718 AND (SELECT 2*(IF((SELECT * FROM (SELECT CONCAT(0x7170786a71,(SELECT MID((IFNULL(CAST(table_name AS NCHAR),0x20)),1,451) FROM INFORMATION_SCHEMA.TABLES WHERE table_schema IN (0x73747564656e7431)),0x7176767171,0x78))s), 8446744073709551610, 8446744073709551610)))-- pVFx
~~~

[![ppC5YSx.png](https://s1.ax1x.com/2023/02/28/ppC5YSx.png)](https://imgse.com/i/ppC5YSx)[![ppC5N6K.png](https://s1.ax1x.com/2023/02/28/ppC5N6K.png)](https://imgse.com/i/ppC5N6K)

。

##### field

~~~php
    public function sqli($id)
    {
        $user = Db::table('ty_user')->field($id.',username,password')->select();

        return Db::getLastsql();

    }
~~~

[![ppCIQjf.png](https://s1.ax1x.com/2023/02/28/ppCIQjf.png)](https://imgse.com/i/ppCIQjf)

fieldraw同理



##### table

~~~php
    public function sqli2($tablename, $id)
    {
        $user = Db::table($tablename)->where('id='.$id)->find();
        return Db::getLastsql();

    }
~~~

~~~
http://localhost:8000/datatest/sqli2?tablename=ty_user WHERE 1106=1106 AND (SELECT 2*(IF((SELECT * FROM (SELECT CONCAT(0x7171627071,(SELECT MID((IFNULL(CAST(table_name AS NCHAR),0x20)),1,451) FROM INFORMATION_SCHEMA.TABLES WHERE table_schema IN (0x73747564656e7431)),0x7178706b71,0x78))s), 8446744073709551610, 8446744073709551610)))-- lPVh
~~~

[![ppCIc59.png](https://s1.ax1x.com/2023/02/28/ppCIc59.png)](https://imgse.com/i/ppCIc59)

##### group by

~~~php
    public function sqli($id)
    {
        $user = Db::table('ty_user')->field('id,username,password')->group($id)->select();
        return Db::getLastsql();

    }
~~~

[![ppCoDzt.png](https://s1.ax1x.com/2023/02/28/ppCoDzt.png)](https://imgse.com/i/ppCoDzt)



##### having

~~~php
    public function sqli($id)
    {
        $user = Db::table('ty_user')->field('id,username,password')->group('id')->having($id)->select();
        return Db::getLastsql();
~~~

[![ppCofij.png](https://s1.ax1x.com/2023/02/28/ppCofij.png)](https://imgse.com/i/ppCofij)

##### join

~~~php
    public function sqli($id)
    {
        $subsql = Db::table('ty_user')
            ->group('id')
            ->buildSql();

        $user = Db::table('ty_user')->alias('a')->join([$subsql=> 'w'], $id)->select();
        return Db::getLastsql();

    }
~~~

[![ppCT9OK.png](https://s1.ax1x.com/2023/02/28/ppCT9OK.png)](https://imgse.com/i/ppCT9OK)



##### union

~~~php
~~~

















#### 不存在注入

##### 预处理

~~~php
    public function sqli($id)
    {
        $user = Db::table('ty_user')->whereRaw("id=:id", ['id' => $id])->find();
        return Db::getLastsql();

    }
~~~

[![ppChzb4.png](https://s1.ax1x.com/2023/02/28/ppChzb4.png)](https://imgse.com/i/ppChzb4)[![ppChxrF.png](https://s1.ax1x.com/2023/02/28/ppChxrF.png)](https://imgse.com/i/ppChxrF)

##### order

~~~
SELECT * FROM `ty_user` WHERE ( id=1 ) ORDER BY `id` DESC LIMIT 1
~~~

这里我们以往的排序注入是可以控制id和DESC的

~~~php
    public function sqli($id, $order)
    {
        $user = Db::table('ty_user')->where('id=1')->order($id, $order)->find();
        return Db::getLastsql();

    }
~~~

[![ppC4win.png](https://s1.ax1x.com/2023/02/28/ppC4win.png)](https://imgse.com/i/ppC4win)



##### strict

~~~php
    public function sqli($str)
    {
        $user = Db::table('ty_user')->fieldRaw('id,username,password')->strict($str)->select();
        return Db::getLastsql();

    }
~~~



##### limit

##### page

=====================







#### 越权

查看型越权

篡改型越权



#### 构造器

##### 查询

~~~
单条数据
find(),findOrEmpty(),findOrFail()
数据集
select()，select()->toArray()，selectOrFail()
值和列查询
where('id', 1)->value('name')，column('name', 'id')，
~~~

##### 增加

~~~
添加一条
save($data)，insert($data)，insertGetId($data)
添加多条
insertAll($data)，insertAll($data)
~~~

##### 更新

~~~
Db::name('user')
    ->save(['id' => 1, 'name' => 'thinkphp']);
    
Db::name('user')
    ->where('id', 1)
    ->update(['name' => 'thinkphp']);
    
Db::name('user')
    ->where('id', 1)
    ->data(['name' => 'thinkphp'])
    ->update();
~~~





