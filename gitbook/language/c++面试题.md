##### 1.在32位操作系统下，一个指针占用多少内存？

四个字节

64位下就是八个字节

##### 2.什么是空指针，什么是野指针？

空指针：内存中编号为0，可以用来初始化指针变量

~~~c++
int * p = NULL;
~~~

野指针：指针变量指向非法的内存空间

~~~c++
int * p = (int *)0x1100;
~~~

##### 3.指针常量和常量指针？

~~~c++
int * const p = &a;			指针常量
特点：指针的指向不可以改，指向的值可以改
*p = 20
~~~

~~~c++
const int * p = &a;			常量指针
特点：指针的指向可以改，指向的值步可以改
p = &a;
~~~

##### 4.在c++中内存的分区模型？

代码区（共享只读）：存放函数体的二进制代码，由操作系统进行管理的

全局区：全局变量，静态变量，常量

栈区：编译器管理的，函数的参数值和局部变量

~~~c++
int * func()
{
	int a = 10;
	return &a;
}
int main() {
	int *p = func();
	cout << *p << endl;
	cout << *p << endl;
	system("pause");
	return 0;
}
~~~

堆区：程序员分配和释放

用new在堆区开辟数据

~~~c++
int* func()
{
	int* a = new int(10);
	return a;
}

int main() {

	int *p = func();

	cout << *p << endl;
	cout << *p << endl;
    
	system("pause");

	return 0;
}
~~~

##### 5.c++的引用是什么？

本质是指针常量

~~~
引用是一个别名，它是一个已经存在的变量的别名，而指针是一个变量，它存储了另一个变量的地址。
引用在定义时必须 初始化，而指针可以在任何时候初始化。
引用不能被重新赋值，而指针可以被重新赋值。(引用本质是本质是指针常量)
引用不能为NULL，而指针可以为NULL。
引用在使用时不需要解引用，而指针需要使用解引用运算符*来 访问指针所指向的值。
~~~



##### 6.在c++中struct和class的区别？

* struct 默认权限为公共
* class   默认权限为私有

~~~c++
#include <iostream>

using namespace std;

class C1
{
	int  m_A; //默认是私有权限
};

struct C2
{
	int m_A;  //默认是公共权限
};

int main() {

	C1 c1;
	//c1.m_A = 10; //错误，访问权限是私有

	C2 c2;
	c2.m_A = 10; //正确，访问权限是公共

	system("pause");

	return 0;
}
~~~

##### 7.在c++和java中构造函数和析构函数是？

c++，析构函数不需要有参数

~~~c++
#include <iostream>
using namespace std;

class Person
{
public:
	//构造函数
	Person()
	{
		cout << "Person的构造函数调用" << endl;
	}
	//析构函数
	~Person()
	{
		cout << "Person的析构函数调用" << endl;
	}
};
void test01()
{
	Person p;
}
int main() {
	test01();
	system("pause");
	return 0;
}
~~~

java，Java 中没有析构函数

~~~java
class Person {
    private String name;
    private int age;

    // 构造函数
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
        System.out.println("构造函数");
    }

    // getter 和 setter 方法
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public int getAge() {
        return age;
    }
    public void setAge(int age) {
        this.age = age;
    }
}

public class Hello{
    public static void main(String[] args){
        Person p = new Person("Alice", 20);
    }
}
~~~

##### 8.c++中的拷贝构造函数是什么？

如下，简单来说就是将一个该类的对象作为参数实例化该类的对象

~~~c++
#include <iostream>
using namespace std;


class MyClass {
public:
    int* data;
    int size;

    // 拷贝构造函数
    MyClass(const MyClass& other) : size(other.size) {
        data = new int[size];
        for (int i = 0; i < size; ++i) {
            data[i] = other.data[i];
        }
        std::cout << "调用了拷贝构造函数！" << std::endl;
    }

    // 构造函数
    MyClass(int s) : size(s) {
        data = new int[size];
        for (int i = 0; i < size; ++i) {
            data[i] = i;
        }
        std::cout << "调用了构造函数！" << std::endl;
    }

    // 析构函数
    ~MyClass() {
        delete[] data;
        std::cout << "调用了析构函数！" << std::endl;
    }
};

int main() {
    MyClass obj1(5);  // 调用构造函数
    MyClass obj2 = obj1;  // 调用拷贝构造函数
    return 0;
}
~~~

##### 9.c++的深拷贝和浅拷贝？

浅拷贝：简单的赋值拷贝操作

深拷贝：在堆区重新申请空间，进行拷贝操作

##### 10.什么是静态成员变量？

