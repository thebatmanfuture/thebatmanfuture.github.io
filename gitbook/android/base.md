思维导图

https://www.processon.com/view/link/62427cb2e0b34d0730e20e00

### APP开发基础

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/108583/40/41834/31602/64bf7314F5d7439d0/6ca75429da9d1b98.jpg)

#### App的运行日志

Android不使用System.out.println，而是采用Log工具打印日志

Log工具将各类日志划分为 5 个等级，每个等级的重要性是不一样的，这些日志等级按照从高到低的顺序依次说明如下：

~~~
Log.e：表示错误信息，比如可能导致程序崩溃的异常。

Log.w：表示警告信息。

Log.i：表示一般消息。

Log.d：表示调试信息，可把程序运行时的变量值打印出来，方便跟踪调试。

Log.v：表示冗余信息。
~~~

示例代码

~~~java
package com.example.myapplicationjava;

import androidx.appcompat.app.AppCompatActivity;

import android.os.Bundle;
import android.util.Log;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        Log.d("ning","onCreate");
    }
}
~~~

运行之后在日志这里搜索onCreate就能找到

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/170153/13/38423/97360/64b7963eF156e2afb/0ce7d68cdfcc7c63.jpg)

.

#### USB调试

手机开启调试之后进行安装，通过adb调试，android debug briage

![Screenshot_20230719_160709_com.huawei.appmarket.jpg](https://img13.360buyimg.com/ddimg/jfs/t1/158774/15/38617/10246/64b799c7Fc3f18a2b/463fbe96b02f6617.jpg)

安装后打开

![Screenshot_20230719_160717_com.example.myapplicat.jpg](https://img13.360buyimg.com/ddimg/jfs/t1/101675/20/41793/5192/64b799e9F6d691b42/a8037b1416e5f205.jpg)

项目和模块

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/167215/8/38529/28611/64ba4ad3F6e923640/92d6d83bc5d3d81b.jpg)

切换到android视图之后，有app和gradle scripts两个模块

![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/99823/34/42422/59077/64ba4b12Fddd7c1fd/4110c1685d6966b8.jpg)

如图

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/176055/21/38892/10602/64ba4b54Fdd3ede66/5db66bb0a1638609.jpg)

app目录下有：

manifests下只有AndroidManifest.xml文件：app运行的配置文件，清单文件

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <application
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.MyApplicationjava"
        tools:targetApi="31">
        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
~~~

java子目录下下有三个包，源文件

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/200712/7/32533/58508/64ba4c62Fdb70b35a/63c825d0d5faae1e.jpg)







res

存放当前文件的资源文件

![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/205560/1/31991/50819/64ba4c8aFb3507b86/669c232a893f24b1.jpg)

drawable：存放图形描述文件与图片文件

layout：目录存放APP页面的布局文件

mipmap：存放app的启动图标

values：存放一些常量定义文件，例如字符串常量strings.xml



gradle scripts下面主要是工程的编译配置文件，主要有：

1）build.gradle，该文件分为项目级与模块级两种，用于描述APP工程的编译规则

2）proguard-rules.pro，该文件用于描述java代码的混淆规则

3）gradle.properties，该文件用户配置编译工程搜索命令行参数，一般不需要改动

4）settings.gradle，该文件配置了需要编译的哪些模块，include "app"，表示只编译app模块

5）local.properties，项目的本地配置文件，在工程编译时自动完成

 ![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/220315/29/32779/36643/64bf48b4Fab57b904/717f1beed8c3fd4f.jpg)

Gradle 是一个项目自动化构建工具，帮我们做了依赖、打包、部署、发布、各种渠道的
差异管理等工作。

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/163839/6/28699/35949/64bf4a3cF4a7045cc/2222992fa98d5bc3.jpg)

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/144528/12/29626/62206/64bf4ad7F22acec6a/d8993802ee529a8c.jpg)



如下

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/141014/26/37511/49976/64bf4b90Fc0dfee84/6cb0499c1252e631.jpg)

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/90253/36/42686/49716/64bf4b40Fc02ea7e1/30cd62d82e4aa41f.jpg)



build.gradle内的dependencies决定了导入的包在哪，如图

我们extends继承的是来自于androidx.appcompat下

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/194727/24/35210/52789/64bf4caaF7da595e2/b19375fb75f61c72.jpg)
![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/141041/17/37242/42587/64bf4cc0F2460a5c5/924a38dd59699655.jpg)

`D:\android_project\MyApplicationjava\app\src\main\AndroidManifest.xml`

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/193834/7/35418/83479/64bf4da3F903f4cf8/e97c5d854a3138d4.jpg)

这里的application

```
android:allowBackup="true"指是否允许备份
android:icon指在app手机屏幕上显示的图标
android:label指在app手机屏幕上显示的名称
android:roundIcon指在app手机屏幕上的圆角图标
android:theme指在app的显示风格
```



Activity 是一个应用程序组件，提供一个屏幕，用户可以用来交互为了完成某项任务

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/102976/22/33985/11944/64bf63e1F5f078ed9/244eb2ec6400ee4f.jpg)

`D:\android_project\MyApplicationjava\app\src\main\AndroidManifest.xml`

在application下还有activity节点，是活动页面的注册声明，只有在AndroidManifest.xml中正确配置了节点，才能访问时访问正确的活动页面，初始配置的MainActivity是App的默认主页，

~~~xml
        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
~~~









![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/199637/28/38163/23795/64bf4d6dF22e6eb96/2928a3ddc7a70714.jpg)



