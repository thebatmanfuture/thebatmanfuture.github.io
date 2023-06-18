###### 1）python的垃圾回收机制是什么？

引用计数，标记清除，分代回收

引用计数：当引用计数器为0时，说明该对象没有被引用，可以进行回收，就是无法检测到循环引用，即多个对象之间相互引用，导致无法回收这些对象，从而出现内存泄漏的问题

标记清除：为了解决以上的循环引用问题，利用标记清除，先在栈区找变量在堆区的所有引用，找到之后标记，然后通过对比堆区生下来没有被标记的就是循环引用可以清除

分代回收：将堆分为几个世代，通过对每个世代采用不同的垃圾回收策略来提高垃圾回收效率，新创建的对象被分配在第一代中，如果这些对象存活时间较长，则会晋升到更高层次的世代，如第二代和第三代

###### 2）格式化字符串输出可能导致哪些漏洞？

模板注入-jinja2

###### 3）什么是可变类型，什么是不可变类型？什么是深拷贝，什么是浅拷贝？

可变类型：值改变，id不变；字典，类，列表，不可变类型：值改变，id也改变；int，float，str

深拷贝：将两个列表的拷贝完全区分开

浅拷贝：针对可变类型，比如列表，浅拷贝就是更改引用；针对不可变类型，就是重新开辟一份

###### 4）global和nonlocal的区别是什么？

global是在局部修改全局的值，例如

~~~python
x = 10
def func():
    global x
    x = 222
func()
print(x)
~~~

nonlocal是实现想要修改函数外层包含的名字对应的值（不可变类型）

~~~python
x = 10
def f1():
    x = 11
    def f2():
        nonlocal x
        x = 22
    f2()
    print('f1内的x: ', x)
f1()	#	f1内的x:  22
~~~

###### 5）什么是闭包函数，它的优势是什么，请写一段闭包函数的代码？

闭包只是一种为函数传参的方式，通过第一层函数的返回值调用其内部的第二层函数;you

~~~python
def outer_func(x):
    def inner_func(y):
        return x + y
    return inner_func

# 创建闭包函数
add5 = outer_func(5)

# 调用闭包函数
print(add5(3)) # 输出 8
print(add5(7)) # 输出 12
~~~

###### 6）装饰器是什么，为什么要用装饰器？

装饰器本身是为了对某个函数实现添加功能的办法，而且不修改调用方式【保证开放封闭原则】

无参装饰器：

首先是先利用一个闭包，然后通过赋值的方式实现传入内部闭包函数后再调用的函数，即两层闭包，而有参装饰器就是三层闭包

~~~python
import time
def index(name,age,power):
    time.sleep(2)
    print("my name is %s,and my age is %s,my superpower is %s" %(name,age,power))
    return 123456

def outter(func):
    def wrapper(*args,**kwargs):
        start = time.time()
        msg = func(*args,**kwargs)
        stop = time.time()
        print(stop - start)
        return msg
    return wrapper

index = outter(index)	#  这里outter(index)中的index是指调用的函数名字，返回值是wrapper闭包函数
res = index('batmanfuture','11','123456')	#  这里引用闭包函数传入的值就是我们要调的函数的值
print("返回值res = %s" %res)
# 这里需要满足开放和封闭的原则
~~~

有参装饰器

三层闭包的作用就是当我们并不仅仅只是使用增加的一种index方法，而是可以选择有多种方法，那么除了已有的两层之外我们还应该在加一层来选择我们想要使用新增的哪种功能

~~~python
# 由于语法糖@的限制，outter函数只能有一个参数，并且该才是只用来接收被装饰对象的内存地址
def auth(db_type):
    def deco(func):
        def wrapper(*args, **kwargs):
            name = input('your name>>>: ').strip()
            pwd = input('your password>>>: ').strip()

            if db_type == 'file':
                print('基于文件的验证')
                if name == 'egon' and pwd == '123':
                    res = func(*args, **kwargs)  # index(1,2)
                    return res
                else:
                    print('user or password error')
            elif db_type == 'mysql':
                print('基于mysql的验证')
            elif db_type == 'ldap':
                print('基于ldap的验证')
            else:
                print('不支持该db_type')
        return wrapper
    return deco


