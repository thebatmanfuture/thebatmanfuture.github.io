#### 多线程

main线程和 thread-0线程

1)

~~~java
//1.创建一个继承于Thread的子类
public class Hello{
    public static void main(String args[]){
        //3.创建thrad类的子类的对象
        PrintNumber p1 = new PrintNumber();
        //4.通过对象调用重写后的run()
        p1.start();// 这里调用了当前线程的run方法
        for(int i = 1;i <= 100; i++){
            if(i % 2 != 0){
                System.out.println(Thread.currentThread().getName() + ":" + i + "*****");
            }
        }
    }
}

//2.重写对thread类的run()
class PrintNumber extends Thread{
    @Override
    public void run(){
        for(int i = 1;i <= 100; i++){
            if(i % 2 == 0){
                System.out.println(Thread.currentThread().getName() + ":" + i + "*****");
            }
        }
    }
}

~~~

~~~
System.out.println(Thread.currentThread().getName() + ":" + i + "*****");
~~~

![](https://s3.bmp.ovh/imgs/2023/03/11/83aa15d3a2503199.png)

~~~
这里的p.start()是启动线程，如果不启动，直接调用p.run()则不好启动线程，那么只有一个主线程在运行；且不能让已经start的线程执行多步
~~~





2)

~~~java
//1.创建一个实现Runnable接口的类
//2.实现接口中的run方法，将此线程要执行的操作，声明在此方法体中
//3.创建当前实现类的对象
//4.将此对象作为参数传递到构造器中，创建thread类的实例
//5.通过该实例调用start()

class EvenNumberPrint implements Runnable{
    @Override
    public void run(){
        for (int i=1;i<100;i++){
            System.out.println(Thread.currentThread().getName() + ":" + i);
        }
    }
}

public class Hello{
    public static void main(String args[]){
        EvenNumberPrint p2 = new EvenNumberPrint();
        Thread p3 = new Thread(p2);
        p3.start();
    }
}
~~~