如下，安卓开发这种就是利用xml写前端activity，java代码写后端，这和jar文件开发是差不多的，页面用fxml写，后端用java写

。

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/199493/7/38846/39473/64ba4c10Fc365fbca/42b19f753cee44d0.jpg)

前端页面

原始代码

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello World!"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
~~~

更改代码

LinearLayout是线性布局，

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="vertical">

    <TextView
        android:id="@+id/tv"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello World!" />

    <Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="24dp"
        android:text="跳转" />

</LinearLayout>
~~~



#### 自定义创建APP页面

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/134467/6/37824/22379/64bf6c3aF86e02be1/45f21177be8cf3e4.jpg)



页面创建

1）MainActivity.java

~~~java
package com.example.myapplicationjava;

import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;

import androidx.appcompat.app.AppCompatActivity;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState); //继承舞台
        setContentView(R.layout.activity_main); //绑定activity_main.xml
        TextView tv = findViewById(R.id.tv); // 提取id属性，设置值
        tv.setText("你好，世界！");

        Button button = findViewById(R.id.button);
        button.setOnClickListener(new View.OnClickListener() {  //如果点击id所在的按钮，就跳转到class MainActivity2
            @Override
            public void onClick(View v) {
                Intent intent = new Intent();
                intent.setClass(MainActivity.this, MainActivity2.class);
                startActivity(intent);
            }
        });
    }
}
~~~



2）activity_main.xml

设置控件，tv属性和button属性，tv是TextView是视图展示，button是按钮控件

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="vertical">

    <TextView
        android:id="@+id/tv"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello World!" />

    <Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="跳转"/>

</LinearLayout>
~~~

3）MainActivity2.java

很简单，当MainActivity.java处理完之后跳转到MainActivity2.java，就给到视图

~~~java
package com.example.myapplicationjava;
import android.os.Bundle;

import androidx.annotation.Nullable;
import androidx.appcompat.app.AppCompatActivity;

public class MainActivity2 extends AppCompatActivity {

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main2);
    }
}

~~~

4）activity_main2.xml

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="vertical">
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/text2" />
</LinearLayout>
~~~

这里`android:text="@string/text2"`是定义了一个新的string类型，所以必须修改values/string.xml

5）strings.xml

~~~xml
<resources>
    <string name="app_name">孙雷</string>
    <string name="text2">Activity Main 2</string>
</resources>
~~~

点击"跳转"

![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/162218/29/39133/31727/64bf7172F337c20d3/b7919309cd054261.jpg)
![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/94107/27/41655/22647/64bf7176F23912b69/f9c0ee7d8c09a6c4.jpg)

.

### 简单控件

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/164661/7/39341/28148/64bf7358F3abe7581/4f9e9b4980b2c542.jpg)
![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/178120/24/35297/11519/64bf7367F6abad324/517e670d427c29a2.jpg)

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/206405/35/32643/16467/64bf73efFf05c8949/4f8d21454292e8d5.jpg)

1）文本显示

设置文本内容的两种方式

在 XML 文件中通过属性 android:text 设置文本，这里的`android:text="@string/text2"`

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="vertical">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/text2" />
</LinearLayout>
~~~

在 Java 代码中调用文本视图对象的 setText 方法设置文本，这里的`tv.setText`

~~~java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState); //继承舞台
        setContentView(R.layout.activity_main); //绑定activity_main.xml
        TextView tv = findViewById(R.id.tv); // 提取id属性，设置值
        tv.setText("你好，世界！");
    }
}
~~~

2）引用字符串资源