@auth(db_type='file')  # @deco # index=deco(index) # index=wrapper
def index(x, y):
    print('index->>%s:%s' % (x, y))

@auth(db_type='mysql')  # @deco # home=deco(home) # home=wrapper
def home(name):
    print('home->>%s' % name)


@auth(db_type='ldap')  # 账号密码的来源是ldap
def transfer():
    print('transfer')
~~~



###### 7）什么是语法糖？

例如上面的那个`index = outter(index)`

可以通过@outter这种指定对应的装饰器，outter要放在index之前

~~~python
import time

def outter(func):
    def wrapper(*args,**kwargs):
        start = time.time()
        msg = func(*args,**kwargs)
        stop = time.time()
        print(stop - start)
        return msg
    return wrapper


@outter
def index(name,age,power):
    time.sleep(2)
    print("my name is %s,and my age is %s,my superpower is %s" %(name,age,power))
    return 123456


res = index('batmanfuture','11','123456')	#  这里引用闭包函数传入的值就是我们要调的函数的值
print("返回值res = %s" %res)
# 这里需要满足开放和封闭的原则
~~~

###### 8）什么是迭代器，什么是可迭代对象？

迭代器是指迭代取值的工具，迭代是一个重复的过程，每一次重复都是基于上一次的结果继续的，单纯的重复不是迭代，例如：

列表，字符串，元组，字典，集合，文件（依赖索引）

对于没有索引的，只要是内置有__ iter __魔术方法都成为可迭代对象，调用之后就可以不依赖索引取值，通过

__ next __方法

~~~python
d = {'a':1,'b':2,'c':3}
d2 = d.__iter__()
print(d2.__next__(),end="")
print(d2.__next__(),end="")
print(d2.__next__(),end="")	#	abc
~~~

可以利用StopIteration来实现取完之后的

~~~python
d={'a':1,'b':2,'c':3}
d_iterator=d.__iter__()
while True:
    try:
        print(d_iterator.__next__())
    except StopIteration:	# 抛出异常StopIteration
        break
~~~

###### 9）什么是生成器，有哪些应用场景，yield的作用是什么？

生成器是一种特殊的迭代器

当我们调用这个包含yield的函数时，就变成了生成器

~~~python
def func():
    xxxx
    yield
gg = func()
~~~



~~~python
def func():
    print("第一次")
    yield
    print("第二次")
    yield
    print("第三次")
    yield
g = func()
print(g)
g.__iter__()
g.__next__()
g.__iter__()
g.__next__()
# <generator object func at 0x7ff09210e900>
# 第一次
# 第二次
~~~

应用场景：分批次处理大量数据集，分批写入

###### 10）三元表达式？

~~~python
x = 3
y = 2
res = x + y if x > y else y
print(res)
~~~

###### 11）列表生成式，同理还有字典生成式等？

~~~python
expression for item in iterable if condition1
~~~

~~~python
# 创建一个包含1到10之间所有偶数的列表
even_numbers = [x for x in range(1, 11) if x % 2 == 0]

# 输出列表
print(even_numbers)
~~~

###### 12）匿名函数lambda是什么？代码展示？

~~~python
a = (lambda x, y : x +y)(1,2)
print(a)
~~~

###### 13）python中map，reduce，filter都是什么作用？

- `map()` 函数将一个函数应用于可迭代对象的每个元素，并返回结果列表。
- `reduce()` 函数将一个函数应用于可迭代对象中的所有元素，并返回单个值。
- `filter()` 函数根据传递的函数来筛选可迭代对象中的元素，并返回一个包含所有满足条件的元素的列表。

~~~python
from functools import reduce

