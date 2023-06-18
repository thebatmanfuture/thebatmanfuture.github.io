### Yii2框架

> PHP 5.4.0 或以上版本支持

安装方式：在你的yii文件夹下执行，这样安装的是稳定版

~~~http
composer create-project --prefer-dist yiisoft/yii2-app-basic basic
~~~

如果你在官网安最新版的会报版本错误

![](https://s3.bmp.ovh/imgs/2023/06/01/608a6c3728a08872.png)

~~~http
http://localhost/yii2/basic/web/index.php
~~~

![](https://s3.bmp.ovh/imgs/2023/06/01/69bc85dddce7b78c.png)

安装好之后第一步是查看yii2的版本，包括代码审计也是，先定位到版本，然后判断该版本是否存在某些漏洞

查看`\basic\vendor\yiisoft\yii2\BaseYii.php`，搜索version

![](https://s3.bmp.ovh/imgs/2023/06/01/73dd2ec5d7e12c2e.png)

~~~php
basic/                  应用根目录
    composer.json       Composer 配置文件, 描述包信息
    config/             包含应用配置及其它配置
        console.php     控制台应用配置信息
        web.php         Web 应用配置信息
    commands/           包含控制台命令类
    controllers/        包含控制器类
    models/             包含模型类
    runtime/            包含 Yii 在运行时生成的文件，例如日志和缓存文件
    vendor/             包含已经安装的 Composer 包，包括 Yii 框架自身
    views/              包含视图文件
    web/                Web 应用根目录，包含 Web 入口文件
        assets/         包含 Yii 发布的资源文件（javascript 和 css）
        index.php       应用入口文件
    yii                 Yii 控制台命令执行脚本
~~~

代码审计时，定位到`config/db.php`查看是不是root权限连的，还有就是查看mysql是不是小于等于5.6

web目录是可以对外访问的

![](https://s3.bmp.ovh/imgs/2023/06/02/8876d4da0c1bf3db.png)

执行流程

![](https://s3.bmp.ovh/imgs/2023/06/02/0fb248bd8cd0b66e.png)

~~~php
http://localhost/yii2/basic/web/index.php?r=site/index
http://localhost/yii2/basic/web/index.php
~~~

增加一个hello world

![](https://s3.bmp.ovh/imgs/2023/06/02/c4d79d45c3cdda1c.png)

![](https://s3.bmp.ovh/imgs/2023/06/02/c716c45f57c2f38b.png)

这里render到index就是渲染到如下views/site/index.php视图

![](https://s3.bmp.ovh/imgs/2023/06/02/e58e1dcd37bd77ca.png)

这里我们的新加的控制器必须是xxxController.php，方法必须是actionxxx，驼峰命名

~~~php
<?php
namespace app\controllers;
use Yii;
use yii\filters\AccessControl;
use yii\web\Controller;
use yii\web\Response;
use yii\filters\VerbFilter;
use app\models\LoginForm;
use app\models\ContactForm;

class HomeController extends Controller{
    public function actionHaha()
    {
        echo "123";
    }
}
~~~

![](https://s3.bmp.ovh/imgs/2023/06/02/ad08e14ccf4e1c6e.png)

GET方式

~~~php
class HomeController extends Controller{
    public function actionHaha()
    {
        $request = \Yii::$app->request;
        echo $request->get('id');
    }
}
~~~

![](https://s3.bmp.ovh/imgs/2023/06/02/4625fa09bc9bb6ca.png)

post方式

~~~php
class HomeController extends Controller{
    public function actionHaha()
    {
        $request = \Yii::$app->request;
        $username = $request->post('id','hhhhhhhhhhhhhhhhh');
        echo $username;
    }
}
~~~

渲染方式

~~~php
    public function actionRender()
    {
        $request = \Yii::$app->request;

        $data = $request->get('data','hhhhhhhhhhhhhhhhh');
        return $this->renderPartial('render', ['data'=>$data]); // 不会渲染父模板
        //return $this->render('render',compact('user','article')); // 会渲染父模板

    }
~~~

~~~php
<h1>hello</h1>
<!--data = 123;phpinfo();-->
<h2><?php echo $data;?></h2>
~~~

自定义防XSS

方式一

~~~php
<?php
use \yii\helpers\Html;

<h1>hello index</h1>
<p><?= Html::encode($str);?></p>
    
?>
~~~

方式二

~~~php
<?= HtmlPurifier::process($model->content) ?>
~~~

~~~php
public function actionCreate()
{
    $model = new Post();

    if ($model->load(Yii::$app->request->post()) && $model->save()) {
        // ...
    }
    return $this->render('create', [
        'model' => $model,
    ]);
}
~~~





















