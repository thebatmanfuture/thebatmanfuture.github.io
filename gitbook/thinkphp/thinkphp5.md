### thinkphp5

~~~http
composer create-project topthink/think=5.0.* tp5  --prefer-dist
~~~



~~~
http://localhost/thinkphp/tp5/public/
~~~

![cdf13574d377bd40ae34e88a973162e9.png](https://s1.imagehub.cc/images/2023/02/02/cdf13574d377bd40ae34e88a973162e9.png)

~~~php
project  应用部署目录
├─application           应用目录（可设置）
│  ├─common             公共模块目录（可更改）
│  ├─index              模块目录(可更改)
│  │  ├─config.php      模块配置文件
│  │  ├─common.php      模块函数文件
│  │  ├─controller      控制器目录
│  │  ├─model           模型目录
│  │  ├─view            视图目录
│  │  └─ ...            更多类库目录
│  ├─command.php        命令行工具配置文件
│  ├─common.php         应用公共（函数）文件
│  ├─config.php         应用（公共）配置文件
│  ├─database.php       数据库配置文件
│  ├─tags.php           应用行为扩展定义文件
│  └─route.php          路由配置文件
├─extend                扩展类库目录（可定义）
├─public                WEB 部署目录（对外访问目录）
│  ├─static             静态资源存放目录(css,js,image)
│  ├─index.php          应用入口文件
│  ├─router.php         快速测试文件
│  └─.htaccess          用于 apache 的重写
├─runtime               应用的运行时目录（可写，可设置）
├─vendor                第三方类库目录（Composer）
├─thinkphp              框架系统目录
│  ├─lang               语言包目录
│  ├─library            框架核心类库目录
│  │  ├─think           Think 类库包目录
│  │  └─traits          系统 Traits 目录
│  ├─tpl                系统模板目录
│  ├─.htaccess          用于 apache 的重写
│  ├─.travis.yml        CI 定义文件
│  ├─base.php           基础定义文件
│  ├─composer.json      composer 定义文件
│  ├─console.php        控制台入口文件
│  ├─convention.php     惯例配置文件
│  ├─helper.php         助手函数文件（可选）
│  ├─LICENSE.txt        授权说明文件
│  ├─phpunit.xml        单元测试配置文件
│  ├─README.md          README 文件
│  └─start.php          框架引导文件
├─build.php             自动生成定义文件（参考）
├─composer.json         composer 定义文件
├─LICENSE.txt           授权说明文件
├─README.md             README 文件
├─think                 命令行入口文件
~~~

![50983457dfac6209e1f7a237c2a7ea6b.png](https://s1.imagehub.cc/images/2023/02/02/50983457dfac6209e1f7a237c2a7ea6b.png)

```
http://localhost/thinkphp/tp5/public/index.php/index/index/test/id/3
路径 + 入口文件 + 模块 + 控制器类 + 方法 + 变量 + 值

http://localhost/thinkphp/tp5/public/index.php?s=index/index/test/id/3
```







### thinkphp3.2.3

![fea06b67c0293e2d58f32f6633a87cd6.png](https://s1.imagehub.cc/images/2023/02/02/fea06b67c0293e2d58f32f6633a87cd6.png)

~~~
www  WEB部署目录（或者子目录）
├─index.php       入口文件
├─README.md       README文件
├─Application     应用目录
├─Public          资源文件目录
└─ThinkPHP        框架目录
~~~



~~~
├─ThinkPHP 框架系统目录（可以部署在非web目录下面）
│  ├─Common       核心公共函数目录
│  ├─Conf         核心配置目录 
│  ├─Lang         核心语言包目录
│  ├─Library      框架类库目录
│  │  ├─Think     核心Think类库包目录
│  │  ├─Behavior  行为类库目录
│  │  ├─Org       Org类库包目录
│  │  ├─Vendor    第三方类库目录
│  │  ├─ ...      更多类库目录
│  ├─Mode         框架应用模式目录
│  ├─Tpl          系统模板目录
│  ├─LICENSE.txt  框架授权协议文件
│  ├─logo.png     框架LOGO文件
│  ├─README.txt   框架README文件
│  └─ThinkPHP.php 框架入口文件
~~~



#### A方法

![b4d3d9af7e2e8524959c83f3f06c5887.png](https://s1.imagehub.cc/images/2023/02/02/b4d3d9af7e2e8524959c83f3f06c5887.png)

![64075c912cb536898461259766a8107e.png](https://s1.imagehub.cc/images/2023/02/02/64075c912cb536898461259766a8107e.png)

A方法调用Usercontroller下的index方法

~~~
http://localhost/thinkphp/thinkphp_3.2.3/index.php/home/index/test123
~~~

![7effffc803bad625259cd1cf6978d1b5.png](https://s1.imagehub.cc/images/2023/02/02/7effffc803bad625259cd1cf6978d1b5.png)



或者直接R方法

~~~php
    public function test_R()
    {
        R('user/index');
    }
~~~



~~~
http://localhost/thinkphp/thinkphp_3.2.3/index.php/home/index/test_R
~~~