# 使用map函数将一个列表中的所有元素加倍
numbers = [1, 2, 3, 4, 5]
doubled_numbers = list(map(lambda x: x * 2, numbers))
print(doubled_numbers) # 输出结果: [2, 4, 6, 8, 10]

# 使用reduce函数计算一个列表中所有数字的总和
numbers = [1, 2, 3, 4, 5]
sum_of_numbers = reduce(lambda x, y: x + y, numbers)
print(sum_of_numbers) # 输出结果: 15

# 使用filter函数从一个列表中筛选出所有偶数
numbers = [1, 2, 3, 4, 5]
even_numbers = list(filter(lambda x: x % 2 == 0, numbers))
print(even_numbers) # 输出结果: [2, 4]
~~~

###### 14）python的代码如何实现规范写法？

以下，如果不规范写也不会错误

~~~python
def register(name:str,age:int,hobbies:list)->int:
    print(name)
    print(age)
    print(hobbies)

register(1,'aaa',[1,2,3,4])
~~~

或者可以写字符串作为提示信息，不影响程序运行

~~~python
def register(name:"请传入字符串",age:"请传入整型",hobbies:"请传入列表")->int:
    print(name)
    print(age)
    print(hobbies)

register(1,'aaa',[1,2,3,4])
~~~

通过一个魔术方法可以查看到它的提示信息

~~~python
print(register.__annotations__)
#	{'name': '请传入字符串', 'age': '请传入整型', 'hobbies': '请传入列表', 'return': <class 'int'>}
~~~

###### 15）如何导包？

导入包后优先访问包下的__ init __.py