![](https://s3.bmp.ovh/imgs/2023/03/11/f37a158223334828.png)

拓展-匿名函数写法

~~~java
public class Hello{
    public static void main(String args[]){

        //我们可以写一个匿名函数
        new Thread(new Runnable(){
            public void run(){
                for (int i=1;i<100;i++){
                    System.out.println(Thread.currentThread().getName() + ":" + i);
                }
            }
        }).start();
    }
}
~~~

原理刨析

1.写匿名实现类的匿名对象，故我们不用实例化出一个对象p3然后再调用start()，可以直接new Thread(xxx).start()

2.Thread有一个构造器Runnable target，我们也不必引入p2，可以直接new EvenNumberPrint()，即

![](https://s3.bmp.ovh/imgs/2023/03/11/1dabf0018f1c7bdc.png)

相当于Thread p3 = new EvenNumberPrint();，而由于EvenNumberPrint类是继承Runnable接口，故我们可以直接写成new Runnable()，然后完善run()方法

![](https://s3.bmp.ovh/imgs/2023/03/11/57a361418c12a0c9.png)

![](https://s3.bmp.ovh/imgs/2023/03/11/f9d6e61cc2abb4cc.png)





~~~
两者比较：
共同点：
1.启动线程，使用的都是Thread类的start()
2.创建的线程对象，都是Thread类或其子类的实例
不同点：
一个是类的继承，一个是接口的实现
Runnable方法的好处：1)实现的方式，避免的类的单继承的局限性 2)更适合处理有数据共享的问题
3)代码和数据的分离
~~~

##### Thread类的常用方法

###### thread类的构造器

![](https://s3.bmp.ovh/imgs/2023/03/11/5a6d9e3d4e9369d7.png)

![](https://s3.bmp.ovh/imgs/2023/03/11/62a4615b432bd0b8.png)

1）空参构造器

~~~java
public Thread() {}
~~~

2）分配一个指定名字的新的线程对象

~~~java
public Thread(String name) {}
~~~

3）创建指定线程的目标对象，实现了Runnable接口中的run方法

~~~java
public Thread(Runnable target) {}
~~~

4）

~~~java
public Thread(Runnable target, String name) {}
~~~

实例演示

~~~java
class EvenNumberPrint extends Thread{

    public EvenNumberPrint(){
    }
    public EvenNumberPrint(String name){
        super(name);
    }
    public void run(){
        for (int i=1;i<100;i++){
            System.out.println(Thread.currentThread().getName() + ":" + i);
        }
    }
}
public class Hello{
    public static void main(String args[]){
        EvenNumberPrint p1 = new EvenNumberPrint("线程batmanfuture");
        p1.start();
    }
}
~~~

![](https://s3.bmp.ovh/imgs/2023/03/11/7f694795b3879863.png)

这里Thread类有getName()和setName()两个方法

我们通过getName()得到了主线程的名称，通用也可以通过setName()修改名称

![](https://s3.bmp.ovh/imgs/2023/03/11/983dc3f0c87dccda.png)

这里我们直接修改名称后调用p1.run()启动主线程

![](https://s3.bmp.ovh/imgs/2023/03/11/c66e7b6ac1674756.png)

![](https://s3.bmp.ovh/imgs/2023/03/11/bdbe53b378223bc3.png)

或者，想再修改子线程的名称则p1.setName("123456");

![](https://s3.bmp.ovh/imgs/2023/03/11/4e684798c1c4499f.png)

#### 路线

![](https://s3.bmp.ovh/imgs/2023/05/31/95b2535934d0990e.png)



#### 基础类和基础API

1）String

~~~java
// final表示不可以被继承，没有明显的父类，则继承Object
//且继承于Serializable接口(可序列化的)，凡是实现此接口的类，他的对象就可以序列化了
//凡是实现此接口的类都可以比较大小，实现了Comparable里的抽象方法
//字符序列的接口CharSequence
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
~~~

![](https://s3.bmp.ovh/imgs/2023/03/11/dcb9db5772a78c9c.png)

内部声明的属性

这里的value是由final修饰的，说明指明此value数组，一旦初始化，其地址就不可变

举个例子

~~~java
String arr[] = {1,2,3,4,5};
这里声明了一个字符串数组，长度被限定死了是5，由于这里的value数组是不可更改的，所以如果我们再声明一个6长度的数组，本质上新实例化了一个类
String arr1[] = {1,2,3,4,5,6}

~~~

字符串常量都存储在字符串常量池中（StringTable），不允许存放两个相同的字符串常量

![](https://s3.bmp.ovh/imgs/2023/03/11/340509efb01e9c0f.png)

![](https://s3.bmp.ovh/imgs/2023/03/11/980b40c4f818e76e.png)

。

String的不可变性

如果是修改s2的值，那么如下

![](https://s3.bmp.ovh/imgs/2023/03/11/7240498c6eae5ea2.png)

如果是修改

![](https://s3.bmp.ovh/imgs/2023/03/11/38b50dc5630aaad6.png)

String类的两种实例化声明方式

~~~java
String name = "hello";
String name = new String("hello");
~~~

























[![5lGko8.png](https://z3.ax1x.com/2021/10/14/5lGko8.png)](https://imgtu.com/i/5lGko8)

[![5lGrFO.png](https://z3.ax1x.com/2021/10/14/5lGrFO.png)](https://imgtu.com/i/5lGrFO)

~~~java
//编译文件
javac hello_world.java，生成helloWorld.class
//运行文件
java.exe helloWorld.class
~~~

~~~java
javadoc -d myHello -author -version HelloJava
~~~

#### JDBC

~~~
操作数据库的一套API
~~~

~~~
首先要导入mysql的jar包
~~~



~~~java
public class jdbc {
    public static void main(String[] args) throws ClassNotFoundException, SQLException {
        // 1.注册--下面这个代码在mysql5以后可以不用写了
        Class.forName("com.mysql.jdbc.Driver");
        // 2.获取连接
        // 如果连接的就是本机则可以简写：jdbc:mysql:///test
        String url = "jdbc:mysql://127.0.0.1:3306/test";
        String username = "root";
        String password = "root";
        Connection conn = DriverManager.getConnection(url,username, password);
        // 3.定义SQL语句
        String sql = "select * from ty_user";
        // 4.获取执行SQL的对象
        Statement stmt = conn.createStatement();
        // 5.执行SQL
        boolean count = stmt.execute(sql);
        System.out.println(count);
        // 6.释放资源
        stmt.close();
        conn.close();
    }
}

~~~

##### DriverManager

~~~
1.注册驱动 2.获取数据库连接
~~~

1.注册驱动

```
Class.forName("com.mysql.jdbc.Driver");
```

![](https://s3.bmp.ovh/imgs/2023/03/08/9ce40a59f8c9448f.png)

2.获取数据库连接

~~~java
String url = "jdbc:mysql://127.0.0.1:3306/test";
Connection conn = DriverManager.getConnection(url,username, password);
~~~

##### Connection

~~~
1.获取执行SQL的对象 2.事务管理
~~~

![](https://s3.bmp.ovh/imgs/2023/03/08/a5d50cd6f5603c08.png)

##### 基础的SQL注入

~~~java
package com.batmanfuture;

import com.mysql.jdbc.Driver;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.Scanner;

/*
*   batmanfuture
* */
public class jdbc {
    public static void main(String[] args) throws ClassNotFoundException, SQLException {
        // 1.注册--下面这个代码在mysql5以后可以不用写了
        Class.forName("com.mysql.jdbc.Driver");
        // 2.获取连接
        // 如果连接的就是本机则可以简写：jdbc:mysql:///test
        String url = "jdbc:mysql://127.0.0.1:3306/test";
        String username = "root";
        String password = "root";

        Connection conn = DriverManager.getConnection(url,username, password);

        String id;
        Scanner scanner = new Scanner(System.in);
        id = scanner.next();

        // 3.定义SQL语句
        String sql = "select * from ty_user where id = '" + id + "'";
        // 4.获取执行SQL的对象
        Statement stmt = conn.createStatement();
        // 5.执行SQL
        boolean count = stmt.execute(sql);
        System.out.println(count);
        // 6.释放资源
        stmt.close();
        conn.close();
    }
}
~~~

![](https://s3.bmp.ovh/imgs/2023/03/08/869ba0d820bcc9a1.png)

##### Statement

##### ResultSet

数据集

~~~java
public class jdbc {
    public static void main(String[] args) throws ClassNotFoundException, SQLException {
        // 1.注册--下面这个代码在mysql5以后可以不用写了
        Class.forName("com.mysql.jdbc.Driver");
        // 2.获取连接
        // 如果连接的就是本机则可以简写：jdbc:mysql:///test
        String url = "jdbc:mysql://127.0.0.1:3306/test";
        String username = "root";
        String password = "root";

        //Connection conn = DriverManager.getConnection(url,username, password);
        Connection conn = DriverManager.getConnection(url,username, password);


        // 3.定义SQL语句
        String sql = "select * from ty_user";

        // 4.获取执行SQL的对象
        Statement stmt = conn.createStatement();

        // 5.执行SQL
        ResultSet rs = stmt.executeQuery(sql);

        while(rs.next()){
            int i = rs.getInt(1);
            int j = rs.getInt(2);
            String username1 = rs.getString(3);
            String password1 = rs.getString(4);
            String name = rs.getString(5);
            int type = rs.getInt(6);
            int status = rs.getInt(7);


            System.out.println(i);
            System.out.println(j);
            System.out.println(username1);
            System.out.println(password1);
            System.out.println(name);
            System.out.println(type);
            System.out.println(status);
        }
    }
~~~

##### PrepareStatement

1.获取PrepareStatement的对象

~~~java
String sql = "select * from user where username = ? and password = ?";
PrepareStatement pstmt = conn.PrepareStatement(sql);
~~~

2.设置参数值

~~~java
~~~

3.执行SQL

~~~java
executeUpdate();// executeQuery();
~~~



##### 防上面那个基础SQL注入

~~~java
package com.batmanfuture;

import com.mysql.jdbc.Driver;

import java.sql.*;
import java.util.Scanner;

/*
*   batmanfuture
* */
public class jdbc {
    public static void main(String[] args) throws ClassNotFoundException, SQLException {
        // 1.注册--下面这个代码在mysql5以后可以不用写了
        Class.forName("com.mysql.jdbc.Driver");
        // 2.获取连接
        // 如果连接的就是本机则可以简写：jdbc:mysql:///test
        String url = "jdbc:mysql://127.0.0.1:3306/test";
        String username = "root";
        String password = "root";

        Connection conn = DriverManager.getConnection(url,username, password);

        String id;
        Scanner scanner = new Scanner(System.in);
        id = scanner.next();

        // 3.定义SQL语句
        String sql = "select * from ty_user where id = ?";
        // 4.获取执行SQL的对象
        PreparedStatement stmt = conn.prepareStatement(sql);
        // 5.执行SQL
        stmt.setString(1, id);


        ResultSet rs = stmt.executeQuery();
        while(rs.next()){
            int i = rs.getInt(1);
            int j = rs.getInt(2);
            String username1 = rs.getString(3);
            String password1 = rs.getString(4);
            String name = rs.getString(5);
            int type = rs.getInt(6);
            int status = rs.getInt(7);


            System.out.println(i);
            System.out.println(j);
            System.out.println(username1);
            System.out.println(password1);
            System.out.println(name);
            System.out.println(type);
            System.out.println(status);
        }
        // 6.释放资源
        stmt.close();
        conn.close();
    }
}
~~~

![](https://s3.bmp.ovh/imgs/2023/03/08/391835696c6a84a8.png)



开启预编译

~~~java
        String url = "jdbc:mysql://127.0.0.1:3306/test?userServerPrepStmts=true";
~~~



#### 数据库连接池

~~~
Druid
C3P0
DBCP
~~~

~~~java
public class Druid {
    public static void main(String[] args) throws Exception {
        // 1.导入jar包

        // 2.定义配置文件

        // 3.加载配置文件
        Properties prop = new Properties();
        prop.load(new FileInputStream("/idea/project/src/druid.properties"));
        // 4.获取连接池对象
        DataSource dataSource = DruidDataSourceFactory.createDataSource(prop);

        // 5.获取数据库连接Connection
        Connection connection = dataSource.getConnection();

        System.out.println(connection);


    }
}

~~~

#### Maven

![](https://s3.bmp.ovh/imgs/2023/03/11/f954d86664dee418.png)

仓库和坐标

https://mvnrepository.com/

settings.xml

~~~
<localRepository>D:\idea\IntelliJ IDEA 2021.3.3\plugins\maven\lib\maven3\mvn_resp</localRepository>
~~~

![49fcf4a337594a46413c0624fcd83bde.png](https://s1.imagehub.cc/images/2023/03/11/49fcf4a337594a46413c0624fcd83bde.png)

设置阿里云镜像

~~~xml
<mirrors>
   	<mirror>  
		 <id>maven-default-http-blocker</id>  
		 <mirrorOf>external:http:*</mirrorOf>  
		 <name>Pseudo repository to mirror external repositories initially using HTTP.</name>
		 <url>http://0.0.0.0/</url>  
		 <blocked>true</blocked>        
	</mirror>
	<mirror>  
		 <id>alimaven</id>  
		 <name>aliyun maven</name>  
		 <url>http://maven.aliyun.com/nexus/content/groups/public/</url>  
		 <mirrorOf>central</mirrorOf>        
	</mirror>
</mirrors>
~~~

![0b492ae99ceaab829e04b2fd38a92cd1.png](https://s1.imagehub.cc/images/2023/03/11/0b492ae99ceaab829e04b2fd38a92cd1.png)

maven基本信息

![4ff4f39d1a8001e56f4cc0dee4af82aa.png](https://s1.imagehub.cc/images/2023/03/11/4ff4f39d1a8001e56f4cc0dee4af82aa.png)

依赖资源

![2323b20430b19929e6c45d7bf0c0b75e.png](https://s1.imagehub.cc/images/2023/03/11/2323b20430b19929e6c45d7bf0c0b75e.png)





常用命令

~~~
mvn compile
mvn clean
mvn test
mvn install
mvn package
~~~

坐标

~~~
groupid
artifactid
version
~~~

maven项目的创建![](https://s3.bmp.ovh/imgs/2023/03/09/1b0235354cbd75f4.png)

#### MyBatis

简化jdbc开发，

~~~
JAVA EE三层架构：表现层，业务层，持久层
持久层负责数据保存在数据库的那一层代码
~~~



##### JDBC的缺点

1）硬编码

比如mysql的账号密码不可更新，sql语句不可更改

2）操作繁琐

![](https://s3.bmp.ovh/imgs/2023/03/09/0270d4ec2471b575.png)

![](https://s3.bmp.ovh/imgs/2023/03/09/abf13e94f2b6ef5c.png)

![](https://s3.bmp.ovh/imgs/2023/03/09/2ae3f409fb3514bb.png)

在resources目录下有三个文件，logback.xml，mybatis-config.xml，testMapper.xml

logback.xml

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <!--
        CONSOLE ：表示当前的日志信息是可以输出到控制台的。
    -->
    <appender name="Console" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>[%level]  %blue(%d{HH:mm:ss.SSS}) %cyan([%thread]) %boldGreen(%logger{15}) - %msg %n</pattern>
        </encoder>
    </appender>

    <logger name="com.itheima" level="DEBUG" additivity="false">
        <appender-ref ref="Console"/>
    </logger>    <!--

      level:用来设置打印级别，大小写无关：TRACE, DEBUG, INFO, WARN, ERROR, ALL 和 OFF
     ， 默认debug
      <root>可以包含零个或多个<appender-ref>元素，标识这个输出位置将会被本日志级别控制。
      -->
    <root level="DEBUG">
        <appender-ref ref="Console"/>
    </root>
</configuration>
~~~

mybatis-config.xml

~~~xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <!-- 连接信息 -->
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql:///test?useSSL=false"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <!-- 加载sql映射文件  -->
        <mapper resource="testMapper.xml"/>
    </mappers>
</configuration>
~~~

下面这里设置连接的是mysql，连接地址，哪个数据库，以及账号密码

![](https://s3.bmp.ovh/imgs/2023/03/09/0f3230776260908f.png)

加载SQL映射文件

![](https://s3.bmp.ovh/imgs/2023/03/09/b25078743b0201f0.png)

testMapper.xml

~~~xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!-- namespace名称空间 -->
<mapper namespace="test">
    <select id="selectAll" resultType="com.batmanfuture.pojo.test">
        select * from ty_user;
    </select>

</mapper>
~~~

![](https://s3.bmp.ovh/imgs/2023/03/09/550b3f28241c7da4.png)

这里namespace和select里面的id标签，在代码中有特指，后面的resultType指定我们的代码位置

com.batmanfuture.pojo.test

![](https://s3.bmp.ovh/imgs/2023/03/09/fed7dc7f99cca715.png)

![](https://s3.bmp.ovh/imgs/2023/03/09/3a1272ec9f215651.png)

这个类文件就是声明我们的数据库某表的字段信息，并且设置getter ，setter放啊，以及toString()



mybatisDemo.java

~~~java
package com.batmanfuture;

import com.batmanfuture.pojo.test;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;

public class mybatisDemo {

    public static void main(String[] args) throws IOException {

        // 1.加载mybatis的核心配置文件，获取sqlsessionFactory
        String resource = "mybatis-config.xml";
        InputStream inputStream = Resources.getResourceAsStream(resource);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

        // 2.获取SQLsession对象，用它来执行SQL
        SqlSession sqlSession = sqlSessionFactory.openSession();

        // 3.执行sql
        List<test> tests = sqlSession.selectList("test.selectAll");

        System.out.println(tests);

        sqlSession.close();

    }

}

~~~

![](https://s3.bmp.ovh/imgs/2023/03/09/e3ab68e3b64f6e8f.png)

这里指定mybatis的配置文件路径，然后下面执行SQL，SQL语句的地方就是在testMapper.xml(名字是以数据库 + Mapper.xml)中设定的

运行

![](https://s3.bmp.ovh/imgs/2023/03/09/51f1e96325f8b8f2.png)

![](https://s3.bmp.ovh/imgs/2023/03/09/74cb5440dad6ddcc.png)

。

##### Mapper代理开发

~~~
1. 定义与SQL映射文件同名的Mapper接口，并且将Mapper接口和SQL映射文件放置在同一目录下
2. 设置SQL映射文件的namespace属性为Mapper接口全限定名
3. 在Mapper接口中定义方法，方法名就是SQL映射文件中的sql语句的id，并保存参数类型和返回值类型一致
4. 编码
~~~

1.

![](https://s3.bmp.ovh/imgs/2023/03/09/a34be9c90daa52bd.png)

在com.batmanfuture下新建mapper目录，然后创建Mapper接口文件，testMapper

![](https://s3.bmp.ovh/imgs/2023/03/09/d590bb0df2f7e03a.png)

这里声明的testMapper接口中声明一个selectAll()方法，这个方法就是我们testMapper.xml中声明的方法，同理可写多行，由于返回的内容是多行，所以用List泛型集合接收，每一行返回的对象就是test

![](https://s3.bmp.ovh/imgs/2023/03/09/7f0989d677a7432b.png)

这里在resources下面，新建一个目录，内容是

~~~http
com/batmanfuture/mapper
必须是用/分割，而不用.，生成之后，经过maven编译后，我们的testMapper.xml文件和编译生成的testMapper.class字节码文件就在一个目录下了
~~~

![](https://s3.bmp.ovh/imgs/2023/03/09/62463021e8617b09.png)

![](https://s3.bmp.ovh/imgs/2023/03/09/ead66a769b3ece9a.png)

然后在mybatis-config.xml文件中，我们要修改下我们新的加载sql映射文件位置

![](https://s3.bmp.ovh/imgs/2023/03/09/68e69366115db1f9.png)

然后新建一个mybatisDemo2.java

~~~java
package com.batmanfuture;

import com.batmanfuture.mapper.testMapper;
import com.batmanfuture.pojo.test;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;

public class mybatisDemo2 {

    public static void main(String[] args) throws IOException {

        // 1.加载mybatis的核心配置文件，获取sqlsessionFactory
        String resource = "mybatis-config.xml";
        InputStream inputStream = Resources.getResourceAsStream(resource);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

        // 2.获取SQLsession对象，用它来执行SQL
        SqlSession sqlSession = sqlSessionFactory.openSession();

        // 3.执行sql
//        List<test> tests = sqlSession.selectList("test.selectAll");

        // 3.1 获取UserMapper接口的代理对象
        testMapper testmapper = sqlSession.getMapper(testMapper.class);
        List<test> tests = testmapper.selectAll();
        System.out.println(tests);

        sqlSession.close();

    }

}
~~~

![](https://s3.bmp.ovh/imgs/2023/03/09/db48de2f0d47aeab.png)

这里我们获取testMapper接口的代理对象，然后调用selectAll方法得到结果（细节内容是mapper内部完成的）

执行

![](https://s3.bmp.ovh/imgs/2023/03/09/b2dc77dca5ecb55f.png)



m









##### 包扫描

在mybatis-config.xml中

~~~xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <!-- 连接信息 -->
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql:///test?useSSL=false"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <!-- 加载sql映射文件  -->
        <mapper resource="com/batmanfuture/mapper/testMapper.xml"/>

        <!-- Mapper代理方式  -->
        <package name="com.batmanfuture.mapper"/>
    </mappers>
</configuration>
~~~

利用包扫描的方式扫描mapper目录



##### mybatis核心配置

https://mybatis.org/mybatis-3/zh/configuration.html

[![ppnip6g.png](https://s1.ax1x.com/2023/03/09/ppnip6g.png)](https://imgse.com/i/ppnip6g)

安装mybatisX之后

##### 查询

[![ppnF9C6.png](https://s1.ax1x.com/2023/03/09/ppnF9C6.png)](https://imgse.com/i/ppnF9C6)











#### Tomcat

![](https://s3.bmp.ovh/imgs/2023/03/08/253cdf9f71d04a25.png)

.

在bin目录下点击start.bat启动

![](https://s3.bmp.ovh/imgs/2023/03/08/e157217a11521611.png)

![](https://s3.bmp.ovh/imgs/2023/03/08/e1076956c0d17dd6.png)

修改web端口，在conf/server.xml中

[![ppnkRTs.png](https://s1.ax1x.com/2023/03/09/ppnkRTs.png)](https://imgse.com/i/ppnkRTs)

[![ppnAEtI.png](https://s1.ax1x.com/2023/03/09/ppnAEtI.png)](https://imgse.com/i/ppnAEtI)

[![ppnAQBQ.png](https://s1.ax1x.com/2023/03/09/ppnAQBQ.png)](https://imgse.com/i/ppnAQBQ)

idea Maven-web项目

~~~
使用骨架
~~~

[![ppnA8Nn.png](https://s1.ax1x.com/2023/03/09/ppnA8Nn.png)](https://imgse.com/i/ppnA8Nn)

[![ppnAWuD.png](https://s1.ax1x.com/2023/03/09/ppnAWuD.png)](https://imgse.com/i/ppnAWuD)

idea-tomcat

[![ppnEM26.png](https://s1.ax1x.com/2023/03/09/ppnEM26.png)](https://imgse.com/i/ppnEM26)

[![ppnEYad.png](https://s1.ax1x.com/2023/03/09/ppnEYad.png)](https://imgse.com/i/ppnEYad)

tomcat插件

```
<build>

  <plugins>
    <plugin>
      <groupId>org.apache.tomcat.maven</groupId>
      <artifactId>tomcat7-maven-plugin</artifactId>
      <version>2.1</version>
    </plugin>
```





#### Servlet

是java提供的一门动态web技术

[![ppnYzCV.png](https://s1.ax1x.com/2023/03/09/ppnYzCV.png)](https://imgse.com/i/ppnYzCV)

~~~
！！！这里必须注意，这里必须是false
~~~

[![ppntS3T.png](https://s1.ax1x.com/2023/03/09/ppntS3T.png)](https://imgse.com/i/ppntS3T)

[![ppnt9vF.png](https://s1.ax1x.com/2023/03/09/ppnt9vF.png)](https://imgse.com/i/ppnt9vF)

~~~java
package com.batmanfuture;

import javax.servlet.*;
import javax.servlet.annotation.WebServlet;
import java.io.IOException;

@WebServlet("/demo")
public class Servlet01 implements Servlet {
    @Override
    public void init(ServletConfig servletConfig) throws ServletException {

    }

    @Override
    public ServletConfig getServletConfig() {
        return null;
    }

    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        System.out.println("123大撒大撒");
    }

    @Override
    public String getServletInfo() {
        return null;
    }

    @Override
    public void destroy() {

    }
}

~~~

[![ppntm8K.png](https://s1.ax1x.com/2023/03/09/ppntm8K.png)](https://imgse.com/i/ppntm8K)



Servlet运行在Servlet容器中，有四个阶段：

~~~
1.加载和实例化
2.初始化
3.请求处理
4.服务终止
~~~

[![ppntQDH.png](https://s1.ax1x.com/2023/03/09/ppntQDH.png)](https://imgse.com/i/ppntQDH)

##### request和response

~~~java
@WebServlet("/demo2")
public class Servlet02 extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        String name = req.getParameter("name");

        resp.setHeader("content-type","text/html;charset=utf-8");

        resp.getWriter().write("<h1>" + name + "</h1>");

    }
}
~~~

[![ppnUguF.png](https://s1.ax1x.com/2023/03/09/ppnUguF.png)](https://imgse.com/i/ppnUguF)

[![ppnacGt.png](https://s1.ax1x.com/2023/03/09/ppnacGt.png)](https://imgse.com/i/ppnacGt)

~~~java
@Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        // 获取请求方式
        String method = req.getMethod();
        // 获取虚拟目录
        String textPath = req.getContextPath();
        // 获取URL
        StringBuffer url = req.getRequestURL();
        // 获取URI
        String username = req.getQueryString();
        // 获取请求参数(GET方式)
        String password = req.getParameter("name");

    }
~~~



~~~
String getQueryString()
BufferedReader getReader()
~~~

[![ppnd2k9.png](https://s1.ax1x.com/2023/03/09/ppnd2k9.png)](https://imgse.com/i/ppnd2k9)

POST乱码解决

~~~
request.setCharacterEncoding("UTF-8");
~~~



请求转发[![ppn08aQ.png](https://s1.ax1x.com/2023/03/09/ppn08aQ.png)](https://imgse.com/i/ppn08aQ)

response

[![ppn0wrT.png](https://s1.ax1x.com/2023/03/09/ppn0wrT.png)](https://imgse.com/i/ppn0wrT)

.

##### EL表达式

##### JSTL标签

~~~
使用标签取代jsp上的java代码
~~~



##### MVC和三层架构

![](https://s3.bmp.ovh/imgs/2023/03/10/2730e30a90be261d.png)

![](https://s3.bmp.ovh/imgs/2023/03/10/6523594ddccf79a2.png)



![](https://s3.bmp.ovh/imgs/2023/03/10/2fea71ce16e2e3f4.png)















#### JSP

~~~
hello.jsp -> hello_jsp.java -> hello.jsp.class
~~~

~~~
<%!...%> 内容会放在_jspService()方法之外,被类直接包含
~~~







#### 基础top10漏洞

1）反射XSS

~~~java
@WebServlet("/demo2")
public class Servlet02 extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        String name = req.getParameter("name");

        resp.setHeader("content-type","text/html;charset=utf-8");

        resp.getWriter().write("<h1>" + name + "</h1>");

    }
}
~~~

![](https://s3.bmp.ovh/imgs/2023/03/10/303b63b95d448443.png)

2）任意文件下载

~~~java
@WebServlet("/Servlet02")
public class Servlet02 extends HttpServlet {
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //处理乱码获取jsp页面的内容和传输出去的内容改变编码
        response.setContentType("text/html;charset=utf-8");
        request.setCharacterEncoding("utf-8");
        //1:获取请求参数filename
        String filename = request.getParameter("filename");
        System.out.println(filename);
        //2:使用字节输入流加载文件进内存
        //通过response获取servletContext对象;
        ServletContext servletcontext = request.getServletContext();
        System.out.println(servletcontext);
        //获取文件的路径
        String path = servletcontext.getRealPath(filename);
        System.out.println(path);
        //使用字节流关联
        FileInputStream fs = new FileInputStream(path);
        //3:设置response的响应头
        //获取文件的MIME类型;
        String mimeType = servletcontext.getMimeType(filename);
        //设置响应头的类型content-type
        response.setHeader("content-type",mimeType);
        //解决中文文件名称的问题
        //获取user-agent请求头
        String header = request.getHeader("user-agent");
        //使用工具类处理文件名
        //设置响应头打开方式content-disposition,即为点击下载弹出选择
        response.setHeader("content-disposition","attachment;");
        //4:将输入流的数据写入到输出流中
        ServletOutputStream outputStream = response.getOutputStream();
        byte[] bytes = new byte[1024];
        int len = 0;
        while ((len = fs.read(bytes)) != -1){
            outputStream.write(bytes,0,len);
        }
        //释放资源
        fs.close();
    }
}
~~~

3）任意文件读取

~~~java
package com.batmanfuture.web;

import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.ServletOutputStream;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.FileInputStream;
import java.io.IOException;

@WebServlet("/Servlet02")
public class Servlet02 extends HttpServlet {
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //处理乱码获取jsp页面的内容和传输出去的内容改变编码
        response.setContentType("text/html;charset=utf-8");
        request.setCharacterEncoding("utf-8");
        //1:获取请求参数filename
        String filename = request.getParameter("filename");
        System.out.println(filename);
        //2:使用字节输入流加载文件进内存
        //通过response获取servletContext对象;
        ServletContext servletcontext = request.getServletContext();
        System.out.println(servletcontext);
        //获取文件的路径
        String path = servletcontext.getRealPath(filename);
        System.out.println(path);
        //使用字节流关联
        FileInputStream fs = new FileInputStream(path);
        //3:设置response的响应头
        //获取文件的MIME类型;
        //设置响应头的类型content-type
        response.setHeader("content-type", "text/plain");
        //解决中文文件名称的问题
        //获取user-agent请求头
        String header = request.getHeader("user-agent");
        //使用工具类处理文件名
        //设置响应头打开方式content-disposition,即为点击下载弹出选择
        response.setHeader("content-disposition","attachment;");
        //4:将输入流的数据写入到输出流中
        ServletOutputStream outputStream = response.getOutputStream();
        byte[] bytes = new byte[1024];
        int len = 0;
        while ((len = fs.read(bytes)) != -1){
            outputStream.write(bytes,0,len);
        }
        //释放资源
        fs.close();
    }
}
~~~



order by和like这种无法预编译防御SQL注入，必须自定义过滤器





#### Filter过滤器

~~~
<filter-mapping>
	<filter-name>xsscheck</filter-mapping>
	<url-pattern>*.jsp</url-pattern>
</filter-mapping>
~~~

访问*.jsp的文件则执行过滤器

![](https://s3.bmp.ovh/imgs/2023/03/10/b31e0177cbf5a266.png)









~~~
F:\代码审计\北京创讯未来软件技术有限公司\WEB-INF\classes\com\opac\web\postinformation\mapper\PostMapper.xml


~~~



#### Spring

![ede35703f26ae150404b6aaa2dbe5165.png](https://s1.imagehub.cc/images/2023/03/11/ede35703f26ae150404b6aaa2dbe5165.png)

Spring开发步骤

~~~
① 导入坐标
② 创建Bean
③ 创建applicationContext.xml
④ 在配置文件中进行配置
⑤ 创建ApplicationContext对象getBean
~~~

1. 导入坐标

~~~xml
      <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.0.5.RELEASE</version>
      </dependency>
~~~

![](https://s3.bmp.ovh/imgs/2023/03/13/4d95afef7f44e68f.png)

2. 创建Bean

创建接口，然后实现继承该接口，并且实现对应方法

![](https://s3.bmp.ovh/imgs/2023/03/13/52036378ab30709e.png)

~~~java
package com.batmanfuture.dao.impl;
import com.batmanfuture.dao.UserDao;

public class UserDaoImpl implements UserDao {
    public void save(){
        System.out.println("123");
    }
}
~~~

![](https://s3.bmp.ovh/imgs/2023/03/13/3eba14cdac809b19.png)

3. 创建applicationContext.xml

用来绑定bean中的id和class

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="userDao" class="com.batmanfuture.dao.impl.UserDaoImpl"></bean>

</beans>
~~~

![](https://s3.bmp.ovh/imgs/2023/03/13/64efe042ad29ee92.png)

这样我们就可以直接调用这个id，然后这个id再去调用对应的类的方法

4.  在配置文件中进行配置（如上）

5.  创建ApplicationContext对象getBean

~~~java
package com.batmanfuture.demo;

import com.batmanfuture.dao.UserDao;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class UserDaoDemo {
    public static void main(String[] args) {
        ApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserDao userDao = (UserDao) app.getBean("userDao");
        userDao.save();
    }
}
~~~

![](https://s3.bmp.ovh/imgs/2023/03/13/3aa1f730cd3a3d3a.png)

这里就是先创建一个对象，传入我们的spring的xml配置文件，通过调用这个对象的getBean方法将配置文件中的id传入以此，然后直接调用其save()方法



#### Spring配置文件

spring的配置文件，applicationContext.xml文件中

scope有多个值

![](https://s3.bmp.ovh/imgs/2023/03/13/adc134b7a3ee45a1.png)

![](https://s3.bmp.ovh/imgs/2023/03/13/bc7f9436b3029dc9.png)



如果是singleton则

![](https://s3.bmp.ovh/imgs/2023/03/13/a05ac79e273489df.png)

完全一样，说明用的是同一个bean![](https://s3.bmp.ovh/imgs/2023/03/13/49c94b3d88172cf5.png)

。

同理，如果是prototype

![](https://s3.bmp.ovh/imgs/2023/03/13/7d97c6ff22e35364.png)

不一样，说明不是同一个bean

![](https://s3.bmp.ovh/imgs/2023/03/13/3290c28df6be3622.png)



====

当为singleton时

bean只创建一次

![](https://s3.bmp.ovh/imgs/2023/03/13/a859ef80b77701eb.png)

当为prototype时

![](https://s3.bmp.ovh/imgs/2023/03/13/b470d32ade592ea8.png)

这里说明所有的bean是创建了两个

![](https://s3.bmp.ovh/imgs/2023/03/13/0d2ce225dee8f061.png)

~~~
init-method：指定类中的初始化方法名称
destroy-method：指定类中销毁方法名称
~~~

![](https://s3.bmp.ovh/imgs/2023/03/13/412ae8ea32aa9895.png)

init初始方法

![](https://s3.bmp.ovh/imgs/2023/03/13/45132a67da65a0ab.png)

destry销毁方法

~~~java
package com.batmanfuture.demo;

import com.batmanfuture.dao.UserDao;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class UserDaoDemo {
    public static void main(String[] args) {
        ClassPathXmlApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserDao userDao1 = (UserDao) app.getBean("userDao");
        System.out.println(userDao1);
        app.close();
        
    }
}
~~~

![b77605495ea798d31d3ae22f411bc264.png](https://s1.imagehub.cc/images/2023/03/13/b77605495ea798d31d3ae22f411bc264.png)



![292c3175e0676be821d0ab78eee0f8cd.png](https://s1.imagehub.cc/images/2023/03/13/292c3175e0676be821d0ab78eee0f8cd.png)



工厂静态方法

~~~java
package com.batmanfuture.factory;

import com.batmanfuture.dao.UserDao;
import com.batmanfuture.dao.impl.UserDaoImpl;

public class StaticFactory {
    
    public static UserDao getUserDao(){
        return new UserDaoImpl();
    }
}
~~~

![82adc18948b0653a3ded050fa74ff592.png](https://s1.imagehub.cc/images/2023/03/13/82adc18948b0653a3ded050fa74ff592.png)

配置文件

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="userDao" class="com.batmanfuture.factory.StaticFactory" factory-method="getUserDao"></bean>

</beans>
~~~

![5001b75989a67d9acd571db3a139e260.png](https://s1.imagehub.cc/images/2023/03/13/5001b75989a67d9acd571db3a139e260.png)

![cb60bb771dfd14eaa8e12a01f27c9b69.png](https://s1.imagehub.cc/images/2023/03/13/cb60bb771dfd14eaa8e12a01f27c9b69.png)



##### 依赖注入

![05834c3b8448c796889990e00214e06e.png](https://s1.imagehub.cc/images/2023/03/13/05834c3b8448c796889990e00214e06e.png)

![f8f700165e690513e6fd9d82befe5071.png](https://s1.imagehub.cc/images/2023/03/13/f8f700165e690513e6fd9d82befe5071.png)

主函数

~~~java
package com.batmanfuture.demo;

import com.batmanfuture.service.UserService;
import com.batmanfuture.service.impl.UserServiceImpl;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class UserController {
    public static void main(String[] args) {
        ApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserService userService = (UserService) app.getBean("userService");
        userService.save();


    }
}

~~~

这里通过getbean的id是userService，

![8216b18392c0d75acf62607d6338b8ce.png](https://s1.imagehub.cc/images/2023/03/13/8216b18392c0d75acf62607d6338b8ce.png)

这里的userService通过依赖注入将userDao注入到userService中

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="userDao" class="com.batmanfuture.dao.impl.UserDaoImpl"></bean>

    <bean id="userService" class="com.batmanfuture.service.impl.UserServiceImpl">
        <property name="userDao" ref="userDao"></property>
    </bean>
</beans>
~~~

![073d46b185db9b3638ec049b0a0d8dda.png](https://s1.imagehub.cc/images/2023/03/13/073d46b185db9b3638ec049b0a0d8dda.png)

通过set的方式，

~~~java
package com.batmanfuture.service.impl;

import com.batmanfuture.dao.UserDao;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class UserServiceImpl implements com.batmanfuture.service.UserService {

    private UserDao userDao;

    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }

    public void save(){
        userDao.save();
    }

}
~~~

![2fb4cfaaed22d33a6c646ec21f179b27.png](https://s1.imagehub.cc/images/2023/03/13/2fb4cfaaed22d33a6c646ec21f179b27.png)

所以我们调save()，其实就是通过这样依赖注入的方式调的是UserDao的save()

##### Bean的依赖注入方式

###### 命名空间注入/set方法注入

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"

       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="userDao" class="com.batmanfuture.dao.impl.UserDaoImpl"></bean>
    <bean id="userService" class="com.batmanfuture.service.impl.UserServiceImpl" p:userDao-ref="userDao"></bean>

</beans>
~~~

###### 构造方法注入

userServicelmpi.java

~~~java
package com.batmanfuture.service.impl;

import com.batmanfuture.dao.UserDao;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class UserServiceImpl implements com.batmanfuture.service.UserService {

    private UserDao userDao;

    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }

    public UserServiceImpl(UserDao userDao) {
        this.userDao = userDao;
    }

    public UserServiceImpl() {

    }

    public void save(){
        userDao.save();
    }

}

~~~

xml

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"

       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="userDao" class="com.batmanfuture.dao.impl.UserDaoImpl"></bean>

<!--    <bean id="userService" class="com.batmanfuture.service.impl.UserServiceImpl">-->
<!--        <property name="userDao" ref="userDao"></property>-->
<!--    </bean>-->



    <bean id="userService" class="com.batmanfuture.service.impl.UserServiceImpl">
        <constructor-arg name="userDao" ref="userDao"></constructor-arg>
    </bean>

</beans>
~~~



##### 注入方式

![be2bf7184340bf0f18b6d103b7ada4dd.png](https://s1.imagehub.cc/images/2023/03/13/be2bf7184340bf0f18b6d103b7ada4dd.png)

##### import导入

![27ad77788976910608299294ebbff635.png](https://s1.imagehub.cc/images/2023/03/13/27ad77788976910608299294ebbff635.png)

![b93de55f45a376775c25272dbf69ce9c.png](https://s1.imagehub.cc/images/2023/03/13/b93de55f45a376775c25272dbf69ce9c.png)

Spring API

![d96f21f1114b9d03f1d817c4cf8e17d8.png](https://s1.imagehub.cc/images/2023/03/13/d96f21f1114b9d03f1d817c4cf8e17d8.png)

~~~java
        ApplicationContext app = new FileSystemXmlApplicationContext("c:\\xxx");
        UserService userService = (UserService) app.getBean("userService");
        userService.save();
~~~

![9b8dcebd9f7860cdc4cc6e3d1853e47b.png](https://s1.imagehub.cc/images/2023/03/13/9b8dcebd9f7860cdc4cc6e3d1853e47b.png)

之前我们是通过id的方式调getbean

~~~java
UserService userService = (UserService) app.getBean("userService");
~~~

现在是通过

~~~
UserService userService = app.getBean(userService.class);
~~~

~~~java
public class UserController {
    public static void main(String[] args) {
        ApplicationContext applicationContext = new
                ClassPathXmlApplicationContext("applicationContext.xml");
        UserService userService1 = (UserService)
                applicationContext.getBean("userService");
        UserService userService2 = applicationContext.getBean(UserService.class);
    }
}

~~~

##### 数据源（连接池）的作用

~~~
• 数据源(连接池)是提高程序性能如出现的
• 事先实例化数据源，初始化部分连接资源
• 使用连接资源时从数据源中获取
• 使用完毕后将连接资源归还给数据源

常见的数据源(连接池)：DBCP、C3P0、BoneCP、Druid等
~~~



##### 数据源测试

~~~java
import com.mchange.v2.c3p0.ComboPooledDataSource;
import org.junit.jupiter.api.Test;

import java.sql.Connection;

public class DataSource {
    @Test
    public void testC3P0() throws Exception {
// 创建数据源
        ComboPooledDataSource dataSource = new ComboPooledDataSource();
// 设置数据库连接参数
        dataSource.setDriverClass("com.mysql.jdbc.Driver");
        dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/test");
        dataSource.setUser("root");
        dataSource.setPassword("root");
// 获得连接对象
        Connection connection = dataSource.getConnection();
        System.out.println(connection);
    }
}

~~~

导入c3p0和druid的坐标

~~~xml
<!-- C3P0 连接池 -->
<dependency>
<groupId>c3p0</groupId>
<artifactId>c3p0</artifactId>
<version>0.9.1.2</version>
</dependency>
<!-- Druid 连接池 -->
<dependency>
<groupId>com.alibaba</groupId>
<artifactId>druid</artifactId>
<version>1.1.10</version>
</dependency>
~~~

![b8ea992abd530b1eb7959c365efb45f9.png](https://s1.imagehub.cc/images/2023/03/13/b8ea992abd530b1eb7959c365efb45f9.png)

##### 数据源的手动创建

~~~
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/test
jdbc.username=root
jdbc.password=root
~~~

![2911be70a8dcea8abac482f3f7027d33.png](https://s1.imagehub.cc/images/2023/03/13/2911be70a8dcea8abac482f3f7027d33.png)

~~~java
import com.mchange.v2.c3p0.ComboPooledDataSource;
import org.junit.jupiter.api.Test;

import java.sql.Connection;
import java.util.ResourceBundle;

public class DataSource {
    @Test
    public void testC3P0ByProperties() throws Exception {
// 加载类路径下的 jdbc.properties
        ResourceBundle rb = ResourceBundle.getBundle("jdbc");
        ComboPooledDataSource dataSource = new ComboPooledDataSource();
        dataSource.setDriverClass(rb.getString("jdbc.driver"));
        dataSource.setJdbcUrl(rb.getString("jdbc.url"));
        dataSource.setUser(rb.getString("jdbc.username"));
        dataSource.setPassword(rb.getString("jdbc.password"));
        Connection connection = dataSource.getConnection();
        System.out.println(connection);
    }
}
~~~

![f09a2292474ced05cd7a7312f8bee1a2.png](https://s1.imagehub.cc/images/2023/03/13/f09a2292474ced05cd7a7312f8bee1a2.png)



#### Spring配置数据源

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="com.mysql.jdbc.Driver"/>
        <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/test"/>
        <property name="user" value="root"/>
        <property name="password" value="root"/>
    </bean>
</beans>
~~~

~~~java
import com.mchange.v2.c3p0.ComboPooledDataSource;
import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import java.sql.Connection;
import java.util.ResourceBundle;

public class DataSource {
    @Test
    public void testC3P0ByProperties() throws Exception {
// 加载类路径下的 jdbc.properties
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
        DataSource dataSource = (DataSource)
                applicationContext.getBean("dataSource");
        Connection connection = dataSource.getConnection();
        System.out.println(connection);
    }
    
}
~~~





#### 注解开发

![95cbfb78e936aa8c46a20e53c9ea33db.png](https://s1.imagehub.cc/images/2023/03/13/95cbfb78e936aa8c46a20e53c9ea33db.png)

还是刚才的哪个测试代码

主函数

~~~java
package com.batmanfuture.demo;

import com.batmanfuture.service.UserService;
import com.batmanfuture.service.impl.UserServiceImpl;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class UserController {
    public static void main(String[] args) {
        ApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserService userService = (UserService) app.getBean(UserService.class);
        userService.save();


    }
}

~~~

![c1c8b1d0cc4328ee95323951065c02a9.png](https://s1.imagehub.cc/images/2023/03/13/c1c8b1d0cc4328ee95323951065c02a9.png)

UserDao

这里@Component("userDao")就是用来绑定id的

~~~java
package com.batmanfuture.dao.impl;

import com.batmanfuture.dao.UserDao;
import org.springframework.stereotype.Component;

@Component("userDao")
public class UserDaoImpl implements UserDao {
    public void save(){

        System.out.println("save running...");
    }
}
~~~

![bd7ccd026ad85afc650c95a761263d6b.png](https://s1.imagehub.cc/images/2023/03/13/bd7ccd026ad85afc650c95a761263d6b.png)

UserService

这里@Component("userService")是用来绑定id的，下面的

    @Autowired
    @Qualifier("userDao")

就是用来依赖注入的

~~~java
package com.batmanfuture.service.impl;

import com.batmanfuture.dao.UserDao;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.springframework.stereotype.Component;

@Component("userService")
public class UserServiceImpl implements com.batmanfuture.service.UserService {

    @Autowired
    @Qualifier("userDao")
    private UserDao userDao;

    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }

    public void save(){
        userDao.save();
    }

}
~~~

![f4b2fd11423dc4e388b78e49f57300d5.png](https://s1.imagehub.cc/images/2023/03/13/f4b2fd11423dc4e388b78e49f57300d5.png)

![8f5072f7fd0512dda637c900981251c9.png](https://s1.imagehub.cc/images/2023/03/13/8f5072f7fd0512dda637c900981251c9.png)

.

~~~xml
<context:component-scan base-package="com.batmanfuture"></context:component-scan>
~~~

![95cbfb78e936aa8c46a20e53c9ea33db.png](https://s1.imagehub.cc/images/2023/03/13/95cbfb78e936aa8c46a20e53c9ea33db.png)

这里的@Compenent就是用来绑定id的

@Controller是web层下的控制器

@Service是业务层的逻辑处理

@Repository和@Compenent差不多

@Autowired：

如果不是使用xml的方式，这里的set方法可以不写

![9ddeddd9ca8a193dcea9b659d51c2442.png](https://s1.imagehub.cc/images/2023/03/13/9ddeddd9ca8a193dcea9b659d51c2442.png)

可以直接通过反射赋值。

@value可以注入给普通属性

首先在配置文件先加载

~~~xml
<context:property-placeholder location="classpath:jdbc.properties"/>
~~~

![655626d2a2fee019b08652b442b82808.png](https://s1.imagehub.cc/images/2023/03/13/655626d2a2fee019b08652b442b82808.png)

然后

~~~java
package com.batmanfuture.service.impl;

import com.batmanfuture.dao.UserDao;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.springframework.stereotype.Component;

@Component("userService")
public class UserServiceImpl implements com.batmanfuture.service.UserService {

    @Value("${jdbc.driver}")
    private String driver;


    @Qualifier("userDao")
    private UserDao userDao;

    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }

    public void save(){
        System.out.println(driver);
        userDao.save();
    }

}
~~~

![1b1a1e48cc23925b9a0dcc84435a5fdf.png](https://s1.imagehub.cc/images/2023/03/13/1b1a1e48cc23925b9a0dcc84435a5fdf.png)

![b341ece2d11521cc5a5b2514a587b289.png](https://s1.imagehub.cc/images/2023/03/13/b341ece2d11521cc5a5b2514a587b289.png)

![689854414078fc452707bfce935b617d.png](https://s1.imagehub.cc/images/2023/03/13/689854414078fc452707bfce935b617d.png)





#### MVC

![](https://s3.bmp.ovh/imgs/2023/03/14/ecacdea7b69753e4.png)



![](https://s3.bmp.ovh/imgs/2023/03/14/9131a67b5069454a.png)

ContextLoaderListener.java

~~~java
package com.itheima.listener;

import com.itheima.service.UserService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import javax.servlet.ServletContext;
import javax.servlet.ServletContextEvent;
import javax.servlet.ServletContextListener;

public class ContextLoaderListener implements ServletContextListener {

    public void contextInitialized(ServletContextEvent servletContextEvent) {
        ServletContext servletContext = servletContextEvent.getServletContext();
        //读取web.xml中的全局参数
        String contextConfigLocation = servletContext.getInitParameter("contextConfigLocation");
        ApplicationContext app = new ClassPathXmlApplicationContext(contextConfigLocation);
        //将Spring的应用上下文对象存储到ServletContext域中
        servletContext.setAttribute("app",app);
        System.out.println("spring容器创建完毕....");
    }

    public void contextDestroyed(ServletContextEvent servletContextEvent) {

    }
}
~~~

WebApplicationContextUtils.java

~~~java
package com.itheima.listener;

import org.springframework.context.ApplicationContext;

import javax.servlet.ServletContext;

public class WebApplicationContextUtils {

    public static ApplicationContext getWebApplicationContext(ServletContext servletContext){
        return (ApplicationContext) servletContext.getAttribute("app");
    }

}
~~~

![](https://s3.bmp.ovh/imgs/2023/03/14/5c96fd6e77344de9.png)

![](https://s3.bmp.ovh/imgs/2023/03/14/d180fe45d81e883b.png)

~~~xml
<dependency>
<groupId>org.springframework</groupId>
<artifactId>spring-web</artifactId>
<version>5.0.5.RELEASE</version>
</dependency>
~~~

##### SpringMVC开发步骤

~~~
① 导入SpringMVC相关坐标
② 配置SpringMVC核心控制器DispathcerServlet[配置前端控制器]
③ 创建Controller类和视图页面
④ 使用注解配置Controller类中业务方法的映射地址
⑤ 配置SpringMVC核心文件 spring-mvc.xml[为了组件扫描]
⑥ 客户端发起请求测试
~~~

1)

![](https://s3.bmp.ovh/imgs/2023/03/14/59ac4f6b4d182383.png)

2)

![](https://s3.bmp.ovh/imgs/2023/03/14/c0e484f2b9b83d34.png)



3,4)

![](https://s3.bmp.ovh/imgs/2023/03/14/7491dcd2908d1fce.png)

5)

![](https://s3.bmp.ovh/imgs/2023/03/14/e38f851abda16fd6.png)

---------------------

![](https://s3.bmp.ovh/imgs/2023/03/14/f3a49c69379659b8.png)

![](https://s3.bmp.ovh/imgs/2023/03/14/e64ef38814b34667.png)

![](https://s3.bmp.ovh/imgs/2023/03/14/759fdd5df4f036a0.png)

![](https://s3.bmp.ovh/imgs/2023/03/14/5cc89a338150a50b.png)

![](https://s3.bmp.ovh/imgs/2023/03/14/492a2abb459801f9.png)

总结：

用户首先打开网页是：

~~~
http://localhost:8080/batman_war_exploded/
src/main/webapp/index.jsp
~~~

![](https://s3.bmp.ovh/imgs/2023/03/14/d75de765fccb9293.png)

定义了web目录

~~~java
package com.itheima.web;

import com.itheima.service.UserService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.springframework.web.context.WebApplicationContext;
import org.springframework.web.context.support.WebApplicationContextUtils;

import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public class UserServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //ApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
        ServletContext servletContext = this.getServletContext();
        //ApplicationContext app = (ApplicationContext) servletContext.getAttribute("app");
        //ApplicationContext app = WebApplicationContextUtils.getWebApplicationContext(servletContext);
        ApplicationContext app = WebApplicationContextUtils.getWebApplicationContext(servletContext);
        UserService userService = app.getBean(UserService.class);
        userService.save();
    }
}

~~~

![](https://s3.bmp.ovh/imgs/2023/03/14/0fa42ef333d7606e.png)

这个文件配置了监听器，这样就不用每次访问Bean，都去申请一个

![](https://s3.bmp.ovh/imgs/2023/03/14/eced393cd22fb472.png)

![](https://s3.bmp.ovh/imgs/2023/03/14/ae29a459c49afcb1.png)

而这里调用的是UserService

![](https://s3.bmp.ovh/imgs/2023/03/14/4c44c7aa077108b2.png)

这个UserService依赖注入通过set方法，成功拿到了UserDao的内容

![](https://s3.bmp.ovh/imgs/2023/03/14/d4b7c066bd1695c4.png)

![bd4e7181191fbff65a5afd419961e839.png](https://s1.imagehub.cc/images/2023/03/14/bd4e7181191fbff65a5afd419961e839.png)

回顾SpringMVC的执行流程

~~~
① 用户发送请求至前端控制器DispatcherServlet。
② DispatcherServlet收到请求调用HandlerMapping处理器映射器。
③ 处理器映射器找到具体的处理器(可以根据xml配置、注解进行查找)，生成处理器对象及处理器拦截器(如果
有则生成)一并返回给DispatcherServlet。
④ DispatcherServlet调用HandlerAdapter处理器适配器。
⑤ HandlerAdapter经过适配调用具体的处理器(Controller，也叫后端控制器)。
⑥ Controller执行完成返回ModelAndView。
⑦ HandlerAdapter将controller执行结果ModelAndView返回给DispatcherServlet。
⑧ DispatcherServlet将ModelAndView传给ViewReslover视图解析器。
⑨ ViewReslover解析后返回具体View。
⑩ DispatcherServlet根据View进行渲染视图（即将模型数据填充至视图中）。DispatcherServlet响应用户
~~~



#### SpringMVC的数据响应方式

~~~
1) 页面跳转
直接返回字符串
通过ModelAndView对象返回
~~~

![95a3cd1d6d9ef400a26f7c1b41e82c57.png](https://s1.imagehub.cc/images/2023/03/14/95a3cd1d6d9ef400a26f7c1b41e82c57.png)

![f2239a0f43b2f5f4448696acad1f77e8.png](https://s1.imagehub.cc/images/2023/03/14/f2239a0f43b2f5f4448696acad1f77e8.png)![2ff0053caedce18706c29f7c40798268.png](https://s1.imagehub.cc/images/2023/03/14/2ff0053caedce18706c29f7c40798268.png)

页面跳转方式二

![5e288038cf3cf09b41a16443553949d8.png](https://s1.imagehub.cc/images/2023/03/14/5e288038cf3cf09b41a16443553949d8.png)

~~~java
package com.itheima.controller;

import com.fasterxml.jackson.databind.ObjectMapper;
import com.itheima.domain.User;
import com.itheima.domain.VO;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.multipart.MultipartFile;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import java.io.File;
import java.io.IOException;
import java.util.Arrays;
import java.util.Date;
import java.util.List;

@Controller
@RequestMapping("/user")
public class UserController {

    @RequestMapping(value="/quick23")
    @ResponseBody
    public void save23(String username, MultipartFile[] uploadFile) throws IOException {
        System.out.println(username);
        for (MultipartFile multipartFile : uploadFile) {
            String originalFilename = multipartFile.getOriginalFilename();
            multipartFile.transferTo(new File("D:\\idea\\batman\\src\\main\\webapp\\img\\"+originalFilename));
        }
    }

    @RequestMapping(value="/quick22")
    @ResponseBody
    public void save22(String username, MultipartFile uploadFile,MultipartFile uploadFile2) throws IOException {
        System.out.println(username);
        //获得上传文件的名称
        String originalFilename = uploadFile.getOriginalFilename();
        uploadFile.transferTo(new File("C:\\upload\\"+originalFilename));
        String originalFilename2 = uploadFile2.getOriginalFilename();
        uploadFile2.transferTo(new File("C:\\upload\\"+originalFilename2));
    }

    @RequestMapping(value="/quick21")
    @ResponseBody
    public void save21(@CookieValue(value = "JSESSIONID") String jsessionId) throws IOException {
        System.out.println(jsessionId);
    }

    @RequestMapping(value="/quick20")
    @ResponseBody
    public void save20(@RequestHeader(value = "User-Agent",required = false) String user_agent) throws IOException {
        System.out.println(user_agent);
    }


    @RequestMapping(value="/quick19")
    @ResponseBody
    public void save19(HttpServletRequest request, HttpServletResponse response, HttpSession session) throws IOException {
        System.out.println(request);
        System.out.println(response);
        System.out.println(session);
    }

    @RequestMapping(value="/quick18")
    @ResponseBody
    public void save18(Date date) throws IOException {
        System.out.println(date);
    }

    // localhost:8080/user/quick17/zhangsan
    @RequestMapping(value="/quick17/{name}")
    @ResponseBody
    public void save17(@PathVariable(value="name") String username) throws IOException {
        System.out.println(username);
    }

    @RequestMapping(value="/quick16")
    @ResponseBody
    public void save16(@RequestParam(value="name",required = false,defaultValue = "itcast") String username) throws IOException {
        System.out.println(username);
    }

    @RequestMapping(value="/quick15")
    @ResponseBody
    public void save15(@RequestBody List<User> userList) throws IOException {
        System.out.println(userList);
    }

    @RequestMapping(value="/quick14")
    @ResponseBody
    public void save14(VO vo) throws IOException {
        System.out.println(vo);
    }


    @RequestMapping(value="/quick13")
    @ResponseBody
    public void save13(String[] strs) throws IOException {
        System.out.println(Arrays.asList(strs));
    }

    @RequestMapping(value="/quick12")
    @ResponseBody
    public void save12(User user) throws IOException {
        System.out.println(user);
    }

    @RequestMapping(value="/quick11")
    @ResponseBody
    public void save11(String username,int age) throws IOException {
        System.out.println(username);
        System.out.println(age);
    }

    @RequestMapping(value="/quick10")
    @ResponseBody
    //期望SpringMVC自动将User转换成json格式的字符串
    public User save10() throws IOException {
        User user = new User();
        user.setUsername("lisi2");
        user.setAge(32);

        return user;
    }

    @RequestMapping(value="/quick9")
    @ResponseBody
    public String save9() throws IOException {
        User user = new User();
        user.setUsername("lisi");
        user.setAge(30);
        //使用json的转换工具将对象转换成json格式字符串在返回
        ObjectMapper objectMapper = new ObjectMapper();
        String json = objectMapper.writeValueAsString(user);

        return json;
    }

    @RequestMapping(value="/quick8")
    @ResponseBody
    public String save8() throws IOException {
        return "{\"username\":\"zhangsan\",\"age\":18}";
    }

    @RequestMapping(value="/quick7")
    @ResponseBody  //告知SpringMVC框架 不进行视图跳转 直接进行数据响应
    public String save7() throws IOException {
        return "hello itheima";
    }

    @RequestMapping(value="/quick6")
    public void save6(HttpServletResponse response) throws IOException {
        response.getWriter().print("<script>alert(123)</script>");
    }

    @RequestMapping(value="/quick5")
    public String save5(HttpServletRequest request){
        request.setAttribute("username","酷丁鱼");
        return "success";
    }

    @RequestMapping(value="/quick4")
    public String save4(Model model){
        model.addAttribute("username","博学谷");
        return "success";
    }

    @RequestMapping(value="/quick3")
    public ModelAndView save3(ModelAndView modelAndView){
        modelAndView.addObject("username","itheima");
        modelAndView.setViewName("success");
        return modelAndView;
    }


    @RequestMapping(value="/quick2")
    public ModelAndView save2(){
        /*
            Model:模型 作用封装数据
            View：视图 作用展示数据
         */
        ModelAndView modelAndView = new ModelAndView();
        //设置模型数据
        modelAndView.addObject("username","itcast");
        //设置视图名称
        modelAndView.setViewName("success");

        return modelAndView;
    }

    // 请求地址  http://localhost:8080/user/quick
    @RequestMapping(value="/quick",method = RequestMethod.GET,params = {"username"})
    public String save(){
        System.out.println("Controller save running....");
        return "success";
    }

}

~~~

![7eaae3bc6929d302a0b9644c2e570a40.png](https://s1.imagehub.cc/images/2023/03/14/7eaae3bc6929d302a0b9644c2e570a40.png)

这里的quick14方法，这里接收的是V0对象，所以我们跟过去看V0类的声明

![17736e9d93bd557d7e361a757bb49ec9.png](https://s1.imagehub.cc/images/2023/03/14/17736e9d93bd557d7e361a757bb49ec9.png)

V0的声明，这里的V0有一个私有属性LIst泛型集合，内容是User对象

![ba35436cb733c53c218a04629ac335a9.png](https://s1.imagehub.cc/images/2023/03/14/ba35436cb733c53c218a04629ac335a9.png)

跟过去看User类的声明，有两个属性，username和age

![3c22ac13416ab49d14ec045fdd8c51fa.png](https://s1.imagehub.cc/images/2023/03/14/3c22ac13416ab49d14ec045fdd8c51fa.png)

所以我们写一个form.jsp表单，因为是接收集合的方式，所以写成userList[0].username，userList[0].age等

![64da24192d0002378db478953ca80b57.png](https://s1.imagehub.cc/images/2023/03/14/64da24192d0002378db478953ca80b57.png)

构造请求，发送

![cdecf6024b42f5b9c495c844bd51cbc9.png](https://s1.imagehub.cc/images/2023/03/14/cdecf6024b42f5b9c495c844bd51cbc9.png)

![2f351ae09a8d7b5981081a32b239284c.png](https://s1.imagehub.cc/images/2023/03/14/2f351ae09a8d7b5981081a32b239284c.png)

quick16

用的是RequestParam，参数绑定防注入的

文件上传

upload.jsp

~~~html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>

    <form action="${pageContext.request.contextPath}/user/quick23" method="post" enctype="multipart/form-data">
        名称<input type="text" name="username"><br/>
        文件1<input type="file" name="uploadFile"><br/>
        文件2<input type="file" name="uploadFile"><br/>
        <input type="submit" value="提交">
    </form>

    <form action="${pageContext.request.contextPath}/user/quick22" method="post" enctype="multipart/form-data">
        名称<input type="text" name="username"><br/>
        文件1<input type="file" name="uploadFile"><br/>
        文件2<input type="file" name="uploadFile2"><br/>
        <input type="submit" value="提交">
    </form>
</body>
</html>

~~~



##### Spring-JDBCTemplate

步骤

~~~
① 导入spring-jdbc和spring-tx坐标
② 创建数据库表和实体
③ 创建JdbcTemplate对象
④ 执行数据库操作
~~~



查询操作

~~~java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext.xml")
public class JdbcTemplateCRUDTest {

    @Autowired
    private JdbcTemplate jdbcTemplate;

    @Test
    public void testQueryCount(){
        Long count = jdbcTemplate.queryForObject("select count(*) from ty_user", Long.class);
        System.out.println(count);
    }

    @Test
    public void testQueryOne(){
        Account account = jdbcTemplate.queryForObject("select * from account where name=?", new BeanPropertyRowMapper<Account>(Account.class), "tom");
        System.out.println(account);
    }

    @Test
    public void testQueryAll(){
        List<Account> accountList = jdbcTemplate.query("select * from ty_user", new BeanPropertyRowMapper<Account>(Account.class));
        System.out.println(accountList);
    }

    @Test
    public void testUpdate(){
        jdbcTemplate.update("update account set money=? where name=?",10000,"tom");
    }

    @Test
    public void testDelete(){
        jdbcTemplate.update("delete from account where name=?","tom");
    }

}
~~~



#### interceptor拦截器

拦截器链和过滤器链

![3522d44c918f495368287a343849754d.png](https://s1.imagehub.cc/images/2023/03/14/3522d44c918f495368287a343849754d.png)







~~~
src/main/java/com/wxy/servlet/bill/BillServlet.java
~~~