~~~c++
class Person
{
	
public:

	static int m_A; //静态成员变量

	//静态成员变量特点：
	//1 在编译阶段分配内存
	//2 类内声明，类外初始化
	//3 所有对象共享同一份数据
}
~~~



##### 11.友元是什么？

友元的三种实现方式：

~~~
全局函数做友元
类做友元
成员函数做友元
~~~

###### 全局函数做友元

~~~c++
class Building
{
	//告诉编译器 goodGay全局函数 是 Building类的好朋友，可以访问类中的私有内容
	friend void goodGay(Building * building);

public:

	Building()
	{
		this->m_SittingRoom = "客厅";
		this->m_BedRoom = "卧室";
	}


public:
	string m_SittingRoom; //客厅

private:
	string m_BedRoom; //卧室
};


void goodGay(Building * building)
{
	cout << "好基友正在访问： " << building->m_SittingRoom << endl;
	cout << "好基友正在访问： " << building->m_BedRoom << endl;
}


void test01()
{
	Building b;
	goodGay(&b);
}

int main(){

	test01();

	system("pause");
	return 0;
}
~~~

###### 类做友元

这里的Building是在类外作实现的

~~~c++
class Building;
class goodGay
{
public:

	goodGay();
	void visit();

private:
	Building *building;
};


class Building
{
	//告诉编译器 goodGay类是Building类的好朋友，可以访问到Building类中私有内容
	friend class goodGay;

public:
	Building();

public:
	string m_SittingRoom; //客厅
private:
	string m_BedRoom;//卧室
};

Building::Building()
{
	this->m_SittingRoom = "客厅";
	this->m_BedRoom = "卧室";
}

goodGay::goodGay()
{
	building = new Building;
}

void goodGay::visit()
{
	cout << "好基友正在访问" << building->m_SittingRoom << endl;
	cout << "好基友正在访问" << building->m_BedRoom << endl;
}

void test01()
{
	goodGay gg;
	gg.visit();

}

int main(){

	test01();

	system("pause");
	return 0;
}
~~~

###### 成员函数做友元

~~~c++

class Building;
class goodGay
{
public:

	goodGay();
	void visit(); //只让visit函数作为Building的好朋友，可以发访问Building中私有内容
	void visit2(); 

private:
	Building *building;
};


class Building
{
	//告诉编译器  goodGay类中的visit成员函数 是Building好朋友，可以访问私有内容
	friend void goodGay::visit();

public:
	Building();

public:
	string m_SittingRoom; //客厅
private:
	string m_BedRoom;//卧室
};

Building::Building()
{
	this->m_SittingRoom = "客厅";
	this->m_BedRoom = "卧室";
}

goodGay::goodGay()
{
	building = new Building;
}

void goodGay::visit()
{
	cout << "好基友正在访问" << building->m_SittingRoom << endl;
	cout << "好基友正在访问" << building->m_BedRoom << endl;
}

void goodGay::visit2()
{
	cout << "好基友正在访问" << building->m_SittingRoom << endl;
	//cout << "好基友正在访问" << building->m_BedRoom << endl;
}

void test01()
{
	goodGay  gg;
	gg.visit();

}

int main(){
    
	test01();

	system("pause");
	return 0;
}
~~~



##### 12.在python，php，java，c#，c++中继承是怎么写的？

python

~~~python
class A:
    pass
class A(B):
    pass
~~~

php

~~~php
<?php
class Animal {
	pass;
}
class Cat extends Animal {
	pass;
}
?>
~~~

java

~~~java
同上，都是extends
~~~

c#

~~~c#
class Parent {
	pass;
}

class Child : Parent {
	pass;
}
~~~

c++

~~~c++
class Parent {
	pass;
};

class Child : public Parent {
	pass;
};
~~~



##### 13.在c++中如何读写文件？

~~~c++
1. 包含头文件   
   \#include <fstream\>
2. 创建流对象  
   ofstream ofs;
3. 打开文件
   ofs.open("文件路径",打开方式);
4. 写数据
   ofs << "写入的数据";
5. 关闭文件
   ofs.close();
6. ifstream
    
~~~



~~~c++
#include <fstream>
void test01()
{
	ofstream ofs;
	ofs.open("test.txt", ios::out);
	ofs << "姓名：张三" << endl;
	ofs << "性别：男" << endl;
	ofs << "年龄：18" << endl;
	ofs.close();
}
int main() {
	test01();
	system("pause");
	return 0;
}
~~~



##### 14.volatile关键字是什么？

```
易变性，不可优化
```