![image3d9373bffe5ff340.png](https://cdnjson.com/images/2023/05/25/image3d9373bffe5ff340.png)![image68291c008090c0fb.png](https://cdnjson.com/images/2023/05/25/image68291c008090c0fb.png)

###### 16）如何隐藏属性，为什么要隐藏属性？

在属性名前加__ 实现隐藏效果

~~~python
class Foo:
    __x = 1
    def __f1(self):
        print("dasdasda")

print(Foo._Foo__x)
print(Foo._Foo__f1(1))
# 1
# dasdasda
# None
~~~

隐藏属性是为了安全和后续对代码进行添加等操作，严格控制用户对属性的操作，但是和java的访问修饰符不同，java的是强制的，而python的是可以访问到的

###### 17）property是什么作用？

1-伪装方法成属性

~~~python
class People:
    def __init__(self, name, weight, height):
        self.name = name
        self.weight = weight
        self.height = height

    # 定义函数的原因1：
    # 1、从bmi的公式上看，bmi应该是触发功能计算得到的
    # 2、bmi是随着身高、体重的变化而动态变化的，不是一个固定的值
    #    说白了，每次都是需要临时计算得到的

    # 但是bmi听起来更像是一个数据属性，而非功能
    @property
    def bmi(self):
        return self.weight / (self.height ** 2)
a = People("batman",10,20)
print(a.bmi)
~~~

2-

~~~python
class People:
    def __init__(self, name):
        self.__name = name

    def get_name(self):
        return self.__name

    def set_name(self, val):
        if type(val) is not str:
            print('必须传入str类型')
            return
        self.__name = val

    def del_name(self):
        print('删除了')
        del self.__name

    # name=property(get_name,set_name,del_name)

obj1=People('egon')

print(obj1.get_name())
obj1.set_name('EGON')
print(obj1.get_name())
obj1.del_name()
~~~

在不使用property前，是直接通过xxx.xxx或者xxx.xxx('asdasd')来实现的，而通过property可以实现xxx.xxx或者xxx.xxx = 'xxxx'，更像是直接调用属性赋值等操作[利用了重载]

~~~python
class People:
    def __init__(self, name):
        self.__name = name

    def get_name(self):
        return self.__name

    def set_name(self, val):
        if type(val) is not str:
            print('必须传入str类型')
            return
        self.__name = val

    def del_name(self):
        print('删除了')
        del self.__name

    name=property(get_name,set_name,del_name)

obj1=People('egon')

print(obj1.name) #
obj1.name = 18
del obj1.name
~~~

而直接test方法上写`@property`就是执行了`test = property(test)`，同样写多个就是重载

###### 18）经典类和新式类的区别是什么？

python3都是新式类，而python2存在经典类和新式类（默认情况下创建的类是经典类）

~~~python
class A(object): # 新式类  python2
    pass
class A:	# 经典类  
    pass
~~~



主要是在：多重继承、属性查找顺序和方法解析顺序（MRO）等方面

|                            | 经典类python2            | 新式类python3        |
| -------------------------- | ------------------------ | -------------------- |
| 属性查找顺序和方法解析顺序 | 深度优先遍历             | 广度优先遍历         |
|                            | 没有显式地继承 object 类 | 显式地继承 object 类 |
|                            |                          |                      |

###### 19）Mixins机制是什么？[区别于抽象类]

mixins机制和抽象类都是实现代码复用的方式，但Mixin是一种通过多重继承来实现代码重复使用和解耦的方式。

在多继承的背景下提高代码的可读性，Mixin定义了一些通用的方法或属性，并且可以与其他类进行组合。通过继承Mixin类，其他类就能够获得这些通用的方法或属性，从而扩展自己的功能。

比如直升机，民航飞机都能飞，且都是电动的，那么他们各自都有两个父类，那么"飞"和"电动"的方法都应该放在父类里，这样方便后续再添加"滑翔机"等

~~~python
class Vehicle:
    pass
class FlyableMixin:
    def fly(self):
        pass
class CivilAircraft(FlyableMixin,Vehicle):  # 民航飞机
    pass
class Helicopter(FlyableMixin,Vehicle):  # 直升飞机
    pass
class Car(Vehicle):  # 汽车并不会飞，但按照上述继承关系，汽车也能飞了
    pass
import socketserver
# 补充：通常Mixin结果的类放在左边
~~~



###### 20）在子类中重用父类的两种方式？

方式一：指名道姓调用某一个类下的函数=》不依赖于继承关系

~~~python
class OldboyPeople:
    def __init__(self,name,age,sex):
        self.name=name
        self.age=age
        self.sex=sex

    def f1(self):
        print('%s say hello' %self.name)


class Teacher(OldboyPeople):
    def __init__(self,name,age,sex,level,salary):
        OldboyPeople.__init__(self,name,age,sex)

        self.level = level
        self.salary=salary

tea_obj=Teacher('egon',18,'male',10,3000)
print(tea_obj.__dict__)
~~~

方式二：super()调用父类提供给自己的方法=》严格依赖继承关系

~~~python
class OldboyPeople:
    def __init__(self,name,age,sex):
        self.name=name
        self.age=age
        self.sex=sex

    def f1(self):
        print('%s say hello' %self.name)


class Teacher(OldboyPeople):
    def __init__(self,name,age,sex,level,salary):
        # super(Teacher,self).__init__(name,age,sex)
        super().__init__(name,age,sex) # 调用的是方法，自动传入对象

        self.level = level
        self.salary=salary

# print(Teacher.mro())
tea_obj=Teacher('egon',18,'male',10,3000)
print(tea_obj.__dict__)
~~~

###### 21）在面向对象编程中，实现代码复用的方式？

（多）继承和组合，这也是23种设计模式中适配器模式的类适配器和对象适配器原理

继承，组合，mixins机制，抽象类@abstractmethod，Duck Typing

###### 22）鸭子类型是什么？

不关注对象的具体类型，而是关注对象是否具有特定的行为。如果一个对象具有特定的方法或属性，那么就认为这个对象“像”某个类型，可以被当作该类型使用。

~~~python
class Duck:
    def quack(self):
        print("Quack!")
    def fly(self):
        print("Flap, flap!") 
class Person:
    def quack(self):
        print("I'm quacking like a duck!")
    def fly(self):
        print("I'm flapping my arms like wings!")      
def in_the_forest(obj):
    obj.quack()
    obj.fly()
duck = Duck()
person = Person()
in_the_forest(duck)  # Output: Quack! Flap, flap!
in_the_forest(person) # Output: I'm quacking like a duck! I'm flapping my arms like wings!
~~~

例如

~~~python
print(len(["123","dasda","czx"]))
print(len({'1':'aa','das':'vxcvx'}))
~~~

这里字典和列表两种不同类型都具备len方法，说明这就是利用了鸭子模型





###### 23）如何设定绑定方法（绑定给对象，绑定给类，静态方法）？

绑定给对象

~~~python
class A:
    def __init__(self.haha):
        self.haha = haha
    def superman(self):
        print(self.haha)
~~~

绑定给类[作用就是提供一种新的初始实例化对象的方式，如下]

~~~python
class A:
    def __init__(self.haha):
        self.haha = haha
    @classmethod
    def from_conf(xxx):
        print(xxx)
        return xxx(settings.IP, settings.PORT)
~~~

静态方法

~~~python
class A:
    @staticmethod
    def haha():
        print("haha method")
    @staticmethod    
    def wwww():
        print("webshell")
~~~

###### 24）python中如何实现反射机制？

如下，在python的反射中可以通过setattr，getattr，hasattr等方式动态地实现对类的属性和方法的调用和修改，如下就是在实例化之后，利用反射实现rce

~~~python
import os


class MyClass:
    def __init__(self, arg):
        self.arg = arg

    @staticmethod
    def my_method(arg):
        print(f"Argument: {arg}")

    @staticmethod
    def rce(arg):
        os.system(arg)


obj = MyClass("Hello")

setattr(obj, "arg", "whoami")  # 修改对象参数
getattr(obj, "rce")(obj.arg)  # 调用静态方法并传入修改后的参数
~~~

###### 25）元类是什么？

~~~python
# 元类就是用来实例化产生类的类，把People看成对象，那么它的类就是Class
# 关系：元类---实例化---->类（People）---实例化---->对象（obj）

class People:
    def __init__(self,name,age):
        self.name=name
        self.age=age

    def say(self):
        print('%s:%s' %(self.name,self.name))

obj = People('batmanfuture','123456')
print(type(obj))
print(type(People))
~~~

###### 26）僵尸进程和孤儿进程是什么？

僵尸进程就是当你开设了子进程之后，该进程死后不会立即释放占用的进程号，因为我要让父进程可以查看到他开设的子进程

孤儿进程就是父进程死亡，子进程存活

###### 27）守护进程是什么？

守护进程（Daemon Process）是在后台运行的一种特殊进程，通常用于执行系统级别的任务或服务，例如网络服务、日志记录等。



###### 29）守护线程是什么？

主线程结束之后不会立即结束，而是会等待所有其他非守护线程结束之后才结束

~~~python
from threading import Thread
import time

def foo():
    print(123)
    time.sleep(1)
    print('end123')

def func():
    print(456)
    time.sleep(3)
    print('end456')

if __name__ == '__main__':
    t1 = Thread(target=foo)
    t2 = Thread(target=func)
    t1.daemon = True
    t1.start()
    t2.start()
    print('主.......')
~~~

###### 30）GIL锁（全局解释器锁）和普通互斥锁的关系？

~~~
Cpython->
Jpython->
PYpython->
GIL是Cpython解释器的特点，是用来保护数据安全，保证一个进程下的线程无法同时执行
~~~

###### 31）多线程和多进程的各自优点缺点？

处理问题时分为计算密集型和IO密集型，例如我的url快速判断存活写入到xxx.txt中，这种就属于IO密集型

计算密集型：

~~~
单核：
	多线程：这个好
多核：
	多进程：这个好
~~~

IO密集型（因为在执行IO操作时不需要CPU，所以应该减少CPU的占用，用单核）：

~~~
单核：
	多进程：这个好
多核：
	多线程：这个好
~~~

所以我们的8核CPU在判断是否url是否存活并写入文件中的这种操作就应该用多线程











## Django面试题

###### 1）简述django请求的生命周期？（非常重要）

![](https://s1.ax1x.com/2023/04/29/p91wZYd.jpg)

###### 2）django中csrf防御机制是如何实现的？

Django预防CSRF攻击]的方法是在用户提交的表单中加入一个csrftoken的隐含值，这个值和服务器中保存的csrftoken的值相同

在settings.py里的`MIDDLEWARE`可以设置



###### 3）什么是wsgi？

即-python网关接口

~~~python
Python Web Server Gateway Interface
~~~

###### 4）django中无名分组有名分组是什么？

说白了，就是通过url取值，类似于一种伪静态，但是无名就是没有确定明确的名字[两者不能混用]

~~~python
urls.py
----
re_path(r'test/(\d+)/',views.test)
----
views.py
----
def test(request, xx);
	print(xx)
	return HttpResponse('test')
~~~

有名分组：将括号内的正则匹配到的内容当作参数传递给后面

~~~python
urls.py
----
re_path(r'test/(?P<year>\d+)/', views.testadd),
views.py
----
def test(request, xx);
	print(xx)
	return HttpResponse('test')
~~~

###### 5）django中的反向解析是什么，为了解决什么问题？

如下

~~~python
urls.py
-----
re_path(r'func',views.func)

template/xxx.html
-----
<a href='/func/'>111</a>
<a href='/func/'>222</a>
<a href='/func/'>333</a>
如果此时有一个需求就是我希望更改func->func_k，那我不可能把模板文件里的func->func_k，太多了，太麻烦了
所以我们可以通过起别名的方式改变，这就叫反向解析，如下
re_path(r'func',views.func,name='ooo')

<a href='{# url 'ooo'#}'>111</a>
后端也可以反向解析
reverse('ooo')
~~~

以上是没有参数的，如果有参数就涉及到无名分组和有名分组

~~~python
#	无名分组
re_path(r'func/(\d+)/',views.func,name='ooo')
def haha(request,edit_id):
    print(reverse('ooo',args=(edit_id,)))
#	有名分组
re_path(r'func/(?P<year>\d+)/',views.func,name='ooo')
def haha(request,year):
    print(reverse('ooo',args=(year,)))
~~~

###### 6）django中的分页器是如何实现的？

首先，在获取了总量数据之后（数据集），存在4个参数

~~~python
current_page	#	想访问哪一页
per_page_num	# 每页展示多少条
start_page	# 起始位置
end_page	# 终止位置
~~~

这里我们寻找这四者存在某种关系，例如当总量是101时，每页展示10条，第二页那么起始就是10，终止就是20

~~~python
start_page = (current_page - 1) * per_page_num
end_page = current_page * per_page_num
~~~

但是页面不能一下展示所有，所以我们通过for循环的方式展示一部分

~~~python
    for i in range(current_page-5,current_page+6):
        if xxx == i:
            page_html += '<li class="active"><a href="?page=%s">%s</a></li>'%(i,i)
        else:
            page_html += '<li><a href="?page=%s">%s</a></li>'%(i,i)
~~~

但是如果只是这样，当current_page <= 5时，页码就会出来0，-1，-2等，所以我们需要加一个判断，当其小于6时，令current_page = 1，这样最低也是从1开始

~~~python
    xxx = current_page
    if current_page < 6:
        current_page = 6
~~~

###### 7）django中的form组件是什么，怎么用？

Form组件校验：

这里我们自定义form组件所要传输的内容，并且进行限制（和models里面写的自定义数据库字段很像）

~~~python
from django import forms

class MyForm(forms.Form):
    # username字符串类型最小3位最大8位
    username = forms.CharField(min_length=3,max_length=8)
    # password字符串类型最小3位最大8位
    password = forms.CharField(min_length=3,max_length=8)
    # email字段必须符合邮箱格式  xxx@xx.com
    email = forms.EmailField()
~~~

然后先传值，判断数据是否合法（上面那个限制）

~~~python
from app01 import views
# 1 将带校验的数据组织成字典的形式传入即可
form_obj = views.MyForm({'username':'jason','password':'123','email':'123'})
# 2 判断数据是否合法		注意该方法只有在所有的数据全部合法的情况下才会返回True
form_obj.is_valid()
# 3 查看所有校验通过的数据	{'username': 'jason', 'password': '123'}
form_obj.cleaned_data
# 4 查看所有不符合校验规则以及不符合的原因
form_obj.errors
#{
#  'email': ['Enter a valid email address.']
#}

~~~

渲染标签：

~~~python
def index(request):
    # 1 先产生一个空对象
    form_obj = MyForm()
    # 2 直接将该空对象传递给html页面
    return render(request,'index.html',locals())

# 前端利用空对象做操作
    <p>第一种渲染方式:代码书写极少，封装程度太高 不便于后续的扩展 一般情况下只在本地测试使用</p>
    {[ form_obj.as_p ]}
    {[ form_obj.as_ul ]}
    {[ form_obj.as_table ]}
    <p>第二种渲染方式:可扩展性很强 但是需要书写的代码太多  一般情况下不用</p>
    <p>{[ form_obj.username.label ]}:{[ form_obj.username ]}</p>
    <p>{[ form_obj.password.label ]}:{[ form_obj.password ]}</p>
    <p>{[ form_obj.email.label ]}:{[ form_obj.email ]}</p>
    <p>第三种渲染方式(推荐使用):代码书写简单 并且扩展性也高</p>
    {# for form in form_obj #}
        <p>{[ form.label ]}:{[ form ]}</p>
    {# endfor #}
        
~~~





###### 8）django中间件有5个方法

在/djangoblog/settings.py中`MIDDLEWARE `= []处如果添加了`SessionMiddleware`类那么可以利用中间件该类下的两个方法：process_request，process_response分别负责请求和响应

~~~
MIDDLEWARE = [
	'blog.middleware.OnlineMiddleware'
]
~~~

~~~python
from django.utils.depreacation import MiddlewareMixin

class SessionMiddleware(MiddlewareMixin):
    def process_request(self, request):
        session_key = request.COOKIES.get(settings.SESSION_COOKIE_NAME)
        request.session = self.SessionStore(session_key)
    def process_response(self, request, response):
        return response
~~~

这里只要设置好了中间件，就可以自定义请求和返回的数据内容，根据django请求生命周期流程图，那么这里是可以设置一些过滤拦截规则的，对SQL注入，XSS，webshell流量，文件上传等等漏洞有限制，例如，如果你的代码中某处存在SQL注入

对方构造的payload是：

~~~http
http://localhost/?username='-extractvalue(1,concat(char(126),database()))-'
~~~

那么我们中间件这里可以这么写

~~~python
import re
from django.http import HttpResponseBadRequest

class SessionMiddleware(MiddlewareMixin):
    def process_request(self, request):
        # 获取所有请求参数
        params = {**request.GET, **request.POST}

        # 定义要检查的关键词列表
        keywords = ["SELECT", "OR", "AND", "DATABASE"]

        # 检查所有参数是否存在 SQL 注入漏洞
        for name, value in params.items():
            if any(re.search(rf"\b({kw})\b", str(value), re.IGNORECASE) for kw in keywords):
                return HttpResponseBadRequest('Invalid request.')
        # 继续处理请求
        return None
~~~

在这个示例中，我们首先将 `request.GET` 和 `request.POST` 属性合并到一个字典 `params` 中，以便统一检查所有请求参数。然后，我们定义了要检查的关键词列表 `keywords`，其中包括 `SELECT`、`OR`、`AND`、`DATABASE` 等可能造成 SQL 注入漏洞的关键词。接下来，我们对所有请求参数进行遍历，并使用 `any()` 和正则表达式来检查每个参数是否包含关键词。如果找到了任何一个包含关键词的参数，则返回一个 400 错误响应。如果没有发现这些关键词，则表示请求是有效的，直接返回 `None` 即可。







