#### pyc和py

##### .java和.class

~~~java
在java中，.java文件，就是当前编写的代码文件；.class文件，就是编译过后的文件（jvm只识别.class文件）
我们在点run的时候，都是将.java文件先编译成.class文件 然后才能运行的   
~~~

~~~java
//生成命令
javac 	main.java	//	生成.class文件
java	main
~~~

[![L8RB7j.png](https://s1.ax1x.com/2022/04/15/L8RB7j.png)](https://imgtu.com/i/L8RB7j)

~~~java
java是跨平台的，为什么能夸平台，因为有java的jvm。因为jvm只能识别.class文件，所有编写的.java文件运行前都要先进行编译，成为.class文件
~~~

##### .pyc和.py

~~~python
pyc文件是py文件编译后生成的字节码文件(byte code)，pyc文件经过python解释器最终会生成机器码运行。因此pyc文件是可以跨平台部署的，类似Java的.class文件，一般py文件改变后，都会重新生成pyc文件。
~~~

~~~python
#	生成命令
test1.py
--------------
def a():
    return 1

def b():
    return 2

x = a()
y = b()
print(x + y)

test2.py
--------------
import test1

运行python -m test1.py生成test1.pyc文件
~~~

[![L8Whxf.png](https://s1.ax1x.com/2022/04/15/L8Whxf.png)](https://imgtu.com/i/L8Whxf)

##### PYC文件格式分析

https://kdr2.com/tech/python/pyc-format.html

##### CTF案例：PYTHON 字节码

题目附件 http://pan.baidu.com/s/1jGpB8DS 下载后是一个名为 crackme.pyc文件

python逆向基础资源：

> （1）用Python反编译Python软件：http://bbs.pediy.com/archive/index.php?t-111428.html
>
> （2）工具uncompyle2：https://hub.fastgit.xyz/wibiti/uncompyle2
>
> （3）在线工具：https://tool.lu/pyc/

###### 分析

~~~python
我们这里发现KEY2的值的结果就是我们rst每次新插入元素的ascii码值
在ascii表中，从65-125是字母和{}等符号的组合，flag就是由这些组合而成的
所以我们可以通过for j in key2，提取出key2答案的每一个值，然后再通过for i in range(65,126)，去提取字符经过j == (i + seed ^ ord(key[seed])) % 255运算判断是否相等，如果相等，那么该字符就是答案的第一位字符，然后我们连续输出，得到了flag
~~~

###### python脚本

~~~python
def decrypt(key, seed, key2):      #   65-125
    for j in key2:
        for i in range(65,126):
            if j == (i + seed ^ ord(key[seed])) % 255:
                seed = (seed + 1) % 55
                print(chr(i),end="")


KEY1 = 'Maybe you are good at decryptint Byte Code, have a try!'
KEY2 = [
        124,
        48,     #   23个字符
        52,
        59,
        164,
        50,
        37,
        62,
        67,
        52,
        48,
        6,
        1,
        122,
        3,
        22,
        72,
        1,
        1,
        14,
        46,
        27,
        232
]

en_out = decrypt(KEY1, 5, KEY2)

#	WCTF{ILOVEPYTHONSOMUCH}
~~~

















