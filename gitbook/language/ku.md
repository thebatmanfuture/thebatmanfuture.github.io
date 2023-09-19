#### time

~~~python
import time

time.sleep(3)
~~~

#### random

~~~
import random

print(random.random())	# 0 和 1之间的小数
print(random.randint(1, 3))
~~~

#### os

~~~
os.getcwd() 获取当前工作目录，即当前python脚本工作的目录路径
os.system("bash command")

import os
shell = input("please input the code:")
os.system(shell)
~~~

#### sys

~~~python
sys.path
# 查看参数
sys.argv
比如我们写命令行脚本 python sqlmap.py -u "xxx"
sys.argv[0] = sqlmap.py
sys.argv[1] = -u
sys.argv[2] = "xxx"

------
import sys

src_file = sys.argv[1]
dst_file = sys.argv[2]
print(src_file)
~~~

![](https://s3.bmp.ovh/imgs/2022/09/11/425fd68ae662e9f5.png)

#### 打印进度条

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

#### hash

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

#### subprocess

~~~python
import subprocess

res = subprocess.Popen('dir',shell=True,stdout=subprocess.PIPE,stderr=subprocess.PIPE)
#	这里dir是你执行的shell命令，stdout=subprocess.PIPE是当执行成功后把他放在管道里，PIPE,stderr=subprocess.PIPE是执行失败时放在管道里
das = res.stdout.read()	#	让das读出执行成功管道里的值
das_1 = res.stderr.read()  #	让das_1读出执行失败管道里的值
print(das.decode('gbk'))	#	这里gbk代表是windows下的默认编码格式，如果是linux下那么是utf-8
~~~

#### base64

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

对











#### re

##### [豆瓣top250](https://movie.douban.com/top250?start=0&filter=)

```python
import requests
import re
import csv
for w in range(0,250,25):
    url = f"https://movie.douban.com/top250?start={w}&filter="

    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:88.0) Gecko/20100101 Firefox/88.0',
    }

    a = requests.get(url,headers=headers)

    x = a.text
    # print(a.text)

    obj = re.compile(r'<li>.*?<div class="item">.*?<span class='
                     r'"title">(?P<name>.*?)</span>.*?<p class="">.*?<br>(?P<year>.*?)&nbsp.*?property="v:average">'
                     r'(?P<pingfen>.*?)</span>.*?<span>(?P<people>.*?)人评价</span>', re.S)

    result = obj.finditer(x)

    with open("data.csv",mode="a") as f:
        csvwrite = csv.writer(f)

        for i in result:
        # print(i.group('name'))
        # print(i.group('pingfen'))
        # print(i.group('year').strip())
        # print(i.group('people'))
            dic = i.groupdict()
            dic['year'] = dic['year'].strip()
            csvwrite.writerow(dic.values())
print("over!")
```

##### [电影天堂](https://www.dy2018.com/)

```python
import requests
import re
import csv
url = "https://www.dy2018.com"
headers = {
    "User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/88.0.4324.182 Safari/537.36"
}
list = []
a = requests.get(url,headers=headers)
a.encoding = 'gb2312'
# print(a.text)

obj1 = re.compile(r"2021必看热片.*?<ul>(?P<ul>.*?)</ul>", re.S)
obj2 = re.compile(r"<a href='(?P<link>.*?)'", re.S)
obj3 = re.compile(r'<br />◎片　　名(?P<name>.*?)<br />◎年　　代.*?<td style="WORD-WRAP: break-word" bgcolor="#fdfddf"><a href="(?P<download>.*?)</a></td>', re.S)

result = obj1.finditer(a.text)

for it in result:
    ul = it.group('ul')
    # print(ul)

result2 = obj2.finditer(ul)

for its in result2:
    payload = url + its.group('link')
    list.append(payload)
    # print(payload)

# print(list)
with open("data1.csv",mode="a",encoding='utf-8') as f:
    csvwrite = csv.writer(f)

    for href in list:
        w = requests.get(href,headers=headers)
        w.encoding = 'gb2312'
        # print(w.text)
        result3 = obj3.search(w.text)
        # print(result3.group("name"))
        # print(result3.group("download"))
        dic = result3.groupdict()
        csvwrite.writerow(dic.values())
print("over!")
```

#### bs4

##### 菜价			#该源代码已改变

```python
# 1. 拿到页面源代码
# 2. 使用bs4进行解析. 拿到数据
import requests
from bs4 import BeautifulSoup

url = "http://www.xinfadi.com.cn/marketanalysis/0/list/1.shtml"
resp = requests.get(url)

# 解析数据
# 1. 把页面源代码交给BeautifulSoup进行处理, 生成bs对象
page = BeautifulSoup(resp.text, "html.parser")  # 指定html解析器
# 2. 从bs对象中查找数据
# find(标签, 属性=值)
# find_all(标签, 属性=值)
# table = page.find("table", class_="hq_table")  # class是python的关键字
table = page.find("table", attrs={"class": "hq_table"})  # 和上一行是一个意思. 此时可以避免class
# 拿到所有数据行
trs = table.find_all("tr")[1:]
for tr in trs:  # 每一行
    tds = tr.find_all("td")  # 拿到每行中的所有td
    name = tds[0].text  # .text 表示拿到被标签标记的内容
    low = tds[1].text  # .text 表示拿到被标签标记的内容
    avg = tds[2].text  # .text 表示拿到被标签标记的内容
    high = tds[3].text  # .text 表示拿到被标签标记的内容
    gui = tds[4].text  # .text 表示拿到被标签标记的内容
    kind = tds[5].text  # .text 表示拿到被标签标记的内容
    date = tds[6].text  # .text 表示拿到被标签标记的内容
print("over1!!!!")
```

##### [抓取图片](https://www.umei.cc/bizhitupian/weimeibizhi/)

```python
# 1.拿到主页面的源代码. 然后提取到子页面的链接地址, href
# 2.通过href拿到子页面的内容. 从子页面中找到图片的下载地址 img -> src
# 3.下载图片
import requests
from bs4 import BeautifulSoup
import time
url = "https://www.umei.cc/bizhitupian/weimeibizhi/"
resp = requests.get(url)
resp.encoding = 'utf-8'  # 处理乱码
# print(resp.text)
# 把源代码交给bs
main_page = BeautifulSoup(resp.text, "html.parser")
alist = main_page.find("div", class_="TypeList").find_all("a")
# print(alist)
for a in alist:
    href = a.get('href')  # 直接通过get就可以拿到属性的值
    # 拿到子页面的源代码
    child_page_resp = requests.get(href)
    child_page_resp.encoding = 'utf-8'
    child_page_text = child_page_resp.text
    # 从子页面中拿到图片的下载路径
    child_page = BeautifulSoup(child_page_text, "html.parser")
    p = child_page.find("p", align="center")
    img = p.find("img")
    src = img.get("src")
    # 下载图片
    img_resp = requests.get(src)
    # img_resp.content  # 这里拿到的是字节
    img_name = src.split("/")[-1]  # 拿到url中的最后一个/以后的内容
    with open("img/"+img_name, mode="wb") as f:
        f.write(img_resp.content)  # 图片内容写入文件
    print("over!!!", img_name)
    time.sleep(1)
print("all over!!!")
```

#### xpath







#### argparse

[![X2e7hd.jpg](https://s1.ax1x.com/2022/06/12/X2e7hd.jpg)](https://imgtu.com/i/X2e7hd)

（1）

~~~python
parser = argparse.ArgumentParser()
~~~

（2）

~~~python
#	每一行的add_argument就是增加一个参数，第一行这里第一个参数名称是--exploit;
#	nargs表示当前命令行参数的数目，当为 + 时则至少有一个参数，当是 ? 时就是有一个或者0个，如果是 * 时则0个或者所有;
#	type指这里传进来的是str字符串类型，或者还有float，int类型；
#	default是如果我们的参数后面没有跟上具体值的话，那我们就使用这个默认的值;help就是写帮助信息的;
#	action是当参数在命令行中出现时使用的基本类型，值有两个 store_true,store_false
parser.add_argument("--exploit", nargs = '+', type=str, default='mysql', help='superman is over here!')
~~~

（3）

~~~python
#	opt会通过将参数字符串转换为对象
opt = praser.parse_args()
print(opt)

#	可以通过opt.exploit获取对应的值

~~~

#### platform

~~~
platform.system() 　　获取操作系统类型，windows、linux等
platform.platform() 获取操作系统，Darwin-9.8.0-i386-32bit
platform.version() 获取系统版本信息 6.2.0
~~~



#### pyinstaller

~~~
pyinstaller -F -w xxx.py
~~~