~~~
用于告诉编译器该变量的值可能随时会被外部因素改变，从而防止编译器进行一些优化，以确保程序运行的正确性
~~~

~~~c++
#include <iostream>
using namespace std;
volatile int counter = 0;
void increment() {
    counter++;
}
int main() {
    while (counter < 10) {
        increment();
        cout << "Counter value: " << counter << endl;
    }
    return 0;
}
~~~

##### 15.extern关键字是？

~~~
extern 关键字可以用于声明一个变量或函数是在其他文件中定义的，并且在本文件中使用。它告诉编译器该标识符的定义在别处，不要为它分配内存空间或生成代码。
~~~

首先，在 `myfunc.cpp` 文件中定义 `g_num` 和 `PrintNum()` 函数：

```
cpp复制代码// myfunc.cpp

#include <iostream>

int g_num = 42;

void PrintNum()
{
    std::cout << "The number is: " << g_num << std::endl;
}
```

然后，在 `main.cpp` 文件中使用 `extern` 关键字声明 `g_num` 和 `PrintNum()` 函数，并分别调用它们：

```
cpp复制代码// main.cpp

#include <iostream>

extern int g_num;  // 声明 g_num
extern void PrintNum();  // 声明 PrintNum 函数

int main()
{
    std::cout << "The global number is: " << g_num << std::endl;  // 使用 g_num
    PrintNum();  // 调用 PrintNum 函数
    return 0;
}
```

需要注意的是，在 `main.cpp` 文件中使用 `extern` 关键字声明的变量或函数名不能被重新定义，否则会导致编译错误

##### 16.static关键字是什么？

~~~c++
修饰静态函数和静态变量

静态变量:只初始化一次
#include <iostream>
using namespace std;

void func() {
    static int counter = 0; // 定义静态变量
    counter++;
    cout << "Counter value: " << counter << endl;
}

int main() {
    func();
    func();
    func();
    return 0;
}
静态函数:该函数只能被该类的实例或其他静态成员函数调用
#include <iostream>
using namespace std;

class MyClass {
public:
    static void staticFunc() {
        cout << "Hello from static function!" << endl;
    }

    void normalFunc() {
        cout << "Hello from normal function!" << endl;
    }
};

int main() {
    MyClass::staticFunc(); // 调用静态函数
    MyClass obj;
    obj.normalFunc(); // 调用普通函数
    return 0;
}
~~~

##### 17.宏定义和展开、内联函数区别

~~~
宏定义和展开是 C++ 中的预编译过程，而内联函数是在编译阶段进行的
~~~

~~~
宏定义是一种简单的文本替换机制，通过 #define 关键字定义一个标识符表示某个值或表达式。展开就是指预处理器将宏定义替换成相应的文本内容的过程。展开后的代码会生成一个新的源代码文件，即预处理的结果。
extern 关键字可以用于声明一个变量或函数是在其他文件中定义的，并且在本文件中使用。它告诉编译器该标识符的定义在别处，不要为它分配内存空间或生成代码。
~~~

~~~c++
#include <iostream>

// 声明内联函数
inline int GetMax(int a, int b)
{
    return (a > b) ? a : b;
}

int main()
{
    int a = 10;
    int b = 20;
    int max = GetMax(a, b);  // 在此处展开 GetMax 函数

    std::cout << "The bigger number is: " << max << std::endl;
    return 0;
}

~~~

##### 18.STL六大组件是什么？

~~~
容器，算法，仿函数，迭代器，适配器(配接器)，空间配置器
容器：各种数据结构，例如vector，list，deque，set，map等，用来存放数据
算法：各种常见的算法
迭代器，例如数组可以通过for循环可迭代对象
适配器：设计模式中的，用来修饰
仿函数：算法的某种策略
空间配置器：负责空间的配置与管理
~~~

##### 容器

~~~
序列式容器：值的排序
关联式容器：二叉树结构
~~~

##### 序列式容器

vector

~~~c++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;


void MyPrint(int val)
{
    cout << val << endl;
}

int main()
{
    vector<int> v;
    v.push_back(100);
    v.push_back(200);
    v.push_back(300);
    v.pop_back();
    vector<int>::iterator pBegin = v.begin();
    vector<int>::iterator pEnd = v.end();

    for_each(v.begin(), v.end(), MyPrint);

}
~~~

##### 19.list和vector有什么区别？

~~~
vector拥有一段连续的内存空间，因此支持随机存取，如果需要高效的随即存取，而不在乎插入和删除的效率，使用vector。
list拥有一段不连续的内存空间，因此不支持随机存取，如果需要大量的插入和删除，而不关心随即存取，则应使用list。
~~~
