helloworld

~~~c#
using System;
namespace HelloWorldApplication
{
    class HelloWorld
    {
        static void Main(string[] args)
        {
            /* Write C# code in this online editor and run it. */
            Console.WriteLine("输入姓名: ");
            String str = Console.ReadLine();
            Console.WriteLine(str);
            Console.ReadKey();
        }
    }
}
~~~



枚举类型

~~~c#
//区别枚举类型和结构体类型
	public enum Gender
    {
        男,
        女
    }
    internal class Program
    {
        static void Main(string[] args)
        {
            Gender p1 = Gender.男;
        }
    }
~~~

结构体

~~~c#
struct Person {
    char name[50];
    int age;
    float height;
};
~~~

枚举：表示状态、选项或标志。

~~~c#
enum Color {
    RED,
    GREEN,
    BLUE
};
~~~











File类-文件操作

~~~
System.IO.File
转进定义去看
~~~

~~~c#
namespace Linq
{

    internal class Program
    {
        static void Main(string[] args)
        {
            //创建文件
            File.Create(@"F:\blog\gitbook\.net\webshell.aspx");
            //删除文件
            File.Delete(@"F:\blog\gitbook\.net\webshell.aspx");
            //复制文件
            File.Copy(@"F:\blog\gitbook\.net\webshell.aspx",
                      @"F:\blog\gitbook\.net\webshell2.aspx");
            //读取文件 ，格式是字符串数组
            string[] contents = 		File.ReadAllLines(@"F:\blog\gitbook\.net\webshell.aspx",Encoding.Default);
            //读取文件 ，格式是字符串
            string[] contents = File.ReadAllText(@"F:\blog\gitbook\.net\webshell.aspx",Encoding.Default);
            //只能读二进制
            string[] contents = File.ReadAllBytes(@"F:\blog\gitbook\.net\webshell.aspx",Encoding.Default);
            //任意文件写入，字符串
            File.WriteAllText(@"F:\blog\gitbook\.net\webshell.aspx","dassdasdasdasd");
            //任意文件写入，字符串数组
            File.WriteAllLines(@"F:\blog\gitbook\.net\webshell.aspx", "dassdasdasdasd");

            foreach (var item in contents)
            {
                Console.WriteLine(item);
            }
            Console.ReadLine();
        }
    }

}
~~~



