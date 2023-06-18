### 格式化字符串

Python中共有三种格式化字符串输出的方式：

#### 输出

1.  %

~~~python
#	%的第一种形式
name = "batmanfuture"
age = 123
user = "my name is %s, my age is %d" %(name,age)
print(user)
--------------------------------------------------
my name is batmanfuture, my age is 123

#	%的第二种形式:以这种键值对的方式，注意，是 { 括号
user = "my name is %(name)s, my age is %(age)d" %{'name' : "batmanfuture",'age' : 123}
print(user)
--------------------------------------------------
my name is batmanfuture, my age is 123
~~~

2. format

~~~python
#	format的第一种形式
user = "my name is {}, age is {}" .format('batmanfuture',20)
print(user)
--------------------------------------------------
my name is batmanfuture, age is 20

#	format的第二种形式：根据索引取值
user = "my name is {1}, age is {0}" .format('batmanfuture',20)
print(user)
--------------------------------------------------
my name is 20, age is batmanfuture

#	format的第三种形式：根据姓名取值
user = "my name is {name}, age is {age}" .format(name = 'batmanfuture',age = 20)
print(user)
--------------------------------------------------
my name is batmanfuture, age is 20
~~~



3. f

~~~python
name = "batmanfuture"
age = 123
user = f"my name is {name}, age is {age}"
print(user)
--------------------------------------------------
my name is batmanfuture, age is 123
~~~

当格式化字符串由用户输入时，则可能会造成一些问题

4. Template

~~~python
from string import Template

name = 'batmanfuture'
age = 123
t = Template('my name is $name, age is $age')
res=t.substitute(name=name,age=age)
print(res)  
--------------------------------------------------
my name is batmanfuture, age is 123
~~~



















































