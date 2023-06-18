https://www.yuque.com/chengxuyuanyideng/wmiqe2

最基础

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