![](https://s3.bmp.ovh/imgs/2023/03/06/dcd43bb68e6b8622.png)![](https://s3.bmp.ovh/imgs/2023/03/06/e360e27eaa25f7f6.png)







ref，out，param

~~~
out
为了解决在一个方法中返回多个相同类型的值的时候，可以用数组
为了解决在一个方法中返回多个不同类型的值的时候，可以用out
~~~

~~~c#
    internal class Program
    {
        static void Main(string[] args)
        {
            Int[]nums = { 1, 2,3,4,5,6,7,8,9 };
            test(nums, out max, out min, out sum, out avg);
        }
        public staic void Test(int[] nums,out max,out min,out sum,out avg)
        {

        }
    }
~~~

ref

~~~
能够将一个变量带入一个方法中进行改变，改变完成后再将改变完成后带出方法
~~~

~~~c#
    internal class Program
    {
        static void Main(string[] args)
        {
            double salary = 5000;
            jiangjin(ref salary);

        }
        public static void jiangjin(ref double s)
        {
            s += 50;
        }

    }
~~~

param

~~~c#
可变参数
~~~

```c#
internal class Program
{
    static void Main(string[] args)
    {
        int[] s = { 1, 2, 3, 4 };
        // Test("张三",1,2,3,4);
        Test("张三",s);
    }
    public static void Test(string name, int[] score)
    {
    }
```

构造函数和析构函数

~~~c#
class Person{
    int x = 0;
    int y = 0;
    public Person(int x,int y){
        this.x = x;
        this.y = y;
        console.writeline("构造函数");
    }
    
    ~Person(){
        console.writeline("析构函数");
    }
}
~~~

new

~~~
隐藏从父类那里继承过来的同名成员
    public new 123()
        {

        }
~~~

字符串的各种方法

~~~
length
toupper()
tolower()
equals()比较两个字符串
split()
substring()截取字符串
indexof()判断某个字符串在字符从中第一次出现位置
lastindexof()判断某个字符串在字符从中最后一次出现位置
startwith()判断以xxx开头
endswith()
trim()取前后的空格
contains()判断字符从是否包含
string.isnullofempty()判断是否是空或者null
string.join()将数组按照指定的字符串连接，返回一个字符串
~~~

里氏转换

~~~c#
//1.子类可以赋值给父类,如果有一个地方需要父类作为参数，我们可以给一个子类作为代替
    internal class Program
    {
        static void Main(string[] args)
        {
            Person p1 = new Man();
        }
    }
    class Person
    {}
    class Man : Person
    {}
//2.如父类中装的是子类对象，那么可以强转父类为子类对象
Man p2 = (Man)p1;
~~~

集合





![](https://s3.bmp.ovh/imgs/2023/03/03/b9e5635d9f1876d3.png)

![](https://s3.bmp.ovh/imgs/2023/03/03/676c1c27f02c51f0.png)

1）console

![](https://s3.bmp.ovh/imgs/2023/03/03/8c6d39ea19ad6510.png)2

![](https://s3.bmp.ovh/imgs/2023/03/03/6f41233c7dfadc70.png)

![](https://s3.bmp.ovh/imgs/2023/03/03/b7c950d27e326b9d.png)

2）WPF

![](https://s3.bmp.ovh/imgs/2023/03/03/eeb5195c447a705a.png)

3）wpf

![](https://s3.bmp.ovh/imgs/2023/03/03/6061178bc23a29a0.png)

![](https://s3.bmp.ovh/imgs/2023/03/03/33c51dbb7c87cf43.png)

![](https://s3.bmp.ovh/imgs/2023/03/03/c902fc6c5d29c20c.png)

.

4）web form已过时

5）MVC（web端）

...

~~~
类，名称空间
dll引用（黑盒），项目引用（白盒）
依赖关系，排除错误
~~~

![](https://s3.bmp.ovh/imgs/2023/03/03/9ab34a64cc467c50.png)

名称空间

msdn

https://blog.csdn.net/weixin_43833642/article/details/106035267

#### 类，对象，类成员

~~~c#
namespace ConsoleApp2
{
    internal class Program
    {
        static void Main(string[] args)
        {
            Form myForm = new Form();
            myForm.Text = "getshell";
            myForm.ShowDialog();
        }
    }
}
~~~

![](https://s3.bmp.ovh/imgs/2023/03/03/cc484ea8bd853f9e.png)

~~~
属性，方法，事件，
事件event是c#特有的机制，java通过其他方法实现这个机制
~~~

~~~c#
关键字，操作符，标识符，标点符号，文本，注释

强类型语言可以保证数据的完整性安全性

可以利用dynamic模仿弱类型

danamic xxx = 123;
console.writeline(xxx);
xxx = "haha";
console.writeline(xxx);
~~~



#### 内存操作

~~~c#
namespace WpfApp2
{
    /// <summary>
    /// MainWindow.xaml 的交互逻辑
    /// </summary>
    public partial class MainWindow : Window
    {
        public MainWindow()
        {
            InitializeComponent();
        }

        List<Window> winList;

        private void Button_Click(object sender, RoutedEventArgs e)
        {
            winList = new List<Window>();
            for (int i = 0; i < 15000; i++)
            {
                Window w = new Window();
                winList.Add(w);
            }
            
        }

        private void Button_Click_1(object sender, RoutedEventArgs e)
        {
            winList.Clear();
        }
    }
}

~~~

![](https://s3.bmp.ovh/imgs/2023/03/03/d35e24559a799e9d.png)

![](https://s3.bmp.ovh/imgs/2023/03/03/54ec2601274d60f2.png)

~~~
win + r	打开搜索perfmon
process->private bytes->heap
~~~

![](https://s3.bmp.ovh/imgs/2023/03/03/b4cf65903fdc3498.png)

![](https://s3.bmp.ovh/imgs/2023/03/03/f1851016c13b0672.png)



~~~
c语言对类的声明xx.h，对类的定义xx.cpp
而c#则是这两个合在一起的xx.cs
~~~

~~~c#
public int add(int a,int b){
    return a + b;
}
public int add<T>(int a,int b){
    T t;
    return a + b;
}
// 类型形参可以参与构成方法签名
~~~



~~~
传值参数
输出参数
引用参数
数组参数
具名参数
可选参数
扩展方法(this参数)
~~~

#### 委托

~~~
指针的升级版
Action委托和Func委托
~~~

###### Action委托

~~~c#
        static void Main(string[] args)
        {
            Calculator p1 = new Calculator();
            Action p2 = new Action(p1.Report);
            p1.Report();
            p2.Invoke();
            //或者使用 p2();

        }
    }

    class Calculator
    {
        public void Report()
        {
            Console.WriteLine("batmanfuture123456");
        }
    }
~~~

![074228439666a420ca37ce619dfa005b.png](https://s1.imagehub.cc/images/2023/03/03/074228439666a420ca37ce619dfa005b.png)

![](https://s3.bmp.ovh/imgs/2023/03/03/591959bf575338b7.png)

###### Func委托

~~~c#
            Func<int, int, int> func1 = new Func<int, int, int>(p1.Add);
            Func<int, int, int> func2 = new Func<int, int, int>(p1.Sub);

            int x = 100;
            int y = 200;
            int z = 0;
            z = func1.Invoke(x,y);
            Console.WriteLine(z);
            z = func2.Invoke(x, y);
            Console.WriteLine(z);
~~~

或者

~~~c#
            z = func1(x,y);
            Console.WriteLine(z);
            z = func2(x, y);
            Console.WriteLine(z);
~~~



![35efc104aca49b0828982542910b5b49.png](F:\blog\gitbook\thinkphp\35efc104aca49b0828982542910b5b49.png)

![b4e3f4bc10dea6c7cd627b285812d4db.png](https://s1.imagehub.cc/images/2023/03/03/b4e3f4bc10dea6c7cd627b285812d4db.png)

.

###### 自定义委托

~~~c#
namespace ConsoleApp2
{
    public delegate double Calc(double a, double b);
    internal class Program
    {
        static void Main(string[] args)
        {
            Calculator p1 = new Calculator();
            Calc calc1 = new Calc(p1.Add);
            Calc calc2 = new Calc(p1.Sub);
            Calc calc3 = new Calc(p1.Mul);
            Calc calc4 = new Calc(p1.Div);

            double a = 200;
            double b = 100;
            double c = 0;
            c = calc1.Invoke(a, b);
            Console.WriteLine(c);
            c = calc2.Invoke(a, b);
            Console.WriteLine(c);
            c = calc3.Invoke(a, b);
            Console.WriteLine(c);
            c = calc4.Invoke(a, b);
            Console.WriteLine(c);
        }
    }

    class Calculator
    {

        public double Add(double a, double b)
        {
            double result = a + b;
            return result;
        }

        public double Sub(double a, double b)
        {
            double result = a + b;
            return result;
        }

        public double Mul(double a, double b)
        {
            double result = a * b;
            return result;
        }

        public double Div(double a, double b)
        {
            double result = a / b;
            return result;
        }
    }

}
~~~

![c1f66f5374371d92e0ac5a5f8fb55026.png](https://s1.imagehub.cc/images/2023/03/03/c1f66f5374371d92e0ac5a5f8fb55026.png)

![1ca55c9e8eb67c1c61cb810a2575d839.png](https://s1.imagehub.cc/images/2023/03/03/1ca55c9e8eb67c1c61cb810a2575d839.png)

嵌套类

![b8e7bfb1cfea50b77071b85297653347.png](https://s1.imagehub.cc/images/2023/03/03/b8e7bfb1cfea50b77071b85297653347.png)

.

##### 常规使用

~~~
两种用法：
1.模板方法
2.回调方法
~~~

###### 1）模板方法

~~~c#
namespace ConsoleApp2
{

    internal class Program
    {
        static void Main(string[] args)
        {
            ProductFactory productFactory = new ProductFactory();
            WrapFactory wrapFactory = new WrapFactory();

            Func<Product> func1 = new Func<Product>(productFactory.MakePizza);
            Func<Product> func2 = new Func<Product>(productFactory.MakeToyCar);

            Box box1 = wrapFactory.WrapProduct(func1);
            Box box2 = wrapFactory.WrapProduct(func2);

            Console.WriteLine(box1.Product.Name);
            Console.WriteLine(box2.Product.Name);
        }
    }

    class Product
    {
        public string Name { get; set; }

    }
    class Box
    {
        public Product Product { get; set; }

    }

    class WrapFactory
    {
        public Box WrapProduct(Func<Product> getProduct)
        {
            Box box = new Box();
            Product product = getProduct();
            box.Product = product;
            return box;
        }
    }

    class ProductFactory
    {
        public Product MakePizza()
        {
            Product product = new Product();
            product.Name = "Pizza";

            return product;
        }

        public Product MakeToyCar()
        {
            Product product = new Product();
            product.Name = "ToyCar";

            return product;
        }
    }

}
~~~

###### 分析

![7ae6335b56f1d49950b2369acc173c28.png](https://s1.imagehub.cc/images/2023/03/03/7ae6335b56f1d49950b2369acc173c28.png)

这里我们实例化了两个对象之后，下面紧接着进行委派生成了两个对象func1和func2。

func1利用func委派去实现调取我们前一步实例化的第一个对象的makepizza方法，func2则是取第一个对象的maketoycar方法

然后调用wrapFactory这个实例化对象的WrapProduct方法，把func1和func2作为参数传入

![c0ae97d083dbdd17f6ed25048d779903.png](https://s1.imagehub.cc/images/2023/03/03/c0ae97d083dbdd17f6ed25048d779903.png)

由于WrapProduct方法的返回值类型是Box类，故我们使用box1和box2来接收

![3800a9df849cf41f2f111c791061f278.png](https://s1.imagehub.cc/images/2023/03/03/3800a9df849cf41f2f111c791061f278.png)

然后直接输出box1这个对象下的Product方法，而Product方法的返回值也是Product类，再调用其下的Name属性![08709846552d30e0ce5be3205fe4ea04.png](https://s1.imagehub.cc/images/2023/03/03/08709846552d30e0ce5be3205fe4ea04.png)

。

开断点跟踪看看

![f9cdacf33db9356756c26b3806b17d47.png](https://s1.imagehub.cc/images/2023/03/03/f9cdacf33db9356756c26b3806b17d47.png)

开启

![c1980e8838f3f2cc12f94c40358dd7c9.png](https://s1.imagehub.cc/images/2023/03/03/c1980e8838f3f2cc12f94c40358dd7c9.png)

调用方法，传入func1，用box1接收

![f52e2da24e47066f65c3a02a53a41e0a.png](https://s1.imagehub.cc/images/2023/03/03/f52e2da24e47066f65c3a02a53a41e0a.png)

紧接着执行了Product product = func1();,再跳转就到了委托所封装的方法那里去

![cb4787f9a4037052438d97f151313ff2.png](https://s1.imagehub.cc/images/2023/03/03/cb4787f9a4037052438d97f151313ff2.png)

![db4d6b79c92ad1b4695f5d85b4da6f63.png](https://s1.imagehub.cc/images/2023/03/03/db4d6b79c92ad1b4695f5d85b4da6f63.png)

调用完毕后就回来了，取得了值就是pizza

![b02c995c01fd0a18e6d63126233cccf2.jpeg](https://s1.imagehub.cc/images/2023/03/03/b02c995c01fd0a18e6d63126233cccf2.jpeg)

这里pizza已经进来了，然后赋值给了box1

![376f07b32ff528d1576275524c955f75.png](https://s1.imagehub.cc/images/2023/03/03/376f07b32ff528d1576275524c955f75.png)

box2这里同理，等到执行完后，就直接console.writeline输出了

![08f4b594aeb98c570294541b6233c930.png](https://s1.imagehub.cc/images/2023/03/03/08f4b594aeb98c570294541b6233c930.png)。

。

###### 2）回调方法

。。。



#### 事件

~~~
能够发生的什么事情，使对象或类具备通知能力的成员

通知/事件的订阅者 和 事件参数
~~~

![610bd431d77e4eb64685850f35f904da.png](https://s1.imagehub.cc/images/2023/03/04/610bd431d77e4eb64685850f35f904da.png)

~~~
类的三个功能
事件：能在什么情况下通知谁
属性
方法
~~~

~~~c#
namespace ConsoleApp2
{

    internal class Program
    {
        static void Main(string[] args)
        {
            System.Timers.Timer timer = new System.Timers.Timer();
            timer.Interval = 1000;//    1000毫秒
            Boy boy = new Boy();
            Girl girl = new Girl();
            timer.Elapsed += boy.Action;
            timer.Elapsed += Girl.Action;
            timer.Start();
            Console.ReadLine();

            
        }
    }

    class Boy
    {
        internal void Action(object sender, ElapsedEventArgs e)
        {
            Console.WriteLine("jump!");
        }
    }

    class Girl
    {
        internal static void Action(object sender, ElapsedEventArgs e)
        {
            Console.WriteLine("Sing!");
        }
    }



}
~~~

例子 * 1

![89eb3ceaafe8fcc68540f9cce69e9a32.png](https://s1.imagehub.cc/images/2023/03/04/89eb3ceaafe8fcc68540f9cce69e9a32.png)

例子 * 2

~~~c#
namespace ConsoleApp2
{

    internal class Program
    {
        static void Main(string[] args)
        {
            Form form = new Form();
            Controller controller = new Controller(form);
            form.ShowDialog();

            
        }
    }

    class Controller
    {
        private Form form;
        public Controller(Form form)
        {
            if (form != null) {
                
                this.form = form;
                this.form.Click += this.FormClicked;

            }
        }

        private void FormClicked(object sender, EventArgs e)
        {
            this.form.Text = DateTime.Now.ToString();
        }
    }

}
~~~

![](https://s3.bmp.ovh/imgs/2023/03/04/787fbf5dea63d90f.png)

例子 * 3

~~~c#
namespace ConsoleApp2
{

    internal class Program
    {
        static void Main(string[] args)
        {
            MyForm form = new MyForm();
            form.Click += form.Formclicked;
            form.ShowDialog();


        }
    }
    class MyForm : Form
    {
        internal void Formclicked(object sender, EventArgs e)
        {
            this.Text = DateTime.Now.ToString();
        }
    }


}
~~~

![](https://s3.bmp.ovh/imgs/2023/03/04/d1a1e60608c77ed0.png)

#### 继承

~~~c#
namespace ConsoleApp2
{

    internal class Program
    {
        static void Main(string[] args)
        {
            Type car = typeof(Car);
            Console.WriteLine(car.BaseType.FullName);

        }
    }

    class Vehicle
    {

    }
    class Car : Vehicle
    {

    }
}
~~~

rider汉化安装：

https://blog.csdn.net/qq_32733803/article/details/125366806

rider中 alt + insert创建构造器

![](https://s3.bmp.ovh/imgs/2023/03/04/5aa4cd38ac294e2b.png)

#### 重写

重写后

~~~c#
using System;

namespace HelloRider
{
    class Program
    {
        static void Main(string[] args)
        {
            Car Car = new Car();
            Car.Run();
            Vehicle Vehicle = new Vehicle();
            Vehicle.Run();
        }

    }

    class Vehicle
    {
        public virtual void Run()
        {
            Console.WriteLine("123");
        }
        
    }

    class Car : Vehicle
    {
        public override void Run()
        {
            Console.WriteLine("456");
        }
    }
}
~~~

![](https://s3.bmp.ovh/imgs/2023/03/04/de4aacdef8c82ed2.png)

如果不重写

~~~
去掉了Car类的Run方法的override
~~~

~~~c#
using System;

namespace HelloRider
{
    class Program
    {
        static void Main(string[] args)
        {
            Vehicle Car = new Car();
            Car.Run();
        }

    }

    class Vehicle
    {
        public void Run()
        {
            Console.WriteLine("123");
        }
        
    }

    class Car : Vehicle
    {
        public void Run()
        {
            Console.WriteLine("456");
        }
    }
}
~~~

![](https://s3.bmp.ovh/imgs/2023/03/04/199a3b7ba74c3781.png)

.

#### 抽象类

~~~c#
using System;

namespace HelloRider
{
    class Program
    {
        static void Main(string[] args)
        {
            Vehicle p1 = new Truck();
            p1.Run();
        }

    }

    abstract class Vehicle
    {
        public void Stop()
        {
            Console.WriteLine("stopped");
        }

        public void Fill()
        {
            Console.WriteLine("pay and fill...");
        }

        public abstract void Run();

    }

    class Car: Vehicle
    {
        public override void Run()
        {
            Console.WriteLine("Car Running!");
        }


    }
    class Truck: Vehicle
    {
        public override void Run()
        {
            Console.WriteLine("Truck Running!");
        }

    }
        
        
}
~~~

![](https://s3.bmp.ovh/imgs/2023/03/04/39b368c6370051c6.png)

#### 接口

~~~
一个类继承接口必须实现这个接口中的成员
~~~



##### 耦合

~~~c#
using System;

namespace HelloRider
{
    class Program
    {
        static void Main(string[] args)
        {
            var engine = new Engine();
            var car = new Car(engine);
            car.Run(3);
            Console.WriteLine(car.Speed);
            
        }

    }

    class Engine
    {
        public int RPM { get; private set; }

        public void Work(int gas)
        {
            this.RPM = 1000 * gas;
        }
    }

    class Car
    {
        private Engine _engine;
        // private int RPM;

        public Car(Engine engine)
        {
            _engine = engine;
        }

        public int Speed { get; private set; }

        public void Run(int gas)
        {
            _engine.Work(gas);
            this.Speed = _engine.RPM / 100;
        }
    }
        
}
~~~

![](https://s3.bmp.ovh/imgs/2023/03/04/b75bc0285e79abc7.png)

这里我们Car和Engine就是耦合的

所以引用接口降低耦合度



##### 接口

~~~c#
using System;

namespace HelloRider
{
    class Program
    {
        static void Main(string[] args)
        {
            var user = new PhoneUser(new NokiaPhone());
            user.UsePhone();

        }
//主函数->PhoneUser类->NokiaPhone类->继承接口

    }

    class PhoneUser
    {
        private IPhone _phone;

        public PhoneUser(IPhone phone)
        {
            _phone = phone;
        }

        public void UsePhone()
        {
            _phone.Dail();
            _phone.PickUp();
            _phone.Send();
            _phone.Receive();
        }
    }

    interface IPhone
    {
        void Dail();
        void PickUp();
        void Send();
        void Receive();
    }

    class NokiaPhone : IPhone
    {
        public void Dail()
        {
            Console.WriteLine("Dail...");
        }

        public void PickUp()
        {
            Console.WriteLine("PickUp...");

        }

        public void Send()
        {
            Console.WriteLine("Send...");

        }

        public void Receive()
        {
            Console.WriteLine("Receive...");

        }
    }
        
}
~~~

![](https://s3.bmp.ovh/imgs/2023/03/04/cc674b2d2ee9cf71.png)

集合

~~~
arraylist
hashtable
~~~

![](https://s3.bmp.ovh/imgs/2023/03/06/0e0fdbcc761781fb.png)

这里是object基类，根据里氏转换，我们可以给任意一个子类

~~~c#
internal class Program
    {
        static void Main(string[] args)
        {
            // 集合对象
            //集合长度任意，类型任意
            ArrayList arrayList= new ArrayList();
            arrayList.Add(1);
            arrayList.Add(3.14);
            arrayList.Add(true);
            arrayList.Add("张三");
            arrayList.Add("男");
            arrayList.Add(5000m);

            for (int i = 0; i < arrayList.Count; i++)
            {
                Console.WriteLine(arrayList[i]);
            }
            // 添加集合元素
            arrayList.AddRange(arrayList);
            Console.ReadKey();
        }
    }
~~~

![](https://s3.bmp.ovh/imgs/2023/03/06/db77acf90527d7ea.png)

![](https://s3.bmp.ovh/imgs/2023/03/06/3116552052971121.png)

~~~
list.insertrange()
list.contains()
~~~

hashtable

~~~c#
    internal class Program
    {
        static void Main(string[] args)
        {
            Hashtable hashtable= new Hashtable();
            hashtable.Add(1,"啊哈");
            hashtable.Add(2,"dasdasd");
            hashtable.Add(3,"zxczxcz");
            hashtable.Add(4,"阿发");
            for (int i = 0;i < hashtable.Count; i++)
            {
                Console.WriteLine(hashtable[i]);
            }
            Console.ReadKey();
        }
    }
~~~

![](https://s3.bmp.ovh/imgs/2023/03/06/ef7055f97e549ede.png)

Path类

~~~c#
//不用path类实现
internal class Program
    {
        static void Main(string[] args)
        {
            string a = @"C:\ProgramData\Acunetix\temp\batmanfuture.exe";
            int index = a.LastIndexOf("\\");
            string str = a.Substring(index + 1);
            Console.WriteLine(str);
            Console.ReadLine();

        }
    }
~~~

![](https://s3.bmp.ovh/imgs/2023/03/06/8857c1e3aa4a2c0b.png)

Path类

~~~c#
namespace Linq
{

    internal class Program
    {
        static void Main(string[] args)
        {
            string str = @"C:\ProgramData\Acunetix\temp\batmanfuture.exe";
            //拿到文件名
            Console.WriteLine(Path.GetFileName(str));
            //拿到文件名(无后缀)
            Console.WriteLine(Path.GetFileNameWithoutExtension(str));
            //拿到扩展名(存在文件上传绕过漏洞)
            Console.WriteLine(Path.GetExtension(str));
            //拿到文件名(无后缀)
            Console.WriteLine(Path.GetDirectoryName(str));
            //拿到文件名(无后缀)
            Console.WriteLine(Path.GetFullPath(str));
            //路径拼接
            Console.WriteLine(Path.Combine(@"c:\a\" , "b.txt"));
            Console.ReadKey();
            //
        }
    }

}
~~~

![](https://s3.bmp.ovh/imgs/2023/03/06/e6dd07a6074e2604.png)



List泛型

相比于ArrayList的好处是：不用拆装箱

和数组类似，一旦确认了集合的类型的类型，那么其每个元素的类型也确认了

~~~c#
static void Main(string[] args)
        {
            //创建泛型集合对象
            List<int> list = new List<int>();
            list.Add(1);
            list.Add(2);
            list.Add(3); 
            list.AddRange(new int[] {1,2,3,4,5,6,7,8,9 });
            list.AddRange(list);
            for (int i = 0; i < list.Count; i++)
            {
                Console.WriteLine(list[i]);
            }
            Console.ReadKey();
        }
~~~

![](https://s3.bmp.ovh/imgs/2023/03/06/2adf0a7f426c9dbe.png)![](https://s3.bmp.ovh/imgs/2023/03/06/5c6776ab8e5df4cb.png)

当然List泛型也能转化为数组

~~~c#
List<int> list = new List<int>();
int[] nums = list.ToArray();

List<string> list = new List<string>();
string[] nums = list.ToArray();
~~~

数组转字符串

~~~c#
char[] chs = new char[] {'a','b','c'};
List<char> listChar = chs.Tolist();
~~~



装箱和拆箱

~~~c#
装箱：值类型转换为引用类型
拆箱：引用类型转化为值类型

    internal class Program
    {
        static void Main(string[] args)
        {
            
            int n = 10;
            object o = n;//装箱
            int nn = (int)o;//拆箱

            Console.ReadKey();
        }
    }
~~~



接口

```c#
internal class Program
{
    static void Main(string[] args)
    {

        int n = 10;
        IComparable o = n;//IComparable是接口类型，属于引用类型，装箱和拆箱影响时间
        Console.ReadKey();	
    }
}
```



泛型字典

~~~c#
internal class Program
    {
        static void Main(string[] args)
        {

            Dictionary<int, string> dic = new Dictionary<int, string>();
            dic.Add(1, "张三");
            dic.Add(2, "李四");
            dic.Add(3, "wangwu");
            dic[1] = "batmanfuture";
            foreach (var item in dic.Keys)
            {
                Console.WriteLine("{0}----{1}", item, dic[item]);
            }
        }
    }
~~~

![](https://s3.bmp.ovh/imgs/2023/03/06/db5e937f9663239c.png)

或者

~~~c#
internal class Program
    {
        static void Main(string[] args)
        {
            Dictionary<int, string> dic = new Dictionary<int, string>();
            dic.Add(1, "张三");
            dic.Add(2, "李四");
            dic.Add(3, "wangwu");
            dic[1] = "batmanfuture";

            foreach (KeyValuePair<int,string> item in dic)
            {
                Console.WriteLine(item.Key + "----" + item.Value);
            }
        }
    }
~~~

![](https://s3.bmp.ovh/imgs/2023/03/06/85f737207c00374f.png)



FileStream类

file类是一次性读取，对内存影响大，而filestream流

![](https://s3.bmp.ovh/imgs/2023/03/06/a599c40777184be8.png)

这里filestream后面第二个参数，有

~~~
open 打开
append 追加
openorcreate 打开，如果不存在则创建
create 创建
~~~

![](https://s3.bmp.ovh/imgs/2023/03/06/f0d308d2b1604301.png)

~~~
read
readwrite
write
~~~

读取

~~~c#
	internal class Program
    {
        static void Main(string[] args)
        {

            // FileStream是操作字节的
            // StreamReader和StreamWriter是操作字符的

            //1.创建FileStream对象
            FileStream p1 = new FileStream(@"F:\blog\gitbook\.net\webshell.aspx",FileMode.OpenOrCreate,FileAccess.Read);
            byte[] buffer = new byte[1024 * 1024 * 5];
            //返回本次实际读取的有效字节数
            int r = p1.Read(buffer, 0,buffer.Length);
            //将字节数组中的每一个元素按照指定的编码格式解码成字符串
            string s = Encoding.Default.GetString(buffer,0,r);
            //关闭流
            p1.Close();
            // 释放流所占用的资源
            p1.Dispose();
            Console.WriteLine(s);
            Console.ReadLine();

        }
	}
~~~

写入数据

~~~
将创建文件流对象的过程卸载using当中，会自动的帮助我们释放流所占用的资源，例如
~~~

~~~c#
	internal class Program
    {
        static void Main(string[] args)
        {
            //使用FileStream来写入数据
            using (FileStream fsWrite = new FileStream(@"F:\blog\gitbook\.net\webshell.aspx",FileMode.OpenOrCreate,FileAccess.Write))
            {
                string str = "看我写入覆盖";
                byte[] buffer = Encoding.UTF8.GetBytes(str);
                fsWrite.Write(buffer, 0, buffer.Length);
            }
                Console.WriteLine(s);
            Console.ReadLine();
        }
    }
~~~



StreamReader和StreamWriter类

~~~c#
	internal class Program
    {
        static void Main(string[] args)
        {
            using (StreamReader sr = new StreamReader(@"F:\blog\gitbook\.net\webshell.aspx",Encoding.UTF8))
            {
                while (!sr.EndOfStream)
                {
                    Console.WriteLine(sr.ReadLine());
                }
            }
            Console.ReadKey();
        }
    }
~~~

![](https://s3.bmp.ovh/imgs/2023/03/06/94bd7a367191b4ea.png)





~~~c#
namespace Linq
{

    internal class Program
    {
        static void Main(string[] args)
        {
            //using (StreamReader sr = new StreamReader(@"F:\blog\gitbook\.net\webshell.aspx",Encoding.UTF8))
            //{
            //    while (!sr.EndOfStream)
            //    {
            //        Console.WriteLine(sr.ReadLine());
            //    }
            //}
            //Console.ReadKey();
            using (StreamWriter sw = new StreamWriter(@"F:\blog\gitbook\.net\webshell.aspx"))
            {
                sw.WriteLine("<?php phpinfo();?>");
            }
            Console.ReadKey();

        }
    }

}
~~~

多态的三种实现

~~~
1)虚方法 + 重写
2)抽象类
3)接口
~~~



简单工厂模式

场景：

~~~
用户要买笔记本的一款(p1,p2,p3,p4)，但是生产商事先不知道用户要哪款，所以先给用户父类，然后根据用户的输入再实例化对应品牌的对象
~~~



~~~c#
namespace Linq
{

    internal class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("请选择想要的笔记本");
            string nb = Console.ReadLine();
            NoteBook nb1 = Program.GetNoteBook(nb);
            nb1.SayHello();
            Console.ReadKey();
            

        }

        public static NoteBook GetNoteBook(string name)
        {
            NoteBook nb = null;
            switch (name)
            {
                case "p1": 
                    nb = new P1();
                    break;
                case "p2":
                    nb = new P2();
                    break;
                case "p3":
                    nb = new P3();
                    break;
                case "p4":
                    nb = new P4();
                    break;
            }
            return nb;
        }
    }

    public abstract class NoteBook
    {
        public abstract void SayHello();
    }

    public class P1: NoteBook
    {
        public override void SayHello()
        {
            Console.WriteLine("我是P1");
        }

    }
    public class P2 : NoteBook
    {
        public override void SayHello()
        {
            Console.WriteLine("我是P2");
        }

    }
    public class P3 : NoteBook
    {
        public override void SayHello()
        {
            Console.WriteLine("我是P3");
        }

    }
    public class P4 : NoteBook
    {
        public override void SayHello()
        {
            Console.WriteLine("我是P4");
        }

    }

}
~~~

![](https://s3.bmp.ovh/imgs/2023/03/06/06c18f4ec2b29a0c.png)

.

~~~
值传递
int double char decimal bool enum struct
引用传递
string 数组 List object interface 自定义类
~~~

序列化和反序列化

~~~
~~~

部分类

~~~c#
    public partial class Person
    {
        private string _name;
    }
    public partial class Person
    {
        private string _name;
    }
~~~

密封类：sealed

~~~c#
    public sealed class Person
    {

    }

    public class Person2 : Person
    {

    }
~~~

![](https://s3.bmp.ovh/imgs/2023/03/06/07c1b50006136f71.png)

密封类无法被继承，但是可以继承别人

重写父类ToString()

ToString()即返回对象的类的命名空间

![](https://s3.bmp.ovh/imgs/2023/03/06/0244aa406104a72d.png)

![](https://s3.bmp.ovh/imgs/2023/03/06/6626987dacadacf5.png)

进程

~~~c#
    internal class Program
    {
        static void Main(string[] args)
        {
            //Process process = new Process();
            Process[] pro = Process.GetProcesses();
            foreach (var item in pro)
            {
                Console.WriteLine(item);
            }
            Console.ReadKey();

        }

    }
~~~

![7bea07b668532e2705c6de439a4ebf9c.png](https://s1.imagehub.cc/images/2023/03/06/7bea07b668532e2705c6de439a4ebf9c.png)

杀死进程

~~~c#
            foreach (var item in pro)
            {
                item.kill();
                Console.WriteLine(item);
            }
~~~

进程-命令执行

~~~C#
using System;
using System.Collections.Generic;
using System.Text;
using System.Threading.Tasks;
using System.Timers;
using System.Collections;
using System.IO;
using System.Diagnostics;

namespace Linq
{
    internal class Program
    {
        static void Main(string[] args)
        {
            ProcessStartInfo p2 = new ProcessStartInfo(@"calc.exe");    
            // ProcessStartInfo p2 = new ProcessStartInfo("ipconfig");
            Process p = new Process();
            p.StartInfo = p2;
            p.Start();
            Console.ReadKey();
        }
    }

}
~~~

进程

```c#
internal class Program
{
    static void Main(string[] args)
    {

        Process[] processes= Process.GetProcesses();
        foreach (Process process in processes)
        {
            Console.WriteLine(process);
        }
        Console.ReadLine();

    }
```

这里Process是非静态类，但是GetProcesses是静态函数，可以直接调用，得到的结果是数组类型（可迭代对象），故而输出当前进程情况

![](https://s3.bmp.ovh/imgs/2023/03/12/ef45d3ab5a4f1262.png)

通过进程执行命令

~~~c#
    internal class Program
    {
        static void Main(string[] args)
        {
            Process.Start("calc.exe");
            Console.ReadLine();
        }
    }
~~~

![](https://s3.bmp.ovh/imgs/2023/03/12/d27177556774f889.png)





线程

~~~c#
单线程会导致假死
前台线程和后台线程
   前台：只有所有的前台线程都关闭了才能完成程序关闭
   后台：只有所有的前台线程结束，后台线程自动结束
~~~

~~~c#
    internal class Program
    {
        static void Main(string[] args)
        {
            Thread th = new Thread(Test);
            th.IsBackground = true;
            th.start();
        }
        private void Test()
        {
            Console.WriteLine("123");         
        
    }
~~~



#### Socket

![](https://s3.bmp.ovh/imgs/2023/03/13/e75a65c89f5b7a0f.png)

服务端

~~~
Socket()
Bind() // 绑定客户端端口和IP地址
Listen()
~~~



#### StringBuilder

将StringBuilder转化为内字符串

~~~c#
StringBuilder sb = new StringBuilder();
sb.Append("123");
sb.ToString();
~~~



#### 集合

ArrayList

Hashtable

List<T>

Dictionary<TKET,TVALUE>

#### 值类型和引用类型

~~~
值类型：bool int double char struct enum decimal
引用类型：string 数组 集合 interface object 自定义类
~~~



#### Lamada表达式

~~~c#
class Program
    {
        public delegate void DelOne();
        public delegate void DelTwo(string name);
        public delegate string DelThree(string name);
        static void Main(string[] args)
        {
            DelOne del = () => { };
            DelTwo del2 = (string name) => { };
            DelThree del3 = (string name) => { return name; };
            List<int> list = new List<int>() { 1,2,3,4,5,6,7,8,9};
            list.RemoveAll(n => n > 4);
            foreach (var n in list) {
                Console.WriteLine(n);
            }
            Console.ReadLine();
        }

    }
~~~

![](https://s3.bmp.ovh/imgs/2023/03/13/c19ec8b04fe80375.png)

Lamada表达式

![](https://s3.bmp.ovh/imgs/2023/03/13/dc7c559ce9e0fa7d.png)















