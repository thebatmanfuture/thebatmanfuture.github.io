https://www.yuque.com/chengxuyuanyideng/wmiqe2

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/124204/27/34471/7446/64b10277Ff7014e7a/3f837dffeb785de9.jpg)

最基础-引入FXMLLoader类模板fxml

~~~java
package com.example.fofa_search3;

import javafx.application.Application;
import javafx.fxml.FXMLLoader;
import javafx.scene.Scene;
import javafx.scene.image.Image;
import javafx.scene.image.ImageView;
import javafx.scene.layout.HBox;
import javafx.stage.Stage;

import java.io.FileInputStream;
import java.io.IOException;

public class HelloApplication extends Application {
    @Override
    public void start(Stage stage) throws IOException {


        FXMLLoader fxmlLoader = new FXMLLoader(HelloApplication.class.getResource("hello-view.fxml"));
        Scene scene = new Scene(fxmlLoader.load(), 900, 700);
        stage.setTitle("Fofa_Search v1.0.0 By 塔菲@batmanfuture");
        stage.setScene(scene);
        stage.show();
    }

    public static void main(String[] args) {
        launch();
    }
}
~~~



加背景-有深浅

1000 * 700

~~~java
package com.example.fofa_search3;

import javafx.application.Application;
import javafx.fxml.FXMLLoader;
import javafx.scene.Scene;
import javafx.scene.effect.ColorAdjust;
import javafx.scene.image.Image;
import javafx.scene.image.ImageView;
import javafx.scene.layout.HBox;
import javafx.stage.Stage;

import java.io.FileInputStream;
import java.io.IOException;

public class HelloApplication extends Application {
    @Override
    public void start(Stage primaryStage) throws IOException {

        primaryStage.setTitle("Fofa_Search v1.0.0 By 塔菲@batmanfuture");

        Scene scene = new Scene(hbox, 1000, 700);
        primaryStage.setScene(scene);
        primaryStage.show();
    }

    public static void main(String[] args) {
        launch();
    }
}
~~~

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/92998/31/37794/56054/647d93f5F4a23971d/11dd9e4a64e9ee41.jpg)



加载顺序

通过main()执行Application的launch(String str)方法，launch(String str)方法会默认执行本类下的init()、start()、stop()方法

我们通常在start()放在里写

在init()方法内读取配置文件的信息，比如读取fofa apikey，邮箱









~~~java
package com.example.fofa_search3;

import javafx.application.Application;
import javafx.fxml.FXMLLoader;
import javafx.scene.Scene;
import javafx.scene.effect.ColorAdjust;
import javafx.scene.image.Image;
import javafx.scene.image.ImageView;
import javafx.scene.layout.HBox;
import javafx.stage.Stage;

import java.io.FileInputStream;
import java.io.IOException;

public class HelloApplication extends Application {
    @Override
    public void start(Stage stage) throws IOException {
		Label label = new Label("hello world!");	//控件
        BorderPane pane = new BorderPane(label);	//布局:控件放在布局内
        Scene scene = new Scene(pane, 300, 200);	//场景:布局放在场景内
        stage.setScene(scene);						//舞台:场景放在舞台内
        stage.setTitle("我是窗口");
        stage.show();
        
    }

    public static void main(String[] args) {
        launch();
    }
}
~~~

加icon

~~~java
stage.getIcons().add(new Image("images/ddddddddd.png"));
~~~









