在XML文件中引用（@string/***）

~~~xml
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/text2" />
~~~

 在Java代码中引用（R.string.***）

~~~java
        // 通过R.string引用获取字符串资源，并将其赋值给变量
        String text2 = getString(R.string.text2);
        // 将获取到的字符串资源显示在TextView上
        TextView textView = findViewById(R.id.tv);
        textView.setText(text2);
~~~

3）设置文本的大小

 在 Java 代码中调用 setTextSize 方法，即可指定文本大小

~~~java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState); //继承舞台
        setContentView(R.layout.activity_main); //绑定activity_main.xml
        TextView tv = findViewById(R.id.tv); // 提取id属性，设置值
        tv.setText("你好，世界！");
        tv.setTextSize(30);
~~~

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/186022/37/35273/68121/64c5ffadF4fd181e4/5d84d374c03cf11a.jpg)

在 XML 文件中则通过属性 android:textSize 指定文本大小

需要指定字号单位

~~~
px：它是手机屏幕的最小显示单位，与设备的显示屏有关。
dp：它是与设备无关的显示单位，只与屏幕的尺寸有关。
sp：它专门用来设置字体大小，在系统设置中可以调整字体大小。
~~~

~~~xml
    <TextView
        android:id="@+id/tv"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello World!"
        android:textSize="50dp" />
~~~

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/93857/1/38000/40387/64c60019F31c6b9b4/7baba94caa945c35.jpg)

.

4）设置颜色

在 Java 代码中调用 setTextColor 方法即可设置文本颜色，具体色值可从 Color 类取

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/186960/28/31713/23861/64c602fdFa6e8e42b/1c533eddd3511f3f.jpg)

~~~JAVA
import static android.graphics.Color.RED;

        super.onCreate(savedInstanceState); //继承舞台
        setContentView(R.layout.activity_main); //绑定activity_main.xml
        TextView tv = findViewById(R.id.tv); // 提取id属性，设置值
        tv.setText("你好，世界！");
        tv.setTextColor(RED);
~~~

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/153533/31/30009/78242/64c60359F487f84f9/e7435a1fb00e4a75.jpg)

~~~java
        tv.setTextColor(0xff00ff00);
~~~

![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/122523/28/37627/48226/64c604a9Feedbec3d/561b9fc8219983c6.jpg)

在XML文件中则通过属性android:textColor指定文本颜色，色值由透明度alpha和RGB
三原色（红色red、绿色green、蓝色blue）联合定义。

~~~JAVA
    <TextView
        android:id="@+id/tv"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello World!"
        android:textSize="50dp"
        android:textColor="#00ff00"/>
~~~

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/183903/36/35409/53992/64c60521F010c1c48/0e4080b3584cbdea.jpg)

引用颜色资源

在`res/values/colors.xml`中

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="black">#FF000000</color>
    <color name="white">#FFFFFFFF</color>
    <color name="green">#00ff00</color>
</resources>
~~~

`res/layout/activity_main.xml`

~~~xml
   <TextView
        android:id="@+id/tv"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello World!"
        android:textSize="50dp"
        android:textColor="@color/green"/>
~~~

![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/202967/40/37241/51733/64c60627Feaf575c9/f910cbe74bae52d5.jpg)

背景颜色

~~~java
tv.setBackgroundColor(RED);
~~~

![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/177572/40/35669/53415/64c606d0Fe1433767/f2ddc6a5e6049192.jpg)



设置视图的宽高

视图宽度通过属性android:layout_width表达，视图高度通过属性android:layout_heig

ht表达，宽高的取值主要有下列三种：

match_parent：表示与上级视图保持一致。

 wrap_content：表示与内容自适应。

 以dp为单位的具体尺寸。

~~~xml
    <TextView
        android:id="@+id/tv"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello World!"
        android:textSize="50dp"
        android:textColor="@color/green"/>
~~~

在代码中设置视图宽高

首先确保XML中的宽高属性值为wrap_content，接着打开该页面对应的Java代码，依序
执行以下三个步骤：

调用控件对象的getLayoutParams方法，获取该控件的布局参数。

布局参数的width属性表示宽度，height属性表示高度，修改这两个属性值。

调用控件对象的setLayoutParams方法，填入修改后的布局参数使之生效。

~~~java
        ViewGroup.LayoutParams params = tv.getLayoutParams();
        params.height = 0;
        tv.setLayoutParams(params);
~~~

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/203088/7/38362/57407/64c712bdFaaf4e221/5794b02228f1880c.jpg)

设置视图的间距

设置视图的间距有两种方式：

采用layout_margin属性，它指定了当前视图与周围平级视图之间的距离。包括layout_margin、
layout_marginLeft、layout_marginTop、layout_marginRight、layout_marginBottom

采用padding属性，它指定了当前视图与内部下级视图之间的距离。包括padding、paddingLef
t、paddingTop、paddingRight、paddingBottom



设置视图的对齐方式

设置视图的对齐方式有两种途径：
l 采用layout_gravity属性，它指定了当前视图相对于上级视图的对齐方式。
l 采用gravity属性，它指定了下级视图相对于当前视图的对齐方式。
Ø layout_gravity与gravity的取值包括：left、top、right、bottom，还可以用竖线连接各
取值，例如“left|top”表示即靠左又靠上，也就是朝左上角对齐。

~~~xml
    <!-- 第一个子布局背景为红色，它在上级视图中朝下对齐，它的下级视图则靠左对齐 -->
    <LinearLayout
        android:layout_width="0dp"
        android:layout_height="200dp"
        android:layout_gravity="bottom"
        android:layout_margin="10dp"
        android:layout_weight="1"
        android:background="#ff0000"
        android:padding="10dp"
        android:gravity="left">

        <!-- 内部视图的宽度和高度都是100dp，且背景色为青色 -->
        <View
            android:layout_width="100dp"
            android:layout_height="100dp"
            android:background="#00ffff"/>

    </LinearLayout>
    <!-- 第二个子布局背景为红色，它在上级视图中朝上对齐，它的下级视图则靠右对齐 -->
    <LinearLayout
        android:layout_width="0dp"
        android:layout_height="200dp"
        android:layout_gravity="top"
        android:layout_margin="10dp"
        android:layout_weight="1"
        android:background="#ff0000"
        android:padding="10dp"
        android:gravity="right">

        <View
            android:layout_width="100dp"
            android:layout_height="100dp"
            android:background="#00ffff"/>

    </LinearLayout>
~~~



线性布局LinearLayout

线性布局内部的各视图有两种排列方式：
orientation属性值为horizontal时，内部视图在水平方向从左往右排列。
orientation属性值为vertical时，内部视图在垂直方向从上往下排列。
如果不指定orientation属性，则LinearLayout默认水平方向排列。

~~~xml
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal">

        <TextView
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="横排第一个"
            android:textSize="17sp"
            android:textColor="#000000"/>

        <TextView
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="横排第二个"
            android:textSize="17sp"
            android:textColor="#000000"/>

    </LinearLayout>
~~~

![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/118794/25/39898/76219/64c71592F6800df85/699f144f8612489b.jpg)



相对布局RelativeLayout

相对布局的下级视图位置由其他视图决定。用于确定下级视图位置的参照物分两种：

与该视图自身平级的视图；

该视图的上级视图（也就是它归属的RelativeLayout）

如果不设定下级视图的参照物，那么下级视图默认显示在RelativeLayout内部的左上角。

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/124988/39/35996/60722/64c71635F70ed9190/6822cac789588876.jpg)

网格布局GridLayout

网格布局支持多行多列的表格排列。

网格布局默认从左往右、从上到下排列，它新增了两个属性：

columnCount属性，它指定了网格的列数，即每行能放多少个视图；rowCount属性，它指定了网格的行数，即每列能放多少个视图；

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/102387/18/42275/6693/64c716d2F5d4e81bf/9c7c8ee815aa1a67.jpg)



滚动视图ScrollView

滚动视图有两种：

ScrollView，它是垂直方向的滚动视图；垂直方向滚动时，layout_width属性值设置为match_p
arent，layout_height属性值设置为wrap_content。

HorizontalScrollView，它是水平方向的滚动视图；水平方向滚动时，layout_width属性值设置
为wrap_content，layout_height属性值设置为match_parent。





![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/177071/31/35932/19354/64c76350Fdf8b1914/4e5a1d7549c6915b.jpg)

button按钮

按钮控件Button由TextView派生而来，它们之间的区别有

 Button拥有默认的按钮背景，而TextView默认无背景；

Button的内部文本默认居中对齐，而TextView的内部文本默认靠左对齐；

Button会默认将英文字母转为大写，而TextView保持原始的英文大小写；

与TextView相比，Button增加了两个新属性：

textAllCaps属性，它指定了是否将英文字母转为大写，为true是表示自动转为大写，为false表
示不做大写转换。

 onClick属性，它用来接管用户的点击动作，指定了点击按钮时要触发哪个方法；

~~~xml
    <Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="跳转"/>
~~~

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/125777/38/35862/76878/64c764cbF6dadc274/6f5ea299cd8778de.jpg)



点击事件和长按事件

监听器，意思是专门监听控件的动作行为。只有控件发生了指定的动作，监听器才会触发
开关去执行对应的代码逻辑

按钮控件有两种常用的监听器：

点击监听器，通过setOnClickListener方法设置。按钮被按住少于500毫秒时，会触发点击事件。
长按监听器，通过setOnLongClickListener方法设置。按钮被按住超过500毫秒时，会触发长按
事件。

首先定义一个button

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/223236/31/26395/43359/64c766c0Fd177c169/31675fda85f80ee6.jpg)

点击之后调用MainActivity2的onCreate方法

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/161886/26/39003/55945/64c766dfFe0f347a3/cfc28c10d3d70eda.jpg)

onCreate方法

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/170517/2/39022/30381/64c7671fFbefbba15/90a6a510ef65de49.jpg)



禁用与恢复

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/169610/24/38994/44289/64c769bdF114e4aee/83725076f6516a57.jpg)

图像视图ImageView

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/117549/11/37168/41239/64c76b50F318bb209/66b8e3878b6a2c64.jpg)

~~~xml
    <ImageView
        android:id="@+id/iv_scale"
        android:layout_width="match_parent"
        android:layout_height="220dp"
        android:layout_marginTop="5dp"
        android:src="@drawable/apple"
        android:scaleType="centerInside"/>
~~~

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/161022/16/38892/83115/64c76d35Ff050ea8e/db8b1e84e2303fb2.jpg)

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/125688/16/39459/54225/64c76d8dF9b6ccdb7/217da66d2f545ee5.jpg)

。

图像按钮ImageButton

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/219649/22/33840/49123/64c76e9dFdbc2c85f/c738a65ac632ae6d.jpg)

~~~xml
    <ImageButton
        android:layout_width="match_parent"
        android:layout_height="80dp"
        android:scaleType="fitCenter"
        android:src="@drawable/sqrt" />
~~~

![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/166000/12/35414/57757/64c770e4F2f59340c/18f92ea47d0bc009.jpg)

计算器

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/144910/1/38442/8434/64c77132F67121230/9b39c23a1ddcefa5.jpg)

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/128206/27/36744/27482/64c7715eFcf24ade0/c94840712a04e1c8.jpg)

从上到小由三部分组成，文本一，文本二，表格布局

表格布局内部大部分都是按钮button，有部分是image button

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/92043/17/30039/65742/64c79358F7a0d2fd5/b2bfa066d722d26a.jpg)

我们首先编写xml前台展示页面

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/171408/9/39350/94600/64c7977fF9666d3e9/ceed3b2799c75d7d.jpg)

这里就算第一行文本框，居中

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/192406/39/35786/34855/64c797a1Ff10eb12e/b19e91c2102e9745.jpg)
![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/140150/5/38141/92061/64c797b5F07506f22/e59aa1ff33e9b5c6.jpg)

第二行文本框需要接收用户点击按钮的事件，然后将结果展示，所以我们设置id，初始没有点击时令text=0，

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/218964/32/32802/78999/64c79832F59555432/41bb56bda6199f4b.jpg)

然后是第三个网格布局

网格布局这里定义了是4列5行

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/197707/5/38061/74612/64c798ebF5e41e9db/489e355c8c9b9ca4.jpg)

这里由于这个符号无法正常展示，所以是以图片button方式展示的

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/105610/33/42258/87568/64c79928Fdd98ae90/29f3a2fa341438dc.jpg)

所以这里在onCreate方法中先注册了点击监听器事件，然后再写onClick方法，当按钮点击时，

这里如果判断是那个imagebutton就给特定符号，负责就取本身符号带入计算

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/129659/16/32684/84129/64c79b0eF5fdef4c5/9dca2e7db78c1e94.jpg)

再判断，如果你点击的既不是btn_clear（清0）或者如下

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/168104/32/35669/110753/64c79bf6F65bd1243/9f05583eccc43afe.jpg)

其中clear方法是清0，refreshOperate刷新运算结果，refreshText刷新文本显示

 

### 活动Activity

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/167850/9/29180/29413/64c79da6F32018c84/164bee984ef9e51b.jpg)

Activity 的启动和结束

从当前页面跳到新页面，跳转代码如下：`startActivity(new Intent(源页面.this, 目标页面.class));`

从当前页面回到上一个页面，相当于关闭当前页面，返回代码如下：` finish();`// 结束当前的活动页面

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/90119/16/38348/23759/64c79e7dF3079739a/7e84ededf4aa76a1.jpg)

如果我们实现一个简单的页面跳转，首先在xml里先设置一个button

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center">

    <Button
        android:id="@+id/btn_act_next"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="跳到下个页面" />

</LinearLayout>
~~~

java代码如下，我们在oncreate方法内设置监听器，然后触发监听器后onclick方法那日不通过intent实现跳转

~~~java
public class JumpFirstActivity extends AppCompatActivity implements View.OnClickListener {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_jump_first);
        findViewById(R.id.btn_jump_second).setOnClickListener(this);
    }

    @Override
    public void onClick(View v) {
        // 创建一个意图对象，准备跳到指定的活动页面
        Intent intent = new Intent(this, JumpSecondActivity.class);
        // 栈中存在待跳转的活动实例时，则重新创建该活动的实例，并清除原实例上方的所有实例
        intent.setFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
        startActivity(intent);
    }
}
~~~

在JumpSecondActivity方法内部之前写xml，我们想设置成如下，点击 < 可以返回，点击完成可以返回，点击手机的返回可以返回

![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/97440/11/30288/7227/64c7a0a2Faee5b97b/801e64cc2e069a42.jpg)

xml如下，这里ImageView是一个图片

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <ImageView
        android:id="@+id/iv_back"
        android:layout_width="40dp"
        android:layout_height="40dp"
        android:padding="5dp"
        android:src="@drawable/ic_back" />

    <Button
        android:id="@+id/btn_finish"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:text="完成" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="按返回键，或者点击左上角的箭头图标，或者点击上面的完成按钮，均可关闭当前页面、返回上个页面" />

</LinearLayout>
~~~

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/147732/28/37005/58282/64c7a1eaF7d6c490a/ee5c696afe895f4a.jpg)

java代码如下，

~~~java
public class ActFinishActivity extends AppCompatActivity implements View.OnClickListener {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_act_finish);
        findViewById(R.id.iv_back).setOnClickListener(this);
        findViewById(R.id.btn_finish).setOnClickListener(this);
    }

    @Override
    public void onClick(View v) {
        if (v.getId() == R.id.iv_back || v.getId() == R.id.btn_finish) {
            // 结束当前的活动页面
            finish();
        }
    }
}
~~~



生命周期

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/90119/16/38348/23759/64c79e7dF3079739a/7e84ededf4aa76a1.jpg)

`MainActivity` 类是 Android 应用程序中的一个活动（Activity），它是应用程序的主要入口点之一。以下是 `MainActivity` 类内部的几个常见方法及其作用：

1. `onCreate(Bundle savedInstanceState)`
   - 作用：当活动被创建时调用，通常在此方法中进行初始化操作。
   - 参数：
     - `savedInstanceState`：包含活动之前保存的状态的 `Bundle` 对象。
2. `onStart()`
   - 作用：当活动变为可见但还没有进入前台时调用。
   - 注意：在此方法中不应做耗时操作，因为该方法应尽快返回。
3. `onResume()`
   - 作用：当活动处于前台并与用户交互时调用。
   - 注意：这是处理与用户输入和交互的主要方法。
4. `onPause()`
   - 作用：当另一个活动获得焦点或当前活动即将停止时调用。
   - 注意：在此方法中应暂停或释放与用户交互相关的资源。
5. `onStop()`
   - 作用：当活动不再可见时调用。
   - 注意：在此方法中应释放占用的资源，因为活动可能会被销毁。
6. `onDestroy()`
   - 作用：当活动被销毁时调用。
   - 注意：在此方法中应释放所有资源、取消注册广播接收器等清理操作。

![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/145392/22/39556/65691/64c7b2b0Fcc65e75b/a8ca34cfc08196be.jpg)

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/164383/16/37973/33762/64c7b316F9b019bc0/bf69d2f973f055ee.jpg)

启动模式

![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/174591/23/39015/42147/64c7b356Fb00609d0/70f8a3180b99d984.jpg)

设置activity启动模式

1．在配置文件中指定启动模式

打开AndroidManifest.xml，给activity节点添加属性android:launchMode，属性值填入standard表示
采取标准模式，当然不添加属性的话默认就是标准模式。具体的activity节点配置内容示例如下：

~~~xml
<activity android:name=".JumpFirstActivity" android:launchMode="standard" />
~~~

#### 启动模式

![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/117951/16/39970/63339/64c8da30F1e5ac763/b459f88d4ba8c4b0.jpg)

###### 默认启动模式 standard

该模式可以被设定，不在 manifest 设定时候，Activity 的默认模式就是 standard。在该模式下，启动
的 Activity 会依照启动顺序被依次压入 Task 栈中：

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/198800/24/39149/20467/64c8d63bF1573de8e/46c92579be5c4b4d.jpg)

###### 栈顶复用模式 singleTop

在该模式下，如果栈顶 Activity 为我们要新建的 Activity（目标Activity），那么就不会重复创建新的
Activity。

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/184376/20/35936/22175/64c8d66dF772209d9/14ee74bfcf165a32.jpg)

应用场景
适合开启渠道多、多应用开启调用的 Activity，通过这种设置可以避免已经创建过的 Activity 被重复创
建，多数通过动态设置使用。



###### 栈内复用模式 singleTask

与 singleTop 模式相似，只不过 singleTop 模式是只是针对栈顶的元素，而 singleTask 模式下，如果
task 栈内存在目标 Activity 实例，则将 task 内的对应 Activity 实例之上的所有 Activity 弹出栈，并将对
应 Activity 置于栈顶，获得焦点。

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/180785/19/35965/16541/64c8d6b2Ff12031ee/15bf65c6144ec1df.jpg)

应用场景
程序主界面：我们肯定不希望主界面被创建多次，而且在主界面退出的时候退出整个 App 是最好的效
果。
耗费系统资源的Activity：对于那些及其耗费系统资源的 Activity，我们可以考虑将其设为 singleTask
模式，减少资源耗费。

###### 全局唯一模式 singleInstance

在该模式下，我们会为目标 Activity 创建一个新的 Task 栈，将目标 Activity 放入新的 Task，并让目标
Activity获得焦点。新的 Task 有且只有这一个 Activity 实例。 如果已经创建过目标 Activity 实例，则
不会创建新的 Task，而是将以前创建过的 Activity 唤醒。

![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/140635/1/37047/19468/64c8d6d4F775a26d3/680645870edf7952.jpg)



#### 启动模式的实际应用

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/181678/9/35860/52615/64c8daa1Fd5aec8ba/4e89d2589cb69e5c.jpg)

1．在两个活动之间交替跳转

对于不允许重复返回的情况，可以设置启动标志FLAG_ACTIVITY_CLEAR_TOP，即使活动栈里面存在待
跳转的活动实例，也会重新创建该活动的实例，并清除原实例上方的所有实例，保证栈中最多只有该活
动的唯一实例，从而避免了无谓的重复返回。于是活动A内部的跳转代码就改成了下面这般：

~~~java
public class JumpFirstActivity extends AppCompatActivity implements View.OnClickListener {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_jump_first);
        findViewById(R.id.btn_jump_second).setOnClickListener(this);
    }
    @Override
    public void onClick(View v) {
        // 创建一个意图对象，准备跳到指定的活动页面
        Intent intent = new Intent(this, JumpSecondActivity.class);
        // 栈中存在待跳转的活动实例时，则重新创建该活动的实例，并清除原实例上方的所有实例
        intent.setFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
        startActivity(intent);
    }
}
~~~

2．登录成功后不再返回登录页面

可以设置启动标志FLAG_ACTIVITY_CLEAR_TASK，该标志会清空当前活动栈里的所有实例。不过全部清空之后，意味着当前栈没法用了，必须另外找个活动栈才行，也就是同时设置启动标志FLAG_ACTIVITY_NEW_TASK，该标志用于开辟新任务的活动栈。于是离开登录页面的跳转代码变成下面这样：

~~~java
public class LoginInputActivity extends AppCompatActivity implements View.OnClickListener {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_login_input);
        findViewById(R.id.btn_jump_success).setOnClickListener(this);
    }

    @Override
    public void onClick(View v) {
        // 创建一个意图对象，准备跳到指定的活动页面
        Intent intent = new Intent(this, LoginSuccessActivity.class);
        // 设置启动标志：跳转到新页面时，栈中的原有实例都被清空，同时开辟新任务的活动栈
        intent.setFlags(Intent.FLAG_ACTIVITY_CLEAR_TASK | Intent.FLAG_ACTIVITY_NEW_TASK);
        startActivity(intent);
    }
}
~~~

显式intent和隐式intent

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/102520/24/42042/41491/64c8db36F53504b3b/68d0a0934ea890bb.jpg)

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/102818/23/26060/45923/64c8db82Fe977a2f4/8fa9784ea710f61e.jpg)

显式Intent

显式Intent，直接指定来源活动与目标活动，属于精确匹配。它有三种构建方式：

~~~
在Intent的构造函数中指定。
调用意图对象的setClass方法指定。
调用意图对象的setComponent方法指定。
~~~

1．显式Intent，直接指定来源活动与目标活动，属于精确匹配

（1）在Intent的构造函数中指定，示例代码如下：

~~~java
Intent intent = new Intent(this, ActNextActivity.class);  // 创建一个目标确定的意图
~~~

（2）调用意图对象的setClass方法指定，示例代码如下：

~~~java
Intent intent = new Intent();  // 创建一个新意图
intent.setClass(this, ActNextActivity.class); // 设置意图要跳转的目标活动
~~~

（3）调用意图对象的setComponent方法指定，示例代码如下：

~~~java
Intent intent = new Intent();  // 创建一个新意图
// 创建包含目标活动在内的组件名称对象
ComponentName component = new ComponentName(this, ActNextActivity.class);
intent.setComponent(component);  // 设置意图携带的组件信息
~~~

示例

~~~java
    @Override
    public void onClick(View v) {
        // 1.在Intent的构造函数中指定
        //Intent intent = new Intent(this, ActFinishActivity.class);
        // 2.调用意图对象的setClass方法指定
        Intent intent = new Intent();
        // intent.setClass(this,ActFinishActivity.class);
        // 3.调用意图对象的setComponent方法指定
        ComponentName component = new ComponentName(this, ActFinishActivity.class);
        intent.setComponent(component);
        startActivity(intent);
    }
~~~



2．隐式Intent，没有明确指定要跳转的目标活动，只给出一个动作字符串让系统自动匹配，属于模糊匹配

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/98033/22/42177/33492/64cb159cF48e9bf13/38425ead5f48aba3.jpg)

如下是调用拨号代码

~~~java
public class ActionUriActivity extends AppCompatActivity implements View.OnClickListener {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_action_uri);
        findViewById(R.id.btn_dial).setOnClickListener(this);
        findViewById(R.id.btn_sms).setOnClickListener(this);
        findViewById(R.id.btn_my).setOnClickListener(this);
    }

    @Override
    public void onClick(View v) {
        Intent intent = new Intent();
        String phoneNo = "12345";
        switch (v.getId()) {
            case R.id.btn_dial:
                // 设置意图动作为准备拨号
                intent.setAction(Intent.ACTION_DIAL);
                // 声明一个拨号的Uri
                Uri uri = Uri.parse("tel:" + phoneNo);
                intent.setData(uri);
                startActivity(intent);
                break;

            case R.id.btn_sms:
                // 设置意图动作为发短信
                intent.setAction(Intent.ACTION_SENDTO);
                // 声明一个发送短信的Uri
                Uri uri2 = Uri.parse("smsto:" + phoneNo);
                intent.setData(uri2);
                startActivity(intent);
                break;
            case R.id.btn_my:
                intent.setAction("android.intent.action.NING");
                intent.addCategory(Intent.CATEGORY_DEFAULT);
                startActivity(intent);
                break;
        }
    }
}
~~~



activity之间的数据传递

利用bundle

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/194345/8/35768/27479/64cb1947F30c3971a/186dbf4d24116372.jpg)

发送

~~~java
public class ActSendActivity extends AppCompatActivity implements View.OnClickListener {

    private TextView tv_send;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_act_send);
        tv_send = findViewById(R.id.tv_send);
        findViewById(R.id.btn_send).setOnClickListener(this);
    }

    @Override
    public void onClick(View v) {
        Intent intent = new Intent(this, ActReceiveActivity.class);
        // 创建一个新包裹
        Bundle bundle = new Bundle();
        bundle.putString("request_time", DateUtil.getNowTime());
        bundle.putString("request_content", tv_send.getText().toString());
        intent.putExtras(bundle);
        startActivity(intent);
    }
}
~~~

接收

~~~java
public class ActResponseActivity extends AppCompatActivity implements View.OnClickListener {

    private static final String mReponse = "我还没睡，我爸妈不在家。";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_act_response);
        TextView tv_request = findViewById(R.id.tv_request);
        // 从上一个页面传来的意图中获取快递包裹
        Bundle bundle = getIntent().getExtras();
        String request_time = bundle.getString("request_time");
        String request_content = bundle.getString("request_content");
        String desc = String.format("收到请求消息：\n请求时间为%s\n请求内容为%s", request_time, request_content);
        // 把请求消息的详情显示在文本视图上
        tv_request.setText(desc);

        findViewById(R.id.btn_response).setOnClickListener(this);

        TextView tv_response = findViewById(R.id.tv_response);
        tv_response.setText("待返回的消息为：" + mReponse);
    }

    @Override
    public void onClick(View v) {
        Intent intent = new Intent();
        Bundle bundle = new Bundle();
        bundle.putString("response_time", DateUtil.getNowTime());
        bundle.putString("response_content", mReponse);
        intent.putExtras(bundle);
        // 携带意图返回上一个页面。RESULT_OK表示处理成功
        setResult(Activity.RESULT_OK, intent);
        // 结束当前的活动页面
        finish();
    }
}
~~~

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/213105/39/36022/41606/64cb30adF9d7d9fee/9e8bda9487b69d6f.jpg)

发送

~~~java
public class ActRequestActivity extends AppCompatActivity implements View.OnClickListener {

    private static final String mRequest = "你睡了吗？来我家睡吧";
    private ActivityResultLauncher<Intent> register;
    private TextView tv_response;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_act_request);
        TextView tv_request = findViewById(R.id.tv_request);
        tv_request.setText("待发送的消息为：" + mRequest);

        tv_response = findViewById(R.id.tv_response);

        findViewById(R.id.btn_request).setOnClickListener(this);

        register = registerForActivityResult(new ActivityResultContracts.StartActivityForResult(), result -> {
            if (result != null) {
                Intent intent = result.getData();
                if (intent != null && result.getResultCode() == Activity.RESULT_OK) {
                    Bundle bundle = intent.getExtras();
                    String response_time = bundle.getString("response_time");
                    String response_content = bundle.getString("response_content");
                    String desc = String.format("收到返回消息：\n应答时间为%s\n应答内容为%s", response_time, response_content);
                    // 把返回消息的详情显示在文本视图上
                    tv_response.setText(desc);
                }
            }
        });
    }

    @Override
    public void onClick(View v) {
        Intent intent = new Intent(this, ActResponseActivity.class);
        // 创建一个新包裹
        Bundle bundle = new Bundle();
        bundle.putString("request_time", DateUtil.getNowTime());
        bundle.putString("request_content", mRequest);
        intent.putExtras(bundle);
        register.launch(intent);
    }
}
~~~

接收返回

~~~java
public class ActResponseActivity extends AppCompatActivity implements View.OnClickListener {

    private static final String mReponse = "我还没睡，我爸妈不在家。";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_act_response);
        TextView tv_request = findViewById(R.id.tv_request);
        // 从上一个页面传来的意图中获取快递包裹
        Bundle bundle = getIntent().getExtras();
        String request_time = bundle.getString("request_time");
        String request_content = bundle.getString("request_content");
        String desc = String.format("收到请求消息：\n请求时间为%s\n请求内容为%s", request_time, request_content);
        // 把请求消息的详情显示在文本视图上
        tv_request.setText(desc);

        findViewById(R.id.btn_response).setOnClickListener(this);

        TextView tv_response = findViewById(R.id.tv_response);
        tv_response.setText("待返回的消息为：" + mReponse);
    }

    @Override
    public void onClick(View v) {
        Intent intent = new Intent();
        Bundle bundle = new Bundle();
        bundle.putString("response_time", DateUtil.getNowTime());
        bundle.putString("response_content", mReponse);
        intent.putExtras(bundle);
        // 携带意图返回上一个页面。RESULT_OK表示处理成功
        setResult(Activity.RESULT_OK, intent);
        // 结束当前的活动页面
        finish();
    }
}
~~~





在代码中获取元数据

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/181589/4/35751/40671/64cb35abFf46b31e4/22bd67328572b0fa.jpg)

之前我们将变量什么的放在string.xml或者其他配置文件，这样当java代码在被编译后，该文件不会改变，但是如果为了安全性，例如我想访问到的这个不想被其他人访问到，那么就要通过元数据

打开AndroidManifest.xml，在测试活动的activity节点内部添加meta-data标签，通过属性name指定元数据
的名称，通过属性value指定元数据的值。仍以天气为例，添加meta-data标签之后的activity节点如下所
示：

~~~xml
<activity android:name=".MetaDataActivity">
  <meta-data android:name="weather" android:value="晴天" />
</activity>
~~~

元数据的value属性既可直接填字符串，也可引用strings.xml已定义的字符串资源，引用格式形如
“@string/字符串的资源名称”。下面便是采取引用方式的activity节点配置：

~~~xml
<activity android:name=".MetaDataActivity">
  <meta-data
       android:name="weather"
       android:value="@string/weather_str" />
</activity>
~~~

配置好了activity节点的meta-data标签，再回到Java代码获取元数据信息，获取步骤分为下列3步：
1）调用getPackageManager方法获得当前应用的包管理器。

2）调用包管理器的getActivityInfo方法获得当前活动的信息对象。

3）活动信息对象的metaData是Bundle包裹类型，调用包裹对象的getString即可获得指定名称的参数值。

~~~java
public class MetaDataActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_meta_data);
        TextView tv_meta = findViewById(R.id.tv_meta);
        // 获取应用包管理器
        PackageManager pm = getPackageManager();
        try {
            // 从应用包管理器中获取当前的活动信息
            ActivityInfo info = pm.getActivityInfo(getComponentName(), PackageManager.GET_META_DATA);
            // 获取活动附加的元数据信息
            Bundle bundle = info.metaData;
            String weather = bundle.getString("weather");
            tv_meta.setText(weather);
        } catch (PackageManager.NameNotFoundException e) {
            e.printStackTrace();
        }
    }
}
~~~



给页面注册快捷方式

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/182885/25/40183/34020/64cb372dF2b15dc73/c55a41db22a3ae6b.jpg)

如上图，快捷方式

首先在`AndroidManifest.xml`中定义如下

~~~xml
        <activity
            android:name=".ActStartActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>

            <meta-data
                android:name="android.app.shortcuts"
                android:resource="@xml/shortcuts" />
        </activity>
~~~



然后在xml中定义三个快捷方式

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<shortcuts xmlns:android="http://schemas.android.com/apk/res/android">

    <shortcut
        android:enabled="true"
        android:icon="@mipmap/ic_launcher"
        android:shortcutId="first"
        android:shortcutLongLabel="@string/first_long"
        android:shortcutShortLabel="@string/first_short">

        <intent
            android:action="android.intent.action.VIEW"
            android:targetClass="com.example.calc.ActStartActivity"
            android:targetPackage="com.example.calc" />
        <categories android:name="android.shortcut.conversation" />
    </shortcut>


    <shortcut
        android:enabled="true"
        android:icon="@mipmap/ic_launcher"
        android:shortcutId="second"
        android:shortcutLongLabel="@string/second_long"
        android:shortcutShortLabel="@string/second_short">

        <intent
            android:action="android.intent.action.VIEW"
            android:targetClass="com.example.calc.JumpFirstActivity"
            android:targetPackage="com.example.calc" />
        <categories android:name="android.shortcut.conversation" />
    </shortcut>

    <shortcut
        android:enabled="true"
        android:icon="@mipmap/ic_launcher"
        android:shortcutId="third"
        android:shortcutLongLabel="@string/third_long"
        android:shortcutShortLabel="@string/third_short">

        <intent
            android:action="android.intent.action.VIEW"
            android:targetClass="com.example.calc.LoginInputActivity"
            android:targetPackage="com.example.calc" />
        <categories android:name="android.shortcut.conversation" />
    </shortcut>
</shortcuts>
~~~

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/158900/9/37788/87251/64cb5016F8875d71b/af66e30ecb0623b5.jpg)

。

### 中级控件

###### 图形定制





























