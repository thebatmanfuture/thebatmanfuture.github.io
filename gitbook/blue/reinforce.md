







## 安全基线

### mysql安全基线

#### 系统配置要求

**远程访问**：通过远程登录到mysql所在的linux服务器，再在linux上更新安装mysql

**账号管理规范**：数据库用户限止为以下几个：root、repl、monitor、person。自建账号必须修改默认密码；登录过程必须加密，尤其是密码，默认采用sha1和md5

**服务器配置规范**：禁止把数据库服务器放在公网，禁止把内网中数据库服务器做端口映射到公网，内网中限定开放端口，服务器不应该具备访问外网连接（防止被反弹shell，让他不出网无法持久控制）

#### 要求和操作

###### 版本要求：最好是5.1以上

###### 账号和密码

账号：只给几个确定账号，密码长度要符合要求

移除Anonymous帐号；select user from mysql.user where user = '';mysql缺省有一个空用户

限制Mysql的系统帐户，不允许mysql的帐户在系统的root组内；防止借助mysql提权

禁止使用MYSQL_PWD，MYSQL_PWD是明文存储用户帐号信息

对非root用户禁止给予全部权限

~~~sql
select user, host from mysql.user where (Select_priv = 'Y') or (Insert_priv = 'Y') or (Update_priv = 'Y') or (Delete_priv = 'Y') or (Create_priv = 'Y') or (Drop_priv = 'Y');” and “select user, host from mysql.db where db = 'mysql' and ( (Select_priv = 'Y') or Insert_priv = 'Y') or (Update_priv = 'Y') or (Delete_priv = 'Y') or (Create_priv = 'Y') or (Drop_priv = 'Y'));
~~~



FILE的安全设置：select user, host from mysql.user where File_priv = 'Y';确认只有root、应用用户的用户具有写权限，这样对方无法写入一句话

PROCESS的安全设置：确定用户是否可以通过SHOW PROCESSLIST命令查看其他用户的进程
“select user, host from mysql.user where Process_priv = 'Y';”
确认只有管理员有权限

SUPER 权限控制：确定用户是否可以执行某些强大的管理功能，例如通过KILL命令删除用户进程，使用SET
“select user, host from mysql.user where Super_priv = 'Y';”
确认只有管理员有权限

SHUTDOWN权限控制：确定用户是否可以关闭MySQL服务器。在将此权限提供给root账户之外的任何用户时，都应当非常谨慎
“select user, host from mysql.user where Shutdown_priv = 'Y';”
确认只有管理员有权限

CREATE USER权限控制：确定用户是否可以执行CREATE USER命令，这个命令用于创建新的MySQL账户
“select user, host from mysql.user where Create_user_priv = 'Y';”
确认只有管理员有权限

Global GRANT权限控制：修改全局MySQL变量，执行关于复制和日志的各种命令
“select user, host from mysql.user where Reload_user_priv = 'Y';”
确认只有管理员有权限

RELOAD 权限控制：确定用户是否可以执行刷新和重新加载MySQL所用各种内部缓存的特定命令，包括日志、权限、主机、查询和表
“select user, host from mysql.user where Create_user_priv = 'Y';”
确认只有管理员有权限

###### 模块配置

只开启使用最少的模块
只使用最少的模块，目前使用的为：
Innodb
Myisam

删除Web console功能
严禁配置Web console

###### 参数配置

a) 帐号配置要求
failed_login_attempts=3
password_reuse_max=20
password_reuse_time= 365
password_lock_time=1

b) 密码的HASH要求
select User, Password from mysql.user where length(password) < 41;
All password hashes should be 41 bytes or longer

c) 所以帐号都不存空口令
“select user, password from mysql.user where length(password) = 0 or password is null; ”

d) 移除test数据库
SHOW DATABASES like 'test';
DROP DATABASES like 'test';

e) 不使用缺省root作为管理员名字
select user from mysql.user where user = 'root';
如果有返回，修改

f) Disable Load data local的设置
“show variables like 'local_infile';”
确认为OFF

g) Old password hashing的设置
“show variables like 'old_passwords';”
确认为OFF

h) IP绑定
Root：通过mysql限制为只允许本地登陆和通过一台生产网中的指定管理机登陆
Monitor：限制帐号，只允许本地登陆。
应用用户和repl没有限定登陆范围

i) Grant tables
“show variables like 'skip_grant_tables';”
确认为OFF

j) Safe user create
SQL: “select @@global.sql_mode;” 必须包含 NO_AUTO_CREATE_USER
SQL: “select @@session.sql_mode;” 必须包含 NO_AUTO_CREATE_USER

k) Skip Symbolic Links
“show variables like 'have_symlink';”
确认为DISABLE

###### 文件权限控制

a) 确认数据库文件只用root用户有权限
使用show variables like 'datadir' 获取数据库目录，
确认权限，建议配置为600

b) 确认数据库的二进制文件只用root用户有权限
使用“show variables like 'basedir';
确认权限，建议配置为755

c) 重要的配置文件
/etc/mysql/my.cnf
设置权限为644，mysql的用户

d) LOG文件权限
/etc/my.cnf里面 log-bin的位置
确认权限为600

###### 备份和介质管理

需要备份的文件为：
Mysql的数据库文件

###### 日志及其输出

a) 启动LOG记录功能
配置Error 日志以及Slow 日志

###### 安全监控和持续改进

a) 对于安全配置参数，需要进行监控和根据新的风险对系统进行持续性的改进。
b) 对于安全事件，参考“安全事件应急响应规范”中所规范内容。
c) 通过信息安全中心的安全事件应急响应，对突发的安全事件进行及时响应，并在安全时间结束后，分析事件原因，提出解决方法。
d) 通过信息安全中心的安全漏洞修补流程，对不同安全等级漏洞进行修补。
e) 系统确认已经修补信息安全部门发布的漏洞，漏洞威胁级别、修补时间和漏洞修补流程，参考《信息系统安全漏洞修补流程规范》。

~~~python
import os
check_mysql = {}
try:
f_one_one = os.popen('grep mysql /etc/passwd | grep nologin').read()
if f_one_one == '':
check_mysql['1.1为Mysql服务器运行单独创建用户和组'] = 'qualified'
else:
check_mysql['1.1为Mysql服务器运行单独创建用户和组'] = 'unqualified'
except:
check_mysql['1.1为Mysql服务器运行单独创建用户和组'] = '在' + '/etc/passwd' + '未找到'

try:
f_one_two = os.popen('id mysql').read().split(' ')
for info in f_one_two:
if 'uid' in info:
if 499 <= int(info.split('=')[1]):
check_mysql['1.2为Mysql服务器运行单独创建用户和组'] = 'uid qualified'
else:
check_mysql['1.2为Mysql服务器运行单独创建用户和组'] = 'uid unqualified'
if 'gid' in info:
if 499 <= int(info.split('=')[1]):
check_mysql['1.2为Mysql服务器运行单独创建用户和组'] = 'gid qualified'
else:
check_mysql['1.2为Mysql服务器运行单独创建用户和组'] = 'gid unqualified'
except:
f_one_two = os.popen('id mysql').read().split(' ')
check_mysql['1.2为Mysql服务器运行单独创建用户和组'] = '在' + str(f_one_two) + '未找到'

try:
f = os.popen('grep mysql /etc/passwd | grep nologin').read()
if f == '':
check_mysql['2.给于Mysql用户为非交互shell'] = 'qualified'
else:
check_mysql['2.给于Mysql用户为非交互shell'] = 'unqualified'
except:
check_mysql['2.给于Mysql用户为非交互shell'] = '在' + '/etc/passwd' + '未找到'

try:
f = os.popen('passwd -S mysql').read()
if 'Password locked' in f:
check_mysql['3.为MySQL用户上锁'] = 'qualified'
else:
check_mysql['3.为MySQL用户上锁'] = 'unqualified'
except:
f = os.popen('passwd -S mysql').read()
check_mysql['3.为MySQL用户上锁'] = '在' + str(f) + '未找到'

try:
f = os.popen('find $MYSQL_INSTALL \! -user mysql -ls').read() + os.popen('find $MYSQL_INSTALL \! -group mysql -ls').read()
if check_mysql['4.设置mysql安装目录和文件属主'] != '':
check_mysql['4.设置mysql安装目录和文件属主'] = 'qualified'
else:
check_mysql['4.设置mysql安装目录和文件属主'] = 'unqualified'
except:
check_mysql['4.设置mysql安装目录和文件属主'] = '在' + '$MYSQL_INSTALL \!' + '未找到'

try:
f = os.popen('find $MYSQL_DB_DATA \! -user mysql -ls').read() + os.popen('find $MYSQL_INSTALL \! -group mysql -ls').read()
if f != '':
check_mysql['5.1设置数据库/数据库日志/数据库配置文件目录和文件属主'] = '数据库 qualified'
else:
check_mysql['5.1设置数据库/数据库日志/数据库配置文件目录和文件属主'] = '数据库 unqualified'
except:
check_mysql['5.1设置数据库/数据库日志/数据库配置文件目录和文件属主'] = '在' + '$MYSQL_DB_DATA \!' + '未找到'

try:
f = os.popen('find $MYSQL_DB_LOG \! -user mysql -ls').read() + os.popen('find $MYSQL_DB_LOG \! -group mysql -ls').read()
if f != '':
check_mysql['5.2设置数据库/数据库日志/数据库配置文件目录和文件属主'] = '数据库日志 qualified'
else:
check_mysql['5.2设置数据库/数据库日志/数据库配置文件目录和文件属主'] = '数据库日志 unqualified'
except:
check_mysql['5.2设置数据库/数据库日志/数据库配置文件目录和文件属主'] = '在' + '$MYSQL_DB_LOG \!' + '未找到'

try:
f = os.popen('find $MYSQL_DB_ETC \! -user mysql -ls').read() + os.popen('find $MYSQL_DB_ETC \! -group mysql -ls').read()
if f != '':
check_mysql['5.3设置数据库/数据库日志/数据库配置文件目录和文件属主'] = '数据库配置文件 qualified'
else:
check_mysql['5.3设置数据库/数据库日志/数据库配置文件目录和文件属主'] = '数据库配置文件 unqualified'
except:
check_mysql['5.3设置数据库/数据库日志/数据库配置文件目录和文件属主'] = '在' + '$MYSQL_DB_ETC \!' + '未找到'

try:
f_six_one = os.popen('find -L $MYSQL_INSTALL \! -type l \! -type s -perm /o=w -ls').read()
f_six_two = os.popen('find -L $MYSQL_INSTALL \! -type l \! -type s -perm /g=w -ls').read()
f_six_three = os.popen('find -L $/opt/mysql/bin \! -type l \! -type s -perm /o=r -ls').read()
f_six_four = os.popen('find -L $/opt/mysql/bin \! -type l \! -type s -perm /o=x -ls').read()
if f_six_one == '' and f_six_two == '' and f_six_three == '' and f_six_four == '':
check_mysql['6设置mysql安装目录和文件权限'] = 'qualified'
else:
check_mysql['6设置mysql安装目录和文件权限'] = 'unqualified'
except:
check_mysql['6设置mysql安装目录和文件权限'] = '在' + '$MYSQL_INSTALL \!和$/opt/mysql/bin \!' + '未找到'

try:
f_data_one = os.popen('find -L $MYSQL_DB_DATA \! -type l \! -type s -perm /o=r -ls').read()
f_data_two = os.popen('find -L $MYSQL_INSTALL \! -type l \! -type s -perm /g=r -ls').read()
f_data_three = os.popen('find -L $MYSQL_INSTALL \! -type l \! -type s -perm /o=w -ls').read()
f_data_four = os.popen('find -L $MYSQL_INSTALL \! -type l \! -type s -perm /g=w -ls').read()
f_data_five = os.popen('find -L $MYSQL_INSTALL \! -type l \! -type s -perm /o=x -ls').read()
f_data_six = os.popen('find -L $MYSQL_INSTALL \! -type l \! -type s -perm /g=x -ls').read()

if f_data_one == '' and f_data_two == '' and f_data_three == '' and f_data_four == '' and f_data_five == '' and f_data_six == '':
check_mysql['7.1设置数据库/数据库日志/数据库配置文件目录和文件权限'] = 'MYSQL_INSTALL qualified'
else:
check_mysql['7.1设置数据库/数据库日志/数据库配置文件目录和文件权限'] = 'MYSQL_INSTALL unqualified'
except:
check_mysql['7.1设置数据库/数据库日志/数据库配置文件目录和文件权限'] = '在' + '$MYSQL_DB_DATA \!和$MYSQL_INSTALL \!' + '未找到'

try:
f_etc_one = os.popen('find -L $MYSQL_DB_ETC \! -type l \! -type s -perm /o=r -ls').read()
f_etc_two = os.popen('find -L $MYSQL_DB_ETC \! -type l \! -type s -perm /g=r -ls').read()
f_etc_three = os.popen('find -L $MYSQL_DB_ETC \! -type l \! -type s -perm /o=w -ls').read()
f_etc_four = os.popen('find -L $MYSQL_DB_ETC \! -type l \! -type s -perm /g=w -ls').read()
f_etc_five = os.popen('find -L $MYSQL_DB_ETC \! -type l \! -type s -perm /o=x -ls').read()
f_etc_six = os.popen('find -L $MYSQL_DB_ETC \! -type l \! -type s -perm /g=x -ls').read()

if f_etc_one == '' and f_etc_two == '' and f_etc_three == '' and f_etc_four == '' and f_etc_five == '' and f_etc_six == '':
check_mysql['7.1设置数据库/数据库日志/数据库配置文件目录和文件权限'] = 'MYSQL_DB_ETC qualified'
else:
check_mysql['7.1设置数据库/数据库日志/数据库配置文件目录和文件权限'] = 'MYSQL_DB_ETC unqualified'
except:
check_mysql['7.1设置数据库/数据库日志/数据库配置文件目录和文件权限'] = '在' + '$MYSQL_DB_ETC \! ' + '未找到'

try:
f = os.popen('echo $ MYSQL_PWD').read()
if f == '':
check_mysql['8.对数据库管理员禁用MYSQL_PWD环境变量'] = 'qualified'
else:
check_mysql['8.对数据库管理员禁用MYSQL_PWD环境变量'] = 'unqualified'
except:
check_mysql['8.对数据库管理员禁用MYSQL_PWD环境变量'] = 'not found'

# try:
# os.system('mysql')
# f = os.popen('show variables like "log_error"')
# if f == '' or f == r'/dev/null':
# check_mysql['9打开错误日志'] = 'unqualified'
# else:
# check_mysql['9打开错误日志'] = 'qualified'
# except:
# check_mysql['9打开错误日志'] = 'unqualified'

# try:
# os.system('mysql')
# f = os.popen('show databases like "test"')
# if f == '':
# check_mysql['10安装完毕后立刻移除缺省安装的test数据库'] = 'qualified'
# else:
# check_mysql['10安装完毕后立刻移除缺省安装的test数据库'] = 'unqualified'
# except:
# check_mysql['10安装完毕后立刻移除缺省安装的test数据库'] = 'unqualified'

# try:
# os.system('mysql')
# f = os.popen('select user from mysql.user where user="root";')
# if f == '':
# check_mysql['11安装完毕后立刻改变MYSQL数据库管理员名称'] = 'qualified'
# else:
# check_mysql['11安装完毕后立刻改变MYSQL数据库管理员名称'] = 'unqualified'
# except:
# check_mysql['11安装完毕后立刻改变MYSQL数据库管理员名称'] = 'unqualified'

# try:
# os.system('mysql')
# os.system('use mysql')
# f = os.popen('select host,user from USER where passwor="" and user = "sseadmin";')
# if f == '':
# check_mysql['12安装完毕后立刻设定数据库管理员的口令'] = 'qualified'
# else:
# check_mysql['12安装完毕后立刻设定数据库管理员的口令'] = 'unqualified'
# except:
# check_mysql['12安装完毕后立刻设定数据库管理员的口令'] = 'unqualified'

# try:
# os.system('mysql')
# os.system('use mysql')
# f = os.popen('select user from USER where host = "%";')
# if f == '':
# check_mysql['13用户表中的主机字段不允许使用通配符'] = 'qualified'
# else:
# check_mysql['13用户表中的主机字段不允许使用通配符'] = 'unqualified'
# except:
# check_mysql['13用户表中的主机字段不允许使用通配符'] = 'unqualified'

# try:
# os.system('mysql')
# os.system('use mysql')
# f = os.popen('select user from USER where length(password) = 0 or password is null;')
# if f == '':
# check_mysql['14任何用户都不允许使用空口令'] = 'qualified'
# else:
# check_mysql['14任何用户都不允许使用空口令'] = 'unqualified'
# except:
# check_mysql['14任何用户都不允许使用空口令'] = 'unqualified'

# try:
# os.system('mysql')
# os.system('use mysql')
# f = os.popen('select user from USER where user="";')
# if f == '':
# check_mysql['15禁止使用匿名账户'] = 'qualified'
# else:
# check_mysql['15禁止使用匿名账户'] = 'unqualified'
# except:
# check_mysql['15禁止使用匿名账户'] = 'unqualified'

# try:
# os.system('mysql')
# os.system('use mysql')
# f = os.popen('select user, host from user where (select_priv = "Y") or (insert_priv = "Y") or (update_priv = "Y") or (delete_priv = "Y") or (create_priv = "Y") or (drop_priv = "Y");')
# check_mysql['16.1只允许数据库管理员用户访问DBmysql'] = f
# except:
# check_mysql['16.1只允许数据库管理员用户访问DBmysql'] = 'unqualified'
#
# try:
# os.system('mysql')
# os.system('use mysql')
# f = os.popen('select user, host from db = "mysql" and (select_priv = "Y") or (insert_priv = "Y") or (update_priv = "Y") or (delete_priv = "Y") or (create_priv = "Y") or (drop_priv = "Y");')
# if f == '':
# check_mysql['16.2只允许数据库管理员用户访问DBmysql'] = 'unqualified'
# else:
# check_mysql['16.2只允许数据库管理员用户访问DBmysql'] = 'unqualified'
# except:
# check_mysql['16.2只允许数据库管理员用户访问DBmysql'] = 'unqualified'

# try:
# os.system('mysql')
# os.system('use mysql')
# f_file = os.popen('select user, host from user where file_priv = "Y";')
# check_mysql['17.1非数据库管理员不能有若干权限 file_priv'] = f_file
# except:
# check_mysql['17.1非数据库管理员不能有若干权限 file_priv'] = 'unqualified'
#
# try:
# os.system('mysql')
# os.system('use mysql')
# f_super = os.popen('select user, host from user where super_priv = "Y";')
# check_mysql['17.2非数据库管理员不能有若干权限 super_priv'] = f_super
# except:
# check_mysql['17.2非数据库管理员不能有若干权限 super_priv'] = 'unqualified'

# try:
# os.system('mysql')
# os.system('use mysql')
# f_shutdown = os.popen('select user, host from user where shutdown_priv = "Y";')
# check_mysql['17.3非数据库管理员不能有若干权限 shutdown_priv'] = f_shutdown
# except:
# check_mysql['17.3非数据库管理员不能有若干权限 shutdown_priv'] = 'unqualified'
#
# try:
# os.system('mysql')
# os.system('use mysql')
# f_create = os.popen('select user, host from user where create_user_priv = "Y";')
# check_mysql['17.4非数据库管理员不能有若干权限 create_user_priv'] = f_create
# except:
# check_mysql['17.4非数据库管理员不能有若干权限 create_user_priv'] = 'unqualified'

# try:
# os.system('mysql')
# os.system('use mysql')
# f_reload = os.popen('select user, host from user where reload_priv = "Y";')
# check_mysql['17.5非数据库管理员不能有若干权限 reload_priv'] = f_reload
# except:
# check_mysql['17.5非数据库管理员不能有若干权限 reload_priv'] = 'unqualified'

# try:
# os.system('mysql')
# os.system('use mysql')
# f_grant = os.popen('select user, host from user where grant_priv = "Y";')
# check_mysql['17.6非数据库管理员不能有若干权限 grant_priv'] = f_grant
# except:
# check_mysql['17.6非数据库管理员不能有若干权限 grant_priv'] = 'unqualified'


try:
f = os.popen('ps -ef | grep -v grep | grep mysqld | grep allow-suspicious-udfs')
if f == '':
check_mysql['18用户自定义函数安全防范'] = 'qualified'
else:
check_mysql['18用户自定义函数安全防范'] = 'unqualified'
except:
check_mysql['18用户自定义函数安全防范'] = 'not found'

# try:
# os.system('mysql')
# f = os.popen('show variables like "local_infile";')
# if "OFF" in f:
# check_mysql['19禁止从client本地加载数据'] = 'qualified'
# else:
# check_mysql['19禁止从client本地加载数据'] = 'unqualified'
# except:
# check_mysql['19禁止从client本地加载数据'] = 'unqualified'


# try:
# os.system('mysql')
# f = os.popen('show variables like "skip_grant_tables";')
# if "OFF" in f or f == '':
# check_mysql['20确保服务器启动时启用了权限系统'] = 'qualified'
# else:
# check_mysql['20确保服务器启动时启用了权限系统'] = 'unqualified'
# except:
# check_mysql['20确保服务器启动时启用了权限系统'] = 'unqualified'

# try:
# os.system('mysql')
# f = os.popen('show variables like "have_symlink";')
# if "DISABLED" in f:
# check_mysql['21跳过数据文件的符号连接'] = 'qualified'
# else:
# check_mysql['21跳过数据文件的符号连接'] = 'unqualified'
# except:
# check_mysql['21跳过数据文件的符号连接'] = 'unqualified'

with open('check_docker.txt', 'w') as file:
for v, k in check_mysql.items():
file.write('{v}:{k}'.format(v=v, k=k))
file.write('\n')
~~~

### RHEL6-7基线检查

~~~python
import re
import os


def write_file(v, k):
with open('Red Hat.txt', 'a') as file:
file.write('{v}:{k}'.format(v=v, k=k))
file.write('\n')


# 执行命令，返回字符串
def runCMD(cmd):
if (len(cmd) == 0):
return
else:
ty = os.popen('env | grep LANG')
for t in ty:
a = t.replace('\n', '')
if a.split('.')[1] == 'UTF-8':
d = os.popen(cmd)
return d
else:
d = os.popen(cmd)
# dd = d.read().decode(a.split('.')[1]).encode('utf-8')
return d


# 读文件
def readFile(filepath):
result = {}
result['flag'] = 0
try:
with open(filepath, 'r') as f1:
list1 = f1.readlines()
for i in range(0, len(list1)):
list1[i] = list1[i].rstrip('\n')
result['flag'] = 1
result['content'] = list1
return result
except:
return result


def is_exist(file_name):
if not os.path.isfile(file_name):
return "conform"
else:
return "no conform"


#将字符串中的特殊符号加\
def change(content):
fuhaolist = r"\ * . ? + $ ^ [ ] ( ) { } | /".split()
for fuhao in fuhaolist:
fuhao1 = '{0}{1}'.format('\\', fuhao)
content = content.replace(fuhao, fuhao1)
return content

# 匹配到就符合
def check(content, status):
# print content
for i in range(len(content)):
if status in content[i]:
return "conform"
else:
return "no conform"


#匹配到就不符合
def check1(content, status):
for c in content:
status = status.replace(" ", "\\s*")
if re.search(status, c, re.IGNORECASE):
return "no conform"
return "conform"


# 匹配到或未找到status的最后一个词，就符合
def check2(content, status):
for c in content:
if re.search(status, c, re.IGNORECASE):
return "conform"
elif re.search(status.split(' ')[-1], c) == None:
return "conform"
return "no conform"


def check3(content, status):
for c in content:
if ':' in c:
k = c.split(":")[0]
k_list.append(k)
# for name in k_list:
if status in k_list:
return "no conform"
else:
return "conform"


def check4(content, status):
# print content
for i in range(len(content)):
if status in content[i] and content[i + 1] == 'else' and 'umask 022' in content[i + 2]:
return "conform"
else:
return "no conform"


# 严格匹配行
def checkStatus(filepath, statsus):
file = readFile(filepath)
if file['flag'] == 1:
filecontent = file['content']
# txt = "".join(filecontent)
# print filecontent
for i in range(len(filecontent)):
if statsus in filecontent[i]:
return "conform"
else:
return "no conform"
else:
return "no conform（File 404!!）"


# 严格匹配行
def checkStatus1(filepath, statsus):
file = readFile(filepath)
if file['flag'] == 1:
filecontent = file['content']
statsus = "^" + statsus
statsus = statsus.replace(" ", "\\s*")
for con in filecontent:
if re.search(statsus, con, re.IGNORECASE):
return "no conform"
return "conform"
else:
return "conform（File 404!!）"


# 获得redhat版本
def getversion():
version_info = runCMD("cat /etc/redhat-release")
# print(version_info)
# versions = '0'
# version_pattern = re.compile('\d')

# for v in version_info:
# version = re.findall(version_pattern, v)
# while versions != '6' and versions != '7':
# versions = raw_input("Enter your input: ")
# print "Received input is : ", versions
# return versions
# if len(version)==0:
# print 'no version detected'
# exit()
read = version_info.read()
if '6.' in read:
print 'version: RHEL6'
return '6'
elif '7.' in read:
print 'version:RHEL7'
return '7'
else:
print 'no RHEL6 or RHEL7!!!'
exit()


# 检查是否合规
# 分版本
def checkStandard(testcontent, version, file1, standard1, file2, standard2):
if version == '6':
qq = checkStatus(file1, standard1)
write_file(qq, '*********' + testcontent)
# print('{0}*********{1}'.format(qq, testcontent))
else:
qq = checkStatus(file2, standard2)
write_file(qq, '*********' + testcontent)
# print('{0}*********{1}'.format(qq, testcontent))


# 不分版本
def checkStandard_1(testcontent, file1, standard1):
qq = checkStatus(file1, standard1)
write_file(qq, '*********' + testcontent)
# print('{0}*********{1}'.format(qq, testcontent))


# 命令行检查
# 分版本
def checkStandard_2(testcontent, version, cmd, standard1, cmd2, standard2):
if version == '6':
result = runCMD(cmd)
r_list = result.read().split('\n')
# print r_list
if r_list == 0:
# write_file('file404', '*********' + testcontent)
# print('{0}*********{1}'.format('file404', testcontent))
write_file('file404', '*********' + testcontent)
else:
qq = check(r_list, standard1)
print('{0}*********{1}'.format(qq, testcontent))
write_file(qq, '*********' + testcontent)
else:
result = runCMD(cmd2)
r_list = result.read().split('\n')
# print r_list
if r_list == 0:
# write_file('file404', '*********' + testcontent)
# print('{0}*********{1}'.format('file404', testcontent))
write_file('file404', '*********' + testcontent)
else:
qq = check(r_list, standard2)
# print('{0}*********{1}'.format(qq, testcontent))
write_file(qq, '*********' + testcontent)


# 不分版本
# checkStandard_3('5.2.7 删除相关高风险文件-/root/.rhosts', 'ls -l /root/.rhosts', 'ls: ')
def checkStandard_3(testcontent, cmd, standard, printresult=0):
result = runCMD(cmd)
r_list = result.read().split('\n')
# print r_list
if r_list == 0:
write_file('file404', '*********' + testcontent)
# print('{0}*********{1}'.format('file404', testcontent))
else:
qq = check(r_list, standard)
# print('{0}*********{1}'.format(qq, testcontent))
write_file(qq, '*********' + testcontent)


# 6为文件，7为命令行
def checkStandard_4(testcontent, version, file1, standard1, cmd2, standard2):
if version == '6':
qq = checkStatus(file1, standard1)
write_file(qq, '*********' + testcontent)
# print('{0}*********{1}'.format(qq, testcontent))
else:
result = runCMD(cmd2)
r_list = result.read().split('\n')
# print r_list
if r_list == 0:
write_file('file404', '*********' + testcontent)
# print('{0}*********{1}'.format('file404', testcontent))
else:
qq = check(r_list, standard2)
# print('{0}*********{1}'.format(qq, testcontent))
write_file(qq, '*********' + testcontent)


# 6为命令行，7为文件
def checkStandard_5(testcontent, version, cmd, standard1, file2, standard2):
if version == '6':
qq = check(runCMD(cmd), standard1)
write_file(qq, '*********' + testcontent)
# print('{0}*********{1}'.format(qq, testcontent))
else:
qq = checkStatus(file2, standard2)
write_file(qq, '*********' + testcontent)
# print('{0}*********{1}'.format(qq, testcontent))


# 检查文件内容，匹配到或未找到status的最后一个词，就符合
def checkStandard_6(testcontent, content, standard):
qq = check2(content, standard)
write_file(qq, '*********' + testcontent)
# print('{0}*********{1}'.format(qq, testcontent))


# 检查文件内容，匹配到就不符合
def checkStandard_7(testcontent, content, standard):
qq = check1(content, standard)
write_file(qq, '*********' + testcontent)
# print('{0}*********{1}'.format(qq, testcontent))


def checkStandard_8(testcontent, version, file1, standard1, file2, standard2):
print(testcontent)
if version == '6':
for i in standard1.split('\n'):
res = checkStatus(file1, i)
write_file(i, '*********' + res)
# print('{0}*********{1}'.format(i, res))
else:
for i in standard2.split('\n'):
res = checkStatus(file2, i)
write_file(i, '*********' + res)
# print('{0}*********{1}'.format(i, res))


def checkStandard_9(testcontent, file1, standard1):
qq = checkStatus1(file1, standard1)
write_file(qq, '*********' + testcontent)
# print('{0}*********{1}'.format(qq, testcontent))


def checkStandard_10(testcontent, file_name):
result = is_exist(file_name)
write_file(result, '*********' + testcontent)
# print('{0}*********{1}'.format(qq, testcontent))


def checkStandard_11(testcontent, content, standard):
qq = check3(content, standard)
write_file(qq, '*********' + testcontent)
# print('{0}*********{1}'.format(qq, testcontent))


def checkStandard_12(testcontent):
name = os.popen('hostname')
if re.match('[A-Za-z0-9]{5,20}', name.read()):
write_file("conform", '*********' + testcontent)
else:
write_file("no conform", '*********' + testcontent)

# write_file(qq, '*********' + testcontent)
# print('{0}*********{1}'.format(qq, testcontent))


if __name__ == '__main__':
version = getversion()
serviceContent = runCMD('service --status-all').read().split('\n')
yumListInstalled = runCMD('yum list installed').read().split('\n')

checkStandard_3('4.1 Time service client - install NTP or not', 'rpm -q ntp', 'ntp')
print('4.2')
# checkStandard_1('4.3 时间服务地址配置','/etc/ntp.conf',
# 'server ((?:(?:25[0-5]|2[0-4]\d|(?:1\d{2}|[1-9]?\d))\.){3}(?:25[0-5]|2[0-4]\d|(?:1\d{2}|[1-9]?\d)))\nserver ((?:(?:25[0-5]|2[0-4]\d|(?:1\d{2}|[1-9]?\d))\.){3}(?:25[0-5]|2[0-4]\d|(?:1\d{2}|[1-9]?\d)))')
checkStandard_1('4.3 Time service address configuration', '/etc/ntp.conf', 'server \d+\.\d+\.\d+\.\d+server \d+\.\d+\.\d+\.\d+')
checkStandard('4.4 Configure nudge mode - configure nudge or not', version, '/etc/sysconfig/ntpd',
'OPTIONS=\"-x -u ntp:ntp -p /var/run/ntpd.pid\"', '/etc/sysconfig/ntpd', 'OPTIONS=\"-x\"')
checkStandard_1('4.5 Set the maximum allowable synchronization error threshold', '/etc/ntp.conf', 'tinker panic 500')
# checkStandard('4.6 设置开机启动', version, 'chkconfig --list ntpd',
# 'ntpd 0:off 1:off 2:on 3:on 4:on 5:on 6:off')
# print('4.7')

# checkStandard('5.1.1 ulimit设置', version, '/etc/security/limits.d/90-nproc.conf',
# '* soft nproc 2047', '/etc/security/limits.d/20-nproc.conf',
# '* soft nproc 2047')

# 5.1.2 SELinux配置
checkStandard_3('5.1.2 SELinux configuration', 'getenforce', 'disabled')
# 5.1.3 防火墙配置
checkStandard_6('5.1.3 Firewall configuration', serviceContent, 'NetworkManager \(pid \\d*\) is running...')

# 5.1.4 SSHD配置
print('/etc/ssh/sshd_config High permission required')
checkStandard_1('5.1.4 Sshd configuration-22 port', '/etc/ssh/sshd_config', 'Port 22')
checkStandard_1('5.1.4 SSHD configuration - log all information, including info information', '/etc/ssh/sshd_config', 'LogLevel INFO')
checkStandard_1('5.1.4 Sshd configuration - 5 retries maximum', '/etc/ssh/sshd_config', 'MaxAuthTries 5')
checkStandard_1('5.1.4 Sshd configuration - allow password authentication', '/etc/ssh/sshd_config', 'PasswordAuthentication yes')
checkStandard_1('5.1.4 Sshd configuration - turn off query answer mode', '/etc/ssh/sshd_config', 'ChallengeResponseAuthentication no')
checkStandard_1('5.1.4 Sshd configuration - prohibit the use of content in. Rhosts and. Shosts files', '/etc/ssh/sshd_config', 'IgnoreRhosts yes')
checkStandard_1('5.1.4 Sshd configuration - rhosts or / etc / hosts.equiv is successful, which is improperly based on public key authentication success (key)', '/etc/ssh/sshd_config',
'HostbasedAuthentication no')
checkStandard_1('5.1.4 Sshd configuration - rhosts or / etc / hosts.equiv is successful, not RSA authentication is successful, only ssh-1 is valid (password)', '/etc/ssh/sshd_config',
'PasswordAuthentication yes')
checkStandard_1('5.1.4 Sshd configuration - empty password is not allowed when password authentication is used', '/etc/ssh/sshd_config', 'PermitEmptyPasswords no')
checkStandard_1('5.1.4 Sshd configuration - Disable root remote login', '/etc/ssh/sshd_config', 'PermitRootLogin no')
checkStandard_1('5.1.4 Sshd configuration - check the owner and permission of user files and directories before user login', '/etc/ssh/sshd_config', 'StrictModes yes')
checkStandard_1(
'5.1.4 Sshd configuration - the ~ /. SSH / known hosts file should not be ignored when using two authentication methods: rhostsrsaauthentication or hostbasedachentication',
'/etc/ssh/sshd_config', 'IgnoreUserKnownHosts no')
checkStandard_1('5.1.4 Sshd configuration - Using SSH protocol version 2', '/etc/ssh/sshd_config', 'Protocol 2')
checkStandard_1('5.1.4 Sshd configuration - login without DNS resolution', '/etc/ssh/sshd_config', 'UseDNS no')
checkStandard_1('5.1.4 Sshd configuration - do not use GSSAPI authentication mode, which is related to DNS and may lead to slow implementation of connection speed', '/etc/ssh/sshd_config',
'GSSAPIAuthentication no')
checkStandard_1('5.1.4 Sshd configuration - shut down the SFTP service. If it is necessary to use the SFTP service, manually open the old system to maintain the status quo, and shut down the new system', '/etc/ssh/sshd_config',
'#Subsystem sftp /usr/libexec/openssh/sftp-server')
# checkStandard_1('5.1.4 SSHD配置-登陆时不使用DNS解析实施', '/etc/ssh/sshd_config', 'UseDNS no')

rizhi_01_6 = """-a exit,always -F arch=b64 -S execve -k exec
-a exit,always -F arch=b32 -S execve -k exec"""
rizhi_01_7 = """-a exit,always -F arch=b64 -S execve -k exec
-a exit,always -F arch=b32 -S execve -k exec"""

checkStandard_8('5.1.5 Log and audit service configuration', version, '/etc/audit/audit.rules', rizhi_01_6,
'/etc/audit/rules.d/audit.rules', rizhi_01_7)

rizhi_02_6 = """-e 1
-w /etc/crontab -p wa -k crontab
-w /etc/hosts -p wa -k hosts
-w /etc/hosts.allow -p wa -k hosts-allow
-w /etc/hosts.deny -p wa -k hosts-deny
-w /etc/fstab -p wa -k fstab
-w /etc/passwd -p wa -k passwd
-w /etc/shadow -p wa -k shadow
-w /etc/group -p wa -k group
-w /etc/gshadow -p wa -k gshadow
-w /etc/ntp.conf -p wa -k ntp
-w /etc/sysctl.conf -p wa -k sysctl
-w /etc/security/limits.conf -p wa -k limits
-w /boot/grub/grub.conf -p wa -k grub
-w /etc/ssh/sshd_config -p wa -k ssh
-w /etc/udev/rules.d/ -p wa -k udev
-w /etc/profile -p wa -k profile
-w /etc/kdump.conf -p wa -k kdump
-w /etc/lvm/lvm.conf -p wa -k lvm
-w /etc/login.defs -p wa -k login-defs
-w /etc/rsyslog.conf -p wa -k rsyslog
-w /etc/sysconfig/i18n -p wa -k i18n
-w /etc/sysconfig/network -p wa -k network
-w /etc/multipath.conf -p wa -k multipath
-w /etc/audit/auditd.conf -p wa -k audit
-w /etc/audit/audit.rules -p wa -k auditrule"""
rizhi_02_7 = """-e 1
-w /etc/crontab -p wa -k crontab
-w /etc/hosts -p wa -k hosts
-w /etc/hosts.allow -p wa -k hosts-allow
-w /etc/hosts.deny -p wa -k hosts-deny
-w /etc/fstab -p wa -k fstab
-w /etc/passwd -p wa -k passwd
-w /etc/shadow -p wa -k shadow
-w /etc/group -p wa -k group
-w /etc/gshadow -p wa -k gshadow
-w /etc/ntp.conf -p wa -k ntp
-w /etc/sysctl.conf -p wa -k sysctl
-w /etc/security/limits.conf -p wa -k limits
-w /boot/grub2/grub.cfg -p wa -k grub
-w /etc/ssh/sshd_config -p wa -k ssh
-w /etc/udev/rules.d/ -p wa -k udev
-w /etc/profile -p wa -k profile
-w /etc/kdump.conf -p wa -k kdump
-w /etc/lvm/lvm.conf -p wa -k lvm
-w /etc/login.defs -p wa -k login-defs
-w /etc/rsyslog.conf -p wa -k rsyslog
-w /etc/locale.conf -p wa -k locale
-w /etc/sysconfig/network -p wa -k network
-w /etc/multipath.conf -p wa -k multipath
-w /etc/audit/auditd.conf -p wa -k audit
-w /etc/audit/rules.d/audit.rules -p wa -k auditrule"""
checkStandard_8('5.1.5 Log and audit service configuration', version, '/etc/audit/audit.rules', rizhi_02_6,
'/etc/audit/rules.d/audit.rules', rizhi_02_7)
checkStandard_1('5.1.5 Log and audit service configuration - configure audit logs (num_logs = 30)', '/etc/audit/auditd.conf', 'num_logs = 30')
checkStandard_1('5.1.5 Log and audit service configuration - configure audit logs (max_log_file = 50)', '/etc/audit/auditd.conf', 'max_log_file = 50')
checkStandard_1('5.1.5 Log and audit service configuration - configure audit logs (flush = NONE)', '/etc/audit/auditd.conf', 'flush = NONE')
checkStandard_1('5.1.5 Log and audit service configuration - configure audit logs (active = yes)', '/etc/audisp/plugins.d/syslog.conf', 'active = yes')
checkStandard_1('5.1.5 Log and audit service configuration - configure audit logs (args = LOG_LOCAL0)', '/etc/audisp/plugins.d/syslog.conf', 'args = LOG_LOCAL0')
checkStandard_1('5.1.5 Log and audit service configuration - configure audit logs (/etc/rsyslog.conf)', '/etc/rsyslog.conf',
'\*.info;mail.none;authpriv.none;cron.none;local0.none /var/log/messages')
# checkStandard_1('5.1.5 日志和审计服务配置-配置audit日志', '/etc/rsyslog.conf', '*.* @179.3.10.191:514 ---IP根据实际环境修改(业务内网为180.7.95.191，办公内网为172.18.1.181)')

checkStandard_1('5.1.5 Log and audit service configuration - log local retention time', '/etc/logrotate.conf', 'rotate 24')
checkStandard_6('5.1.5 Log and audit service configuration - rsyslog log log service', serviceContent, 'rsyslog \(pid \\d*\) is running...')
checkStandard_2('4.6 Set startup', version, 'chkconfig --list rsyslog',
'rsyslog 0:off 1:off 2:on 3:on 4:on 5:on 6:off','service rsyslog status','running')

checkStandard_4('5.2.1 Physical security settings', version, '/etc/init/control-alt-delete.conf', '#start on control-alt-delete',
'systemctl list-unit-files | grep ctrl-alt-del.target', 'masked')
checkStandard('5.2.2 Single user encryption', version, '/etc/sysconfig/init', 'SINGLE=/sbin/sulogin',
'/usr/lib/systemd/system/rescue.service', 'ExecStart=-/bin/sh -c "/user/sbin/sulogin; ')
checkStandard_1('5.2.3 Password policy settings - password history is 5 times', '/etc/pam.d/system-auth-ac',
'password required pam_pwhistory.so use_authtok remember=5 enforce_for_root')
checkStandard_1("5.2.4 System login security settings - avoid recording the login information of non existing users, and avoid password disclosure caused by user's wrong input", '/etc/login.defs', 'LOG_UNKFAIL_ENAB yes')
checkStandard_1("5.2.4 System login security settings - avoid recording the login information of non existing users, and avoid password disclosure caused by user's wrong input", '/etc/login.defs', 'LASTLOG_ENAB yes')
checkStandard_1('5.2.5 System global bashrc security settings - record history and output it to system log messages in a certain format in real time', '/etc/login.defs',
'export PROMPT_COMMAND=\'{ msg=$(history 1 | { read x y; echo $y; });logger \"[euid=$(whoami)]\":$(who am i):[`pwd`]\"$msg\"; }\'')
checkStandard_3('5.2.6 System sensitive file permission settings-/etc/crontab', 'ls -l /etc/crontab', '-r--------', 1)
checkStandard_3('5.2.6 System sensitive file permission settings-/etc/securetty', 'ls -l /etc/securetty', '-r--------', 1)
if version == '6':
checkStandard_3('5.2.6 System sensitive file permission settings-/boot/grub/grub.conf', 'ls -l /boot/grub/grub.conf', '-rw-------', 1)
else:
checkStandard_3('5.2.6 System sensitive file permission settings-/boot/grub2/grub.cfg', 'ls -l /boot/grub2/grub.cfg', '-rw-------', 1)
checkStandard_3('5.2.6 System sensitive file permission settings-/etc/inittab', 'ls -l /etc/inittab', '-rw-------', 1)
checkStandard_3('5.2.6 System sensitive file permission settings-/etc/login.defs', 'ls -l /etc/login.defs', '-rw-r-----', 1)
checkStandard_3('5.2.6 System sensitive file permission settings-/etc/passwd', 'ls -l /etc/passwd', '-rw-r--r--', 1)
checkStandard_3('5.2.6 System sensitive file permission settings-/etc/shadow', 'ls -l /etc/shadow', '----------', 1)
checkStandard_3('5.2.6 System sensitive file permission settings-/etc/gshadow', 'ls -l /etc/gshadow', '----------', 1)
checkStandard_3('5.2.6 System sensitive file permission settings-/etc/group', 'ls -l /etc/group', '-rw-r--r--', 1)
checkStandard_3('5.2.6 System sensitive file permission settings-/etc/services', 'ls -l /etc/services', '-rw-r--r--', 1)
checkStandard_3('5.2.6 System sensitive file permission settings-/etc/xinetd.conf', 'ls -l /etc/xinetd.conf', '-rw-------', 1)
# checkStandard_3('5.2.6 系统敏感文件权限设置-/etc/security','ls -l /etc/security','drw-------',1)
checkStandard_3('5.2.6 System sensitive file permission settings-/etc/group', 'ls -l /etc/group', '-rw-r--r--', 1)

checkStandard_10('5.2.7 System sensitive file permission settings-/root/.rhosts', '/root/.rhosts')
checkStandard_10('5.2.7 System sensitive file permission settings-/root/.shosts', 'root/.shosts')
checkStandard_10('5.2.7 System sensitive file permission settings-/etc/hosts.equiv', '/etc/hosts.equiv')
checkStandard_10('5.2.7 System sensitive file permission settings-/etc/shosts.equiv', '/etc/shosts.equiv')

# checkStandard_3('5.2.7 删除相关高风险文件-/root/.rhosts','ls -l /root/.rhosts','ls: ')

checkStandard_6('5.2.8 Disable unnecessary system services-postfix', serviceContent, change('postfix is stopped'))
checkStandard_6('5.2.8 Disable unnecessary system services-nfslock(Server for NFS ignored)', serviceContent, change('nfslock is stopped'))
checkStandard_6('5.2.8 Disable unnecessary system services-rpcgssd(Server for NFS ignored)', serviceContent, change('rpcgssd is stopped'))
checkStandard_6('5.2.8 Disable unnecessary system services-rpcbind(Server for NFS ignored)', serviceContent, change('rpcbind is stopped'))
checkStandard_6('5.2.8 Disable unnecessary system services-rpcidmapd(Server for NFS ignored)', serviceContent, change('rpcidmapd is stopped'))
checkStandard_6('5.2.8 Disable unnecessary system services-rpcsvcgssd(Server for NFS ignored)', serviceContent, change('rpcsvcgssd is stopped'))
checkStandard_6('5.2.8 Disable unnecessary system services-avahi-daemon', serviceContent, change('avahi-daemon is stopped'))
checkStandard_6('5.2.8 Disable unnecessary system services-cups', serviceContent, change('cups is stopped'))
checkStandard_6('5.2.8 Disable unnecessary system services-pcscd', serviceContent, change('pcscd is stopped'))
checkStandard_6('5.2.8 Disable unnecessary system services-smartd', serviceContent, change('smartd is stopped'))
checkStandard_6('5.2.8 Disable unnecessary system services-alsasound', serviceContent, change('alsasound is stopped'))
checkStandard_6('5.2.8 Disable unnecessary system services-iscsitarget', serviceContent, change('iscsitarget is stopped'))
checkStandard_6('5.2.8 Disable unnecessary system services-smb', serviceContent, change('smb is stopped'))
checkStandard_6('5.2.8 Disable unnecessary system services-acpid', serviceContent, change('acpid is stopped'))
checkStandard_6('5.2.8 Disable unnecessary system services-iptables', serviceContent, change('iptables is stopped'))
checkStandard_6('5.2.8 Disable unnecessary system services-ip6tables', serviceContent, change('ip6tables is stopped'))
checkStandard_6('5.2.8 Disable unnecessary system services-firewalld', serviceContent, change('firewalld is stopped'))
checkStandard_6('5.2.8 Disable unnecessary system services-rhsmcertd', serviceContent, change('rhsmcertd is stopped'))
if check(serviceContent, 'rhsmcertd \(pid \\d*\) is running...') == '符合':
print('开启了xinetd服务')
checkStandard_6('5.2.8 Disable unnecessary system services-如果开启了xinetd服务，应禁止chargen-dgram', serviceContent,
change('chargen-dgram is stopped'))
checkStandard_6('5.2.8 Disable unnecessary system services-如果开启了xinetd服务，应禁止chargen-stream', serviceContent,
change('chargen-stream is stopped'))
checkStandard_6('5.2.8 Disable unnecessary system services-如果开启了xinetd服务，应禁止daytime-dgram', serviceContent,
change('daytime-dgram is stopped'))
checkStandard_6('5.2.8 Disable unnecessary system services-如果开启了xinetd服务，应禁止daytime-stream', serviceContent,
change('daytime-stream is stopped'))
checkStandard_6('5.2.8 Disable unnecessary system services-如果开启了xinetd服务，应禁止echo-dgram', serviceContent,
change('echo-dgram is stopped'))
checkStandard_6('5.2.8 Disable unnecessary system services-如果开启了xinetd服务，应禁止echo-stream', serviceContent,
change('echo-stream is stopped'))
checkStandard_6('5.2.8 Disable unnecessary system services-如果开启了xinetd服务，应禁止tcpmux-server', serviceContent,
change('tcpmux-server is stopped'))
else:
print('Xinetd service is not enabled')
print('If xinetd service is not needed, it is recommended to uninstall')
checkStandard_7('5.2.8 Uninstall unnecessary system services-xinetd', yumListInstalled, 'xinetd')

checkStandard_7('5.2.8 Uninstall unnecessary system services-telnet-server', yumListInstalled, 'telnet-server')
checkStandard_7('5.2.8 Uninstall unnecessary system services-telnet', yumListInstalled, 'telnet')
checkStandard_7('5.2.8 Uninstall unnecessary system services-rsh-server', yumListInstalled, 'rsh-server')
checkStandard_7('5.2.8 Uninstall unnecessary system services-rsh', yumListInstalled, 'rsh')
checkStandard_7('5.2.8 Uninstall unnecessary system services-ypbind', yumListInstalled, 'ypbind')
checkStandard_7('5.2.8 Uninstall unnecessary system services-ypserv', yumListInstalled, 'ypserv')
checkStandard_7('5.2.8 Uninstall unnecessary system services-tftp-server', yumListInstalled, 'tftp-server')
checkStandard_7('5.2.8 Uninstall unnecessary system services-tftp', yumListInstalled, 'tftp')
checkStandard_7('5.2.8 Uninstall unnecessary system services-talk-server', yumListInstalled, 'talk-server')
checkStandard_7('5.2.8 Uninstall unnecessary system services-talk', yumListInstalled, 'talk')
checkStandard_7('5.2.8 Uninstall unnecessary system services-httpd', yumListInstalled, 'httpd')
checkStandard_7('5.2.8 Uninstall unnecessary system services-dhcp', yumListInstalled, 'dhcp')
checkStandard_7('5.2.8 Uninstall unnecessary system services-openldap-servers', yumListInstalled, 'openldap-servers')
checkStandard_7('5.2.8 Uninstall unnecessary system services-openldap-clients', yumListInstalled, 'openldap-clients')
checkStandard_7('5.2.8 Uninstall unnecessary system services-bind', yumListInstalled, 'bind')
checkStandard_7('5.2.8 Uninstall unnecessary system services-vsftpd', yumListInstalled, 'vsftpd')
checkStandard_7('5.2.8 Uninstall unnecessary system services-httpd', yumListInstalled, 'httpd')
checkStandard_7('5.2.8 Uninstall unnecessary system services-dovecot', yumListInstalled, 'dovecot')
checkStandard_7('5.2.8 Uninstall unnecessary system services-samba', yumListInstalled, 'samba')
checkStandard_7('5.2.8 Uninstall unnecessary system services-net-snmp', yumListInstalled, 'net-snmp')
checkStandard_7('5.2.8 Uninstall unnecessary system services-setroubleshoot', yumListInstalled, 'setroubleshoot')
checkStandard_7('5.2.8 Uninstall unnecessary system services-mcstrans', yumListInstalled, 'mcstrans')

checkStandard_1('5.2.9 System global profile security settings', '/etc/profile', 'export TMOUT=900')

# checkStandard_2('5.2.10 UMASK settings', version, 'cat /etc/profile', 'umask 077',
# 'cat /etc/profile', 'umask 077')

result = runCMD('cat /etc/profile')
r_list = result.read().split('\n')
# print r_list
if r_list == 0:
# write_file('file404', '*********' + testcontent)
# print('{0}*********{1}'.format('file404', '5.2.10 UMASK settings'))
write_file('file404', '*********' + '5.2.10 UMASK settings')
else:
qq = check4(r_list, 'umask 077')
# print('{0}*********{1}'.format(qq, '5.2.10 UMASK settings'))
write_file(qq, '*********' + '5.2.10 UMASK settings')

checkStandard_12('6.1 Host name naming convention - host name')
checkStandard_3("7.1.1 According to the requirements of the company's management, the added management account-sseadmin", "cat /etc/passwd",
'sseadmin')
checkStandard_3("7.1.1 According to the requirements of the company's management, the added management account-patrol", "cat /etc/passwd",
'patrol')
checkStandard_3("7.1.1 According to the requirements of the company's management, the added management account-ansible", "cat /etc/passwd",
'ansible')

k_list = []
userlist = ['adm', 'lp', 'sync', 'shutdown', 'halt', 'news', 'uucp', 'operator', 'gopher', 'ftp', 'games',
'polkitd', 'tss', 'postfix', 'nobody']
usercontent = runCMD('cat /etc/group')
for user in userlist:
checkStandard_11('7.1.4 Delete system built-in account-{0}'.format(user), usercontent, user)

checkStandard_3('9.2 Configure virtual machine template - delete ssh host key', 'find /etc/ssh/ssh_host_*', 'No such file or directory')
checkStandard('9.2 Configure virtual machine template - hostname is:localhost.localdomain', version,'/etc/sysconfig/network',
'HOSTNAME= localhost.localdomain', '/etc/hostname', 'localhost.localdomain')
checkStandard_3('9.2 Configure virtual machine template - delete ssh host key', 'find /etc/udev/rules.d/70-*', 'No such file or directory')

# find /etc/sysconfig/network-scripts/ifcfg-e*
for filepath in runCMD('find /etc/sysconfig/network-scripts/ifcfg-e*'):
checkStandard_9('9.2 Configure "hwaddr =" line "in virtual machine template - {0}'.format(filepath.split('\n')[0]),filepath.split('\n')[0],'HWADDR=')
checkStandard_9('9.2 Configure "UUID =" line in virtual machine template - {0}'.format(filepath.split('\n')[0]), filepath.split('\n')[0], 'UUID=')
~~~







### Apache配置核查

不符合*SSE_SEC_WEB_APACHE_01--1、 确保使用log_module（打开log)--(必须)

不符合*SSE_SEC_WEB_APACHE_02--2、 禁止WebDAV--(必须)

不符合*SSE_SEC_WEB_APACHE_02--2、 禁止WebDAV--(必须)

不符合*SSE_SEC_WEB_APACHE_03--3、 禁止服务器状态及信息模块--(必须)

不符合*SSE_SEC_WEB_APACHE_03--3、 禁止服务器状态及信息模块--(必须)

不符合*SSE_SEC_WEB_APACHE_04--4、 禁止自动索引功能（目录列表）--(必须)

不符合*SSE_SEC_WEB_APACHE_05--5、 禁用Proxy功能--(必须)

不符合*SSE_SEC_WEB_APACHE_05--5、 禁用Proxy功能--(必须)

不符合*SSE_SEC_WEB_APACHE_06--6、 禁用用户目录模块（~)--(必须)

不符合*SSE_SEC_WEB_APACHE_07--7、 为Web服务器运行单独创建用户和组--(必须)

不符合*SSE_SEC_WEB_APACHE_07--7、 为Web服务器运行单独创建用户和组--(必须)

符合*SSE_SEC_WEB_APACHE_07--7、 为Web服务器运行单独创建用户和组--(必须)

The installation path of apache is /usr/local/apache2

不符合*SSE_SEC_WEB_APACHE_01--1、 确保使用log_module（打开log)--(必须)

不符合*SSE_SEC_WEB_APACHE_02--2、 禁止WebDAV--(必须)

不符合*SSE_SEC_WEB_APACHE_02--2、 禁止WebDAV--(必须)

不符合*SSE_SEC_WEB_APACHE_03--3、 禁止服务器状态及信息模块--(必须)

不符合*SSE_SEC_WEB_APACHE_03--3、 禁止服务器状态及信息模块--(必须)

不符合*SSE_SEC_WEB_APACHE_04--4、 禁止自动索引功能（目录列表）--(必须)

不符合*SSE_SEC_WEB_APACHE_05--5、 禁用Proxy功能--(必须)

不符合*SSE_SEC_WEB_APACHE_05--5、 禁用Proxy功能--(必须)

不符合*SSE_SEC_WEB_APACHE_06--6、 禁用用户目录模块（~)--(必须)

不符合*SSE_SEC_WEB_APACHE_07--7、 为Web服务器运行单独创建用户和组--(必须)

不符合*SSE_SEC_WEB_APACHE_07--7、 为Web服务器运行单独创建用户和组--(必须)

符合*SSE_SEC_WEB_APACHE_07--7、 为Web服务器运行单独创建用户和组--(必须)

不符合*SSE_SEC_WEB_APACHE_08--8、 给于apache用户为非交互shell(不能交互登陆)--(必须)

不符合*SSE_SEC_WEB_APACHE_09--9、 为Apache用户上锁--(必须)

符合*SSE_SEC_WEB_APACHE_10--10、 禁止对操作系统根目录的访问--(必须)--(Order deny, allow)

符合*SSE_SEC_WEB_APACHE_10--10、 禁止对操作系统根目录的访问--(必须)--(Deny from all)

符合*SSE_SEC_WEB_APACHE_11--11、 限制目录(.htaccess)权限覆盖

不符合*SSE_SEC_WEB_APACHE_12--12、 限制根目录下的“选项”配置--(必须)

None*SSE_SEC_WEB_APACHE_13--13、 限制文档目录不能Index，不能执行CGI等--(必须)

符合*SSE_SEC_WEB_APACHE_14--14、 删除缺省的HTML内容--(必须)--（删除已安装用户手册或者在配置中不用到手册的连接 httpd-manual）

yum list | grep -i httpd-manual

httpd-manual.noarch 2.4.6-89.el7.centos.1 updates

符合*SSE_SEC_WEB_APACHE_14--14、 删除缺省的HTML内容--(必须)--（移除或者注释掉服务器状态信息）

符合*SSE_SEC_WEB_APACHE_14--14、 删除缺省的HTML内容--(必须)--（移除或者注释掉服务器状态信息）

符合*SSE_SEC_WEB_APACHE_14--14、 删除缺省的HTML内容--(必须)--（移除或者注释掉服务器状态信息）

符合*SSE_SEC_WEB_APACHE_14--14、 删除缺省的HTML内容--(必须)--（移除或者注释掉服务器状态信息）

符合*SSE_SEC_WEB_APACHE_14--14、 删除缺省的HTML内容--(必须)--（移除或者注释掉服务器状态信息）

符合*SSE_SEC_WEB_APACHE_14--14、 删除缺省的HTML内容--(必须)--（移除或者注释掉服务器状态信息）

符合*SSE_SEC_WEB_APACHE_14--14、 删除缺省的HTML内容--(必须)--（移除或者注释掉服务器状态信息）

符合*SSE_SEC_WEB_APACHE_14--14、 删除缺省的HTML内容--(必须)--（移除或者注释掉服务器状态信息）

符合*SSE_SEC_WEB_APACHE_14--14、 删除缺省的HTML内容--(必须)--（移除或者注释掉服务器状态信息）

不符合*SSE_SEC_WEB_APACHE_15--15、 移除缺省的CGI测试脚本(cgi-bin/printenv)--(必须)

不符合*SSE_SEC_WEB_APACHE_15--15、 移除缺省的CGI测试脚本(cgi-bin/test-cgi)--(必须)

符合*SSE_SEC_WEB_APACHE_16--16、 限制HTTP的请求方式--(必须)

None*SSE_SEC_WEB_APACHE_16--16、 限制HTTP的请求方式--(必须)--(Limit HTTP methods)

不符合*SSE_SEC_WEB_APACHE_17--17、 禁用HTTP TRACE方法（系统缺省为On）--(必须)

未检查!!!SSE_SEC_WEB_APACHE_18--18、 限制使用HTTP协议版本--(必须)

不符合*SSE_SEC_WEB_APACHE_19--19、 限制服务器放回的头部信息（ServerToken)--(必须)

不符合*SSE_SEC_WEB_APACHE_20--20、 限制服务器签名信息--(必须)

None*SSE_SEC_WEB_APACHE_21--21、 限制返回错误信息内容ErrorDocument 400 /error/custom400.html

None*SSE_SEC_WEB_APACHE_21--21、 限制返回错误信息内容ErrorDocument 401 /error/custom401.html

None*SSE_SEC_WEB_APACHE_21--21、 限制返回错误信息内容ErrorDocument 403 /error/custom403.html

None*SSE_SEC_WEB_APACHE_21--21、 限制返回错误信息内容ErrorDocument 404 /error/custom404.html

None*SSE_SEC_WEB_APACHE_21--21、 限制返回错误信息内容ErrorDocument 405 /error/custom405.html

None*SSE_SEC_WEB_APACHE_21--21、 限制返回错误信息内容ErrorDocument 500 /error/custom500.html

未检查!!!SSE_SEC_WEB_APACHE_22--22、 确保合法的服务器证书--(必须)

未检查!!!SSE_SEC_WEB_APACHE_23--23、 检查证书位数--(必须)

未检查!!!SSE_SEC_WEB_APACHE_24--24、 保护服务器私钥--(必须)

不符合（File 404!!）*SSE_SEC_WEB_APACHE_25--25、 限制使用低版本SSL协议--(必须)

不符合（File 404!!）*SSE_SEC_WEB_APACHE_26--26、 限制使用弱算法--(必须)

不符合（File 404!!）*SSE_SEC_WEB_APACHE_27--27、 禁止压缩，防止CRIME攻击--(必须)

不符合（File 404!!）*SSE_SEC_WEB_APACHE_28--28、 以服务器的算法优先为主--(必须)

不符合（File 404!!）*SSE_SEC_WEB_APACHE_29--29、 确认不安全的协商已经关闭--(必须)

不符合（File 404!!）*SSE_SEC_WEB_APACHE_30--30、 限制CA验证的深度--(必须)

不符合*SSE_SEC_WEB_APACHE_31--31、 确认使用CRL--(必须)

不符合（File 404!!）*SSE_SEC_WEB_APACHE_32--32、 关闭动态方法调用--(必须)--

不符合SSE_SEC_WEB_APACHE_33--33、 允许/开放访问Web内容--(可选)--（只允许192.169..*的IP地址来访问）

不符合*SSE_SEC_WEB_APACHE_33--33、 允许/开放访问Web内容--(可选)--（只允许本机访问）

+++自行检查+++SSE_SEC_WEB_APACHE_34--34、 确保其他目录配置中没有出现AllowOverride关键字--(可选)

cat /etc/httpd/conf/httpd.conf|grep -i "^\s*AllowOverride"

不符合SSE_SEC_WEB_APACHE_35--35、 限制访问.ht文件--(可选)--（只允许本机访问）

未检查!!!SSE_SEC_WEB_APACHE_36--36、 限制可访问文件类型（使用FileMatch指令）--(可选)

不符合*SSE_SEC_WEB_APACHE_37--37、 配置错误日志级别（LogLevel)--(可选)

符合*SSE_SEC_WEB_APACHE_37--37、 配置错误日志级别（ErrorLog)--(可选)

不符合*SSE_SEC_WEB_APACHE_38--38、 配置访问日志格式(LogFormat)--(可选)

不符合*SSE_SEC_WEB_APACHE_38--38、 配置访问日志格式(CustomLog)--(可选)

未检查!!!SSE_SEC_WEB_APACHE_39--39、 日志监控--(可选)

不符合*SSE_SEC_WEB_APACHE_40--40、 日志存放及循环（磁盘空间vslog纪录时间）--(可选)

符合*SSE_SEC_WEB_APACHE_40--40、 日志存放及循环（磁盘空间vslog纪录时间）--(可选)

未检查!!!SSE_SEC_WEB_APACHE_41--41、 及时打补丁--(可选)

不符合*SSE_SEC_WEB_APACHE_42--42、 减缓DOS(Timeout 20)--(可选)

不符合*SSE_SEC_WEB_APACHE_42--42、 减缓DOS(KeepAlive On)--(可选)

不符合*SSE_SEC_WEB_APACHE_42--42、 减缓DOS(MaxKeepAliveRequests100)--(可选)

不符合*SSE_SEC_WEB_APACHE_42--42、 减缓DOS(KeepAliveTimeout15)--(可选)

不符合*SSE_SEC_WEB_APACHE_43--43、 减缓缓冲区溢出攻击(LimitRequestLine 512)--(可选)

不符合*SSE_SEC_WEB_APACHE_43--43、 减缓缓冲区溢出攻击(LimitRequestFields 100)--(可选)

不符合*SSE_SEC_WEB_APACHE_43--43、 减缓缓冲区溢出攻击(LimitRequestLine 512)--(可选)

不符合*SSE_SEC_WEB_APACHE_43--43、 减缓缓冲区溢出攻击(LimitRequestLine 512)--(可选)

不符合*SSE_SEC_WEB_APACHE_43--43、 减缓缓冲区溢出攻击(LimitRequestLine 512)--(可选)

+++自行检查+++SSE_SEC_WEB_APACHE_44--44、 限制监听地址和端口指令（Listen)--(可选)

cat /etc/httpd/conf/httpd.conf|grep -i "^listen"

The installation path of apache is /usr/local/apache2

不符合*SSE_SEC_WEB_APACHE_01--1、 确保使用log_module（打开log)--(必须)

不符合*SSE_SEC_WEB_APACHE_02--2、 禁止WebDAV--(必须)

不符合*SSE_SEC_WEB_APACHE_02--2、 禁止WebDAV--(必须)

不符合*SSE_SEC_WEB_APACHE_03--3、 禁止服务器状态及信息模块--(必须)

不符合*SSE_SEC_WEB_APACHE_03--3、 禁止服务器状态及信息模块--(必须)

不符合*SSE_SEC_WEB_APACHE_04--4、 禁止自动索引功能（目录列表）--(必须)

不符合*SSE_SEC_WEB_APACHE_05--5、 禁用Proxy功能--(必须)

不符合*SSE_SEC_WEB_APACHE_05--5、 禁用Proxy功能--(必须)

不符合*SSE_SEC_WEB_APACHE_06--6、 禁用用户目录模块（~)--(必须)

不符合*SSE_SEC_WEB_APACHE_07--7、 为Web服务器运行单独创建用户和组--(必须)

不符合*SSE_SEC_WEB_APACHE_07--7、 为Web服务器运行单独创建用户和组--(必须)

符合*SSE_SEC_WEB_APACHE_07--7、 为Web服务器运行单独创建用户和组--(必须)

不符合*SSE_SEC_WEB_APACHE_08--8、 给于apache用户为非交互shell(不能交互登陆)--(必须)

不符合*SSE_SEC_WEB_APACHE_09--9、 为Apache用户上锁--(必须)

符合*SSE_SEC_WEB_APACHE_10--10、 禁止对操作系统根目录的访问--(必须)--(Order deny, allow)

符合*SSE_SEC_WEB_APACHE_10--10、 禁止对操作系统根目录的访问--(必须)--(Deny from all)

符合*SSE_SEC_WEB_APACHE_11--11、 限制目录(.htaccess)权限覆盖

不符合*SSE_SEC_WEB_APACHE_12--12、 限制根目录下的“选项”配置--(必须)

None*SSE_SEC_WEB_APACHE_13--13、 限制文档目录不能Index，不能执行CGI等--(必须)

符合*SSE_SEC_WEB_APACHE_14--14、 删除缺省的HTML内容--(必须)--（删除已安装用户手册或者在配置中不用到手册的连接 httpd-manual）

yum list | grep -i httpd-manual

httpd-manual.noarch 2.4.6-89.el7.centos.1 updates

符合*SSE_SEC_WEB_APACHE_14--14、 删除缺省的HTML内容--(必须)--（移除或者注释掉服务器状态信息）

符合*SSE_SEC_WEB_APACHE_14--14、 删除缺省的HTML内容--(必须)--（移除或者注释掉服务器状态信息）

符合*SSE_SEC_WEB_APACHE_14--14、 删除缺省的HTML内容--(必须)--（移除或者注释掉服务器状态信息）

符合*SSE_SEC_WEB_APACHE_14--14、 删除缺省的HTML内容--(必须)--（移除或者注释掉服务器状态信息）

符合*SSE_SEC_WEB_APACHE_14--14、 删除缺省的HTML内容--(必须)--（移除或者注释掉服务器状态信息）

符合*SSE_SEC_WEB_APACHE_14--14、 删除缺省的HTML内容--(必须)--（移除或者注释掉服务器状态信息）

符合*SSE_SEC_WEB_APACHE_14--14、 删除缺省的HTML内容--(必须)--（移除或者注释掉服务器状态信息）

符合*SSE_SEC_WEB_APACHE_14--14、 删除缺省的HTML内容--(必须)--（移除或者注释掉服务器状态信息）

符合*SSE_SEC_WEB_APACHE_14--14、 删除缺省的HTML内容--(必须)--（移除或者注释掉服务器状态信息）

不符合*SSE_SEC_WEB_APACHE_15--15、 移除缺省的CGI测试脚本(cgi-bin/printenv)--(必须)

不符合*SSE_SEC_WEB_APACHE_15--15、 移除缺省的CGI测试脚本(cgi-bin/test-cgi)--(必须)

符合*SSE_SEC_WEB_APACHE_16--16、 限制HTTP的请求方式--(必须)

None*SSE_SEC_WEB_APACHE_16--16、 限制HTTP的请求方式--(必须)--(Limit HTTP methods)

不符合*SSE_SEC_WEB_APACHE_17--17、 禁用HTTP TRACE方法（系统缺省为On）--(必须)

未检查!!!SSE_SEC_WEB_APACHE_18--18、 限制使用HTTP协议版本--(必须)

不符合*SSE_SEC_WEB_APACHE_19--19、 限制服务器放回的头部信息（ServerToken)--(必须)

不符合*SSE_SEC_WEB_APACHE_20--20、 限制服务器签名信息--(必须)

None*SSE_SEC_WEB_APACHE_21--21、 限制返回错误信息内容ErrorDocument 400 /error/custom400.html

None*SSE_SEC_WEB_APACHE_21--21、 限制返回错误信息内容ErrorDocument 401 /error/custom401.html

None*SSE_SEC_WEB_APACHE_21--21、 限制返回错误信息内容ErrorDocument 403 /error/custom403.html

None*SSE_SEC_WEB_APACHE_21--21、 限制返回错误信息内容ErrorDocument 404 /error/custom404.html

None*SSE_SEC_WEB_APACHE_21--21、 限制返回错误信息内容ErrorDocument 405 /error/custom405.html

None*SSE_SEC_WEB_APACHE_21--21、 限制返回错误信息内容ErrorDocument 500 /error/custom500.html

未检查!!!SSE_SEC_WEB_APACHE_22--22、 确保合法的服务器证书--(必须)

未检查!!!SSE_SEC_WEB_APACHE_23--23、 检查证书位数--(必须)

未检查!!!SSE_SEC_WEB_APACHE_24--24、 保护服务器私钥--(必须)

不符合（File 404!!）*SSE_SEC_WEB_APACHE_25--25、 限制使用低版本SSL协议--(必须)

不符合（File 404!!）*SSE_SEC_WEB_APACHE_26--26、 限制使用弱算法--(必须)

不符合（File 404!!）*SSE_SEC_WEB_APACHE_27--27、 禁止压缩，防止CRIME攻击--(必须)

不符合（File 404!!）*SSE_SEC_WEB_APACHE_28--28、 以服务器的算法优先为主--(必须)

不符合（File 404!!）*SSE_SEC_WEB_APACHE_29--29、 确认不安全的协商已经关闭--(必须)

不符合（File 404!!）*SSE_SEC_WEB_APACHE_30--30、 限制CA验证的深度--(必须)

不符合*SSE_SEC_WEB_APACHE_31--31、 确认使用CRL--(必须)

不符合（File 404!!）*SSE_SEC_WEB_APACHE_32--32、 关闭动态方法调用--(必须)--

不符合SSE_SEC_WEB_APACHE_33--33、 允许/开放访问Web内容--(可选)--（只允许192.169..*的IP地址来访问）

不符合*SSE_SEC_WEB_APACHE_33--33、 允许/开放访问Web内容--(可选)--（只允许本机访问）

+++自行检查+++SSE_SEC_WEB_APACHE_34--34、 确保其他目录配置中没有出现AllowOverride关键字--(可选)

cat /etc/httpd/conf/httpd.conf|grep -i "^\s*AllowOverride"

不符合SSE_SEC_WEB_APACHE_35--35、 限制访问.ht文件--(可选)--（只允许本机访问）

未检查!!!SSE_SEC_WEB_APACHE_36--36、 限制可访问文件类型（使用FileMatch指令）--(可选)

不符合*SSE_SEC_WEB_APACHE_37--37、 配置错误日志级别（LogLevel)--(可选)

符合*SSE_SEC_WEB_APACHE_37--37、 配置错误日志级别（ErrorLog)--(可选)

不符合*SSE_SEC_WEB_APACHE_38--38、 配置访问日志格式(LogFormat)--(可选)

不符合*SSE_SEC_WEB_APACHE_38--38、 配置访问日志格式(CustomLog)--(可选)

未检查!!!SSE_SEC_WEB_APACHE_39--39、 日志监控--(可选)

不符合*SSE_SEC_WEB_APACHE_40--40、 日志存放及循环（磁盘空间vslog纪录时间）--(可选)

符合*SSE_SEC_WEB_APACHE_40--40、 日志存放及循环（磁盘空间vslog纪录时间）--(可选)

未检查!!!SSE_SEC_WEB_APACHE_41--41、 及时打补丁--(可选)

不符合*SSE_SEC_WEB_APACHE_42--42、 减缓DOS(Timeout 20)--(可选)

不符合*SSE_SEC_WEB_APACHE_42--42、 减缓DOS(KeepAlive On)--(可选)

不符合*SSE_SEC_WEB_APACHE_42--42、 减缓DOS(MaxKeepAliveRequests100)--(可选)

不符合*SSE_SEC_WEB_APACHE_42--42、 减缓DOS(KeepAliveTimeout15)--(可选)

不符合*SSE_SEC_WEB_APACHE_43--43、 减缓缓冲区溢出攻击(LimitRequestLine 512)--(可选)

不符合*SSE_SEC_WEB_APACHE_43--43、 减缓缓冲区溢出攻击(LimitRequestFields 100)--(可选)

不符合*SSE_SEC_WEB_APACHE_43--43、 减缓缓冲区溢出攻击(LimitRequestLine 512)--(可选)

不符合*SSE_SEC_WEB_APACHE_43--43、 减缓缓冲区溢出攻击(LimitRequestLine 512)--(可选)

不符合*SSE_SEC_WEB_APACHE_43--43、 减缓缓冲区溢出攻击(LimitRequestLine 512)--(可选)

+++自行检查+++SSE_SEC_WEB_APACHE_44--44、 限制监听地址和端口指令（Listen)--(可选)

cat /etc/httpd/conf/httpd.conf|grep -i "^listen"

~~~python
import re
import os
import sys

# 执行命令，返回字符串
def runCMD(cmd):
if (len(cmd) == 0):
return
else:
d = os.popen(cmd)
dd = d.read().decode('GBK').encode('utf-8')
return dd


# 读文件
def readFile(filepath):
result = {}
result['flag'] = 0
try:
with open(filepath, 'r') as f1:
list1 = f1.readlines()
for i in range(0, len(list1)):
list1[i] = list1[i].rstrip('\n')
result['flag'] = 1
result['content'] = list1
return result
except:
return result


# 匹配到就符合
def check(content, status):
status = status.replace(" ", "\\s*")
if re.search(status, content, re.IGNORECASE):
return "符合"
return "不符合"


def get_a_part_of_the_article(filePath, start, end):
result = readFile(filePath)
if result['flag'] == 1:
flag = 0
frequency = 0
content = {}
for i in result['content']:
if re.search(start, i, re.IGNORECASE):
flag = 1
content[frequency] = []
if flag == 1:
content[frequency].append(i)
if re.search(end, i, re.IGNORECASE):
flag = 0
frequency += 1
return content
else:
return "不符合（File 404!!）"


def checkStatus4(filepath, start, end, statsus):
statsus = "^" + statsus
statsus = statsus.replace(" ", "\\s*")
con = get_a_part_of_the_article(filepath, start, end)
if type(con) == str:
return con
if len(con.keys()):
for i in con.keys():
for cont in con[i]:
# print cont
if re.search(statsus, cont, re.IGNORECASE):
return "符合"
return "不符合"


# 严格匹配行
def checkStatus(filepath, statsus):
file = readFile(filepath)
if file['flag'] == 1:
filecontent = file['content']
statsus = "^" + statsus
statsus = statsus.replace(" ", "\\s*")
for con in filecontent:
if re.search(statsus, con, re.IGNORECASE):
return "符合"
return "不符合"
else:
return "不符合（File 404!!）"


# 检查是否合规
# 分版本
def checkStandard(file, testcontent, version, file1, standard1, file2, standard2):
if version == '6':
qq = checkStatus(file1, standard1)
write2file(file, '{0}*********{1}{2}'.format(qq, testcontent, '\r\n'))
else:
qq = checkStatus(file2, standard2)
write2file(file, '{0}*********{1}{2}'.format(qq, testcontent, '\r\n'))


# 不分版本
def checkStandard_1(file, testcontent, file1, standard1):
qq = checkStatus(file1, standard1)
write2file(file, '{0}*********{1}{2}'.format(qq, testcontent, '\r\n'))


# 不分版本
def checkStandard_2(file, testcontent, cmd, standard, printresult=0):
result = runCMD(cmd)
if printresult == 1:
write2file(file, result)
qq = check(result, standard)
write2file(file, '{0}*********{1}{2}'.format(qq, testcontent, '\r\n'))


def checkStandard_10(file, testcontent, file1, start, end, standard1):
qq = checkStatus4(file1, start, end, standard1)
write2file(file, '{0}*********{1}{2}'.format(qq, testcontent, '\r\n'))


def write2file(file, content):
with open(file, 'a+') as f:
f.write(content + '\n')


def fullPath(path1, path2):
return '{0}{1}'.format(path1, path2)


if __name__ == '__main__':

file = '{0}/{1}'.format(sys.path[0],'jiancha_apache.txt')
installationPath = '/usr/local/apache2'
if len(installationPath) < 5:
installationPath = '/etc/httpd'
write2file(file, 'The installation path of apache is {0}{1}'.format(installationPath, '\r\n'))

httpd_conf = fullPath(installationPath, '/conf/httpd.conf')
ssl_conf = fullPath(installationPath, '/conf.d/ssl.conf')
checkStandard_1(file, 'SSE_SEC_WEB_APACHE_01--1、 确保使用log_module（打开log)--(必须)', httpd_conf,
'LoadModulelog_config_module modules/mod_log_config.so')
checkStandard_1(file, 'SSE_SEC_WEB_APACHE_02--2、 禁止WebDAV--(必须)', httpd_conf,
'(#)+(\s)*LoadModuledav_module modules/mod_dav.so')
checkStandard_1(file, 'SSE_SEC_WEB_APACHE_02--2、 禁止WebDAV--(必须)', httpd_conf,
'(#)+(\s)*LoadModuledav_fs_module modules/mod_dav_fs.so')
checkStandard_1(file, 'SSE_SEC_WEB_APACHE_03--3、 禁止服务器状态及信息模块--(必须)', httpd_conf,
'(#)+(\s)*LoadModuleinfo_module modules/mod_info.so')
checkStandard_1(file, 'SSE_SEC_WEB_APACHE_03--3、 禁止服务器状态及信息模块--(必须)', httpd_conf,
'(#)+(\s)*LoadModulestatus_module modules/mod_status.so')
checkStandard_1(file, 'SSE_SEC_WEB_APACHE_04--4、 禁止自动索引功能（目录列表）--(必须)', httpd_conf,
'(#)+(\s)*LoadModuleautoindex_module modules/mod_autoindex.so')
checkStandard_1(file, 'SSE_SEC_WEB_APACHE_05--5、 禁用Proxy功能--(必须)', httpd_conf,
'(#)+(\s)*LoadModuleproxy_module modules/mod_proxy.so')
checkStandard_1(file, 'SSE_SEC_WEB_APACHE_05--5、 禁用Proxy功能--(必须)', httpd_conf,
'(#)+(\s)*LoadModuleproxy_balancer_module modules/mod_proxy_balancer.so')
checkStandard_1(file, 'SSE_SEC_WEB_APACHE_06--6、 禁用用户目录模块（~)--(必须)', httpd_conf,
'(#)+(\s)*LoadModuleuserdir_module modules/mod_userdir.so')
checkStandard_1(file, 'SSE_SEC_WEB_APACHE_07--7、 为Web服务器运行单独创建用户和组--(必须)', httpd_conf,
'^User apache')
checkStandard_1(file, 'SSE_SEC_WEB_APACHE_07--7、 为Web服务器运行单独创建用户和组--(必须)', httpd_conf,
'^Group apache')
try:
id_apache = runCMD('id apache')
if (int(re.findall('\d+', id_apache)[0]) < 499) & (int(re.findall('\d+', id_apache)[1]) < 499):
write2file(file, '符合*********SSE_SEC_WEB_APACHE_07--7、 为Web服务器运行单独创建用户和组--(必须)\r\n')
else:
write2file(file, '不符合*********SSE_SEC_WEB_APACHE_07--7、 为Web服务器运行单独创建用户和组--(必须)\r\n')
except:
write2file(file, '符合*********SSE_SEC_WEB_APACHE_07--7、 为Web服务器运行单独创建用户和组--(必须)\r\n')
checkStandard_2(file, 'SSE_SEC_WEB_APACHE_08--8、 给于apache用户为非交互shell(不能交互登陆)--(必须)', 'grep apache /etc/passwd',
'/sbin/nologin')
checkStandard_2(file, 'SSE_SEC_WEB_APACHE_09--9、 为Apache用户上锁--(必须)', 'passwd -S apache',
'Password locked')
checkStandard_10(file, 'SSE_SEC_WEB_APACHE_10--10、 禁止对操作系统根目录的访问--(必须)--(Order deny, allow)', httpd_conf,
'<Directory />', '</Directory>',
'(\s)*Order deny, allow')
checkStandard_10(file, 'SSE_SEC_WEB_APACHE_10--10、 禁止对操作系统根目录的访问--(必须)--(Deny from all)', httpd_conf,
'<Directory />',
'</Directory>',
'(\s)*Deny from all')

checkStandard_10(file, 'SSE_SEC_WEB_APACHE_11--11、 限制目录(.htaccess)权限覆盖', httpd_conf, '<Directory />',
'</Directory>',
'\s*AllowOverride None')
checkStandard_10(file, 'SSE_SEC_WEB_APACHE_12--12、 限制根目录下的“选项”配置--(必须)', httpd_conf, '<Directory />',
'</Directory>',
'\s*Options None')
checkStandard_10(file, 'SSE_SEC_WEB_APACHE_13--13、 限制文档目录不能Index，不能执行CGI等--(必须)', httpd_conf,
'<Directory \"/usr/local/apache2/htdocs/\">', '</Directory>', '\s*Options None')
# print '未检查***!!!***SSE_SEC_WEB_APACHE_14--14、 删除缺省的HTML内容--(必须)--（在Web根目录下删除缺省的安装的网页（index.html)）'
checkStandard_2(file, 'SSE_SEC_WEB_APACHE_14--14、 删除缺省的HTML内容--(必须)--（删除已安装用户手册或者在配置中不用到手册的连接 httpd-manual）',
'yum list | grep -i httpd-manual', 'httpd-manual')
write2file(file, 'yum list | grep -i httpd-manual\r\n')
write2file(file, runCMD('yum list | grep -i httpd-manual'))
SSE_SEC_WEB_APACHE_14_c = ['SetHandler server-status', 'Order deny,allow', 'Deny from all',
'Allow from .example.com']
for ii in SSE_SEC_WEB_APACHE_14_c:
if checkStatus4(httpd_conf, '<Location /server-status>', '</Location>', '^\s*{0}'.format(ii)) == '符合':
write2file(file, '不符合*********SSE_SEC_WEB_APACHE_14--14、 删除缺省的HTML内容--(必须)--（移除或者注释掉服务器状态信息）\r\n')
break
write2file(file, '符合*********SSE_SEC_WEB_APACHE_14--14、 删除缺省的HTML内容--(必须)--（移除或者注释掉服务器状态信息）\r\n')
SSE_SEC_WEB_APACHE_14_d = ['SetHandler server-info', 'Order deny,allow', 'Deny from all',
'Allow from .example.com']
for ii in SSE_SEC_WEB_APACHE_14_d:
if checkStatus4(httpd_conf, '<Location /server-info>', '</Location>', '^\s*{0}'.format(ii)) == '符合':
write2file(file, '不符合*********SSE_SEC_WEB_APACHE_14--14、 删除缺省的HTML内容--(必须)--（移除或者注释掉服务器状态信息）\r\n')
break
write2file(file, '符合*********SSE_SEC_WEB_APACHE_14--14、 删除缺省的HTML内容--(必须)--（移除或者注释掉服务器状态信息）\r\n')
SSE_SEC_WEB_APACHE_14_e = ['SetHandler server-status', 'PerlResponseHandler Apache2::Status', 'Order deny,allow',
'Deny from all',
'Allow from .example.com']
for ii in SSE_SEC_WEB_APACHE_14_e:
if checkStatus4(httpd_conf, '<Location /perl-status>', '</Location>', '^\s*{0}'.format(ii)) == '符合':
write2file(file, '不符合*********SSE_SEC_WEB_APACHE_14--14、 删除缺省的HTML内容--(必须)--（移除或者注释掉服务器状态信息）\r\n')
break
write2file(file, '符合*********SSE_SEC_WEB_APACHE_14--14、 删除缺省的HTML内容--(必须)--（移除或者注释掉服务器状态信息）\r\n')

checkStandard_2(file, 'SSE_SEC_WEB_APACHE_15--15、 移除缺省的CGI测试脚本(cgi-bin/printenv)--(必须)',
'cat /etc/httpd/cgi-bin/printenv', '.*?No such file or directory')
checkStandard_2(file, 'SSE_SEC_WEB_APACHE_15--15、 移除缺省的CGI测试脚本(cgi-bin/test-cgi)--(必须)',
'cat /etc/httpd/cgi-bin/test-cgi', '.*?No such file or directory')
checkStandard_10(file, 'SSE_SEC_WEB_APACHE_16--16、 限制HTTP的请求方式--(必须)', httpd_conf,
'<Directory "/usr/local/apache2/htdocs">', '</Directory>', '\s*Order allow,deny')
checkStandard_10(file, 'SSE_SEC_WEB_APACHE_16--16、 限制HTTP的请求方式--(必须)--(Limit HTTP methods)', httpd_conf,
'\s*<LimitExcept GET POST OPTIONS HEAD>', '\s*</LimitExcept>', '\s*deny from all')
checkStandard_1(file, 'SSE_SEC_WEB_APACHE_17--17、 禁用HTTP TRACE方法（系统缺省为On）--(必须)', httpd_conf,
'\s*TraceEnable off')
write2file(file, '未检查***!!!***SSE_SEC_WEB_APACHE_18--18、 限制使用HTTP协议版本--(必须)\r\n')
checkStandard_1(file, 'SSE_SEC_WEB_APACHE_19--19、 限制服务器放回的头部信息（ServerToken)--(必须)', httpd_conf,
'\s*ServerTokens Prod')
checkStandard_1(file, 'SSE_SEC_WEB_APACHE_20--20、 限制服务器签名信息--(必须)', httpd_conf, '\s*ServerSignature Off')
# checkStandard_10(file,'SSE_SEC_WEB_APACHE_21--21、 限制返回错误信息内容', httpd_conf, '<Directory “/var/www/error”>',
# '</Directory>', '\s*Options None')
sa = ['ErrorDocument 400 /error/custom400.html'
, 'ErrorDocument 401 /error/custom401.html'
, 'ErrorDocument 403 /error/custom403.html'
, 'ErrorDocument 404 /error/custom404.html'
, 'ErrorDocument 405 /error/custom405.html'
, 'ErrorDocument 500 /error/custom500.html'
]
for Sa in sa:
checkStandard_10(file, 'SSE_SEC_WEB_APACHE_21--21、 限制返回错误信息内容{0}'.format(Sa), httpd_conf,
'<Directory “/var/www/error”>',
'</Directory>', '\s*{0}'.format(Sa))

write2file(file, '未检查***!!!***SSE_SEC_WEB_APACHE_22--22、 确保合法的服务器证书--(必须)\r\n')
write2file(file, '未检查***!!!***SSE_SEC_WEB_APACHE_23--23、 检查证书位数--(必须)\r\n')
write2file(file, '未检查***!!!***SSE_SEC_WEB_APACHE_24--24、 保护服务器私钥--(必须)\r\n')

checkStandard_1(file, 'SSE_SEC_WEB_APACHE_25--25、 限制使用低版本SSL协议--(必须)', ssl_conf,
'\s*SSLProtocol all -SSLv2\s*-SSLv3')
checkStandard_1(file, 'SSE_SEC_WEB_APACHE_26--26、 限制使用弱算法--(必须)', ssl_conf,
'\s*SSLCipherSuite RC4-SHA:HIGH:!ADH!MD5')
checkStandard_1(file, 'SSE_SEC_WEB_APACHE_27--27、 禁止压缩，防止CRIME攻击--(必须)', ssl_conf, '\s*SSLCompression Off')
checkStandard_1(file, 'SSE_SEC_WEB_APACHE_28--28、 以服务器的算法优先为主--(必须)', ssl_conf, '\s*SSLHonorCipherOrder On')
checkStandard_1(file, 'SSE_SEC_WEB_APACHE_29--29、 确认不安全的协商已经关闭--(必须)', ssl_conf,
'\s*SSLInsecureRenegotiation Off')
checkStandard_1(file, 'SSE_SEC_WEB_APACHE_30--30、 限制CA验证的深度--(必须)', ssl_conf, '\s*SSLVerifyDepth 2')
if checkStatus(ssl_conf, '\s*SSLCompression Off') == '符合' or checkStatus(ssl_conf,
'\s*SSLCARevocationPath directory-path') == '符合':
write2file(file, '符合*********SSE_SEC_WEB_APACHE_31--31、 确认使用CRL--(必须)\r\n')
else:
write2file(file, '不符合*********SSE_SEC_WEB_APACHE_31--31、 确认使用CRL--(必须)\r\n')

try:
for filename in runCMD('find / -name struts.xml').split('\n'):
if len(filename) <= 10:
checkStandard_1(file, 'SSE_SEC_WEB_APACHE_32--32、 关闭动态方法调用--(必须)--{0}'.format(filename), filename,
'<constant name=\"struts.enable.DynamicMethodInvocation\" value=\"false\"\/>')
else:
pass
except:
write2file(file, '不成功*********SSE_SEC_WEB_APACHE_32--32、 关闭动态方法调用--(必须)\r\n')

SSE_SEC_WEB_APACHE_33_1 = ['Order deny,allow', 'Deny from all', 'allow from 192.169.']
for ii in SSE_SEC_WEB_APACHE_33_1:
if checkStatus4(httpd_conf, '<Directory "/var/www/html/">', '</Directory>', '^\s*{0}'.format(ii)) != '符合':
write2file(file, '不符合*********SSE_SEC_WEB_APACHE_33--33、 允许/开放访问Web内容--(可选)--（只允许192.169.*.*的IP地址来访问）\r\n')
break
write2file(file, '符合*********SSE_SEC_WEB_APACHE_33--33、 允许/开放访问Web内容--(可选)--（只允许192.169.*.*的IP地址来访问）\r\n')

SSE_SEC_WEB_APACHE_33_2 = ['Order deny,allow', 'Deny from all', 'Allow from 127.0.0.1', 'Allow from :1 ']
for ii in SSE_SEC_WEB_APACHE_33_2:
if checkStatus4(httpd_conf, '<Directory "/var/www/html/">', '</Directory>', '^\s*{0}'.format(ii)) != '符合':
write2file(file, '不符合*********SSE_SEC_WEB_APACHE_33--33、 允许/开放访问Web内容--(可选)--（只允许本机访问）\r\n')
break
write2file(file, '符合*********SSE_SEC_WEB_APACHE_33--33、 允许/开放访问Web内容--(可选)--（只允许本机访问）\r\n')
write2file(file, '+++自行检查+++SSE_SEC_WEB_APACHE_34--34、 确保其他目录配置中没有出现AllowOverride关键字--(可选)\r\n')
write2file(file, 'cat /etc/httpd/conf/httpd.conf|grep -i "^\s*AllowOverride"\r\n')
write2file(file, runCMD('cat /etc/httpd/conf/httpd.conf|grep -i "^\s*AllowOverride"\r\n'))
SSE_SEC_WEB_APACHE_35 = ['Order deny,allow', 'Deny from all', 'Satisfy All']
for ii in SSE_SEC_WEB_APACHE_35:
if checkStatus4(httpd_conf, '<FilesMatch "^\.ht">', '</FilesMatch>', '^\s*{0}'.format(ii)) != '符合':
write2file(file, '不符合*********SSE_SEC_WEB_APACHE_35--35、 限制访问.ht*文件--(可选)--（只允许本机访问）\r\n')
break
write2file(file, '符合*********SSE_SEC_WEB_APACHE_35--35、 限制访问.ht*文件--(可选)--（只允许本机访问）\r\n')
write2file(file, '未检查***!!!***SSE_SEC_WEB_APACHE_36--36、 限制可访问文件类型（使用FileMatch指令）--(可选)\r\n')

checkStandard_1(file, 'SSE_SEC_WEB_APACHE_37--37、 配置错误日志级别（LogLevel)--(可选)', httpd_conf, '\s*LogLevel Notice')
checkStandard_1(file, 'SSE_SEC_WEB_APACHE_37--37、 配置错误日志级别（ErrorLog)--(可选)', httpd_conf,
'\s*ErrorLog logs\/error_log')
checkStandard_1(file, 'SSE_SEC_WEB_APACHE_38--38、 配置访问日志格式(LogFormat)--(可选)', httpd_conf,
'\s*LogFormat \"%h %l %u %t \\\"%r\\\" %>s %b \\\"%{Referer}i\\\" \\\"%{User-agent}i\\\"\" combined')
checkStandard_1(file, 'SSE_SEC_WEB_APACHE_38--38、 配置访问日志格式\(CustomLog\)--\(可选\)', httpd_conf,
'\s*CustomLog log/access_log combined')
write2file(file, '未检查***!!!***SSE_SEC_WEB_APACHE_39--39、 日志监控--(可选)\r\n')

checkStandard_1(file, 'SSE_SEC_WEB_APACHE_40--40、 日志存放及循环（磁盘空间vslog纪录时间）--(可选)', httpd_conf,
'\s*CustomLog \"\|bin\/rotatelogs -l \/var\/logs\/logfile.%Y.%m.%d 86400\" combined')
checkStandard_2(file, 'SSE_SEC_WEB_APACHE_40--40、 日志存放及循环（磁盘空间vslog纪录时间）--(可选)', 'find /etc/logrotate.conf',
'\/etc\/logrotate.conf')
write2file(file, '未检查***!!!***SSE_SEC_WEB_APACHE_41--41、 及时打补丁--(可选)\r\n')

checkStandard_1(file, 'SSE_SEC_WEB_APACHE_42--42、 减缓DOS(Timeout 20)--(可选)', httpd_conf, '\s*Timeout\s*20')
checkStandard_1(file, 'SSE_SEC_WEB_APACHE_42--42、 减缓DOS(KeepAlive On)--(可选)', httpd_conf, '\s*KeepAlive\s*On')
checkStandard_1(file, 'SSE_SEC_WEB_APACHE_42--42、 减缓DOS(MaxKeepAliveRequests100)--(可选)', httpd_conf,
'\s*MaxKeepAliveRequests\s*100')
checkStandard_1(file, 'SSE_SEC_WEB_APACHE_42--42、 减缓DOS(KeepAliveTimeout15)--(可选)', httpd_conf,
'\s*KeepAliveTimeout\s*15')
checkStandard_1(file, 'SSE_SEC_WEB_APACHE_43--43、 减缓缓冲区溢出攻击(LimitRequestLine 512)--(可选)', httpd_conf,
'\s*LimitRequestLine 512')
checkStandard_1(file, 'SSE_SEC_WEB_APACHE_43--43、 减缓缓冲区溢出攻击(LimitRequestFields 100)--(可选)', httpd_conf,
'\s*LimitRequestFields 100')
checkStandard_1(file, 'SSE_SEC_WEB_APACHE_43--43、 减缓缓冲区溢出攻击(LimitRequestLine 512)--(可选)', httpd_conf,
'\s*LimitRequestFieldsize 1024')
checkStandard_1(file, 'SSE_SEC_WEB_APACHE_43--43、 减缓缓冲区溢出攻击(LimitRequestLine 512)--(可选)', httpd_conf,
'\s*LimitRequestBody 102400')
checkStandard_1(file, 'SSE_SEC_WEB_APACHE_43--43、 减缓缓冲区溢出攻击(LimitRequestLine 512)--(可选)', httpd_conf,
'\s*LimitXMLRequestBody 10240')

write2file(file, '+++自行检查+++SSE_SEC_WEB_APACHE_44--44、 限制监听地址和端口指令（Listen)--(可选)\r\n')
write2file(file, 'cat /etc/httpd/conf/httpd.conf|grep -i "^listen"\r\n')
write2file(file, runCMD('cat /etc/httpd/conf/httpd.conf|grep -i "^listen"'))
~~~

### Docker配置核查

脚本检测结果有4种：
1、qualified
2、unqualified
3、not found
4、列表

qualified是某条规范的检测结果，定义为合格。
unqualified是某条规范的检测结果，定义为合格。

not found说明脚本尝试打开的配置文件没有找到，出现这种情况的原因可能有以下情况导致的：
1、权限问题：有一些配置文件需要一定权限才能打开；
2、未能在指定位置（以《xxx技术有限责任公司Docker安全配置规范.docx》为准）找到对应的配置文件；
3、确认相应服务是否安装，且安装位置是否有问题（这会导致脚本不能在文档指定的位置找到相应配置文件）。

~~~python
# -*-coding:utf8 -*-
import os
check_docker = {}
try:
if os.path.isdir('/varlib/docker/etc/fstab'):
check_docker['1.为容器创建独立的分区'] = os.popen('grep /varlib/docker/etc/fstab').read()
else:
check_docker['1.为容器创建独立的分区'] = '未找到file'
except:
check_docker['1.为容器创建独立的分区'] = '在' + '/varlib/docker/etc/fstab' + '未找到'

try:
check_docker['2.使用已更新过的Linux内核'] = os.popen('uname -r').read()
except:
check_docker['2.使用已更新过的Linux内核'] = 'not found'

try:
check_docker['3.只允许受信任的用户控制Docker deamon'] = os.popen('getent group docker').read()
except:
check_docker['3.只允许受信任的用户控制Docker deamon'] = 'not found'

try:
check_docker['4.审计Docker deamon'] = os.popen('auditctl -l | grep /usr/bin/docker').read()
except:
check_docker['4.审计Docker deamon'] = '在' + '/usr/bin/docker' + '未找到'

try:
check_docker['5.审计var/lib/docker'] = os.popen('auditctl -l | grep /usr/bin/docker').read()
except:
check_docker['5.审计var/lib/docker'] = '在' + '/usr/bin/docker' + '未找到'

try:
check_docker['6.审计/etc/docker'] = os.popen('auditctl -l | grep /etc/docker').read()
except:
check_docker['6.审计/etc/docker'] = '在' + '/etc/docker' + '未找到'

try:
f_dir_list = os.popen('systemctl show -p FragmentPath docker.service').read().split('=')[1]
if f_dir_list == '\n':
check_docker['7.审计docker.service'] = 'unqualified'
else:
infos = os.popen('auditctl -l | grep docker.service')
check_docker['7.审计docker.service'] = infos
except:
f_dir_list = os.popen('systemctl show -p FragmentPath docker.service').read().split('\n')
check_docker['7.审计docker.service'] = '在' + str(f_dir_list) + '未找到'

try:
f_dir_list = os.popen('systemctl show -p FragmentPath docker.socket').read().split('=')[1]
if f_dir_list == '\n':
check_docker['8.审计docker.socket'] = 'unqualified'
else:
infos = os.popen('auditctl -l | grep docker.socket')
check_docker['8.审计docker.socket'] = infos
except:
f_dir_list = os.popen('systemctl show -p FragmentPath docker.service').read().split('\n')
check_docker['8.审计docker.socket'] = '在' + str(f_dir_list) + '未找到'

try:
check_docker['9.审计/etc/default/docker'] = os.popen('auditctl -l | grep /etc/default/docker').read()
except:
check_docker['9.审计/etc/default/docker'] = '在' + '/etc/default/docker' + '未找到'

try:
check_docker['10.审计/etc/docker/daemon.json'] = os.popen('auditctl -l | grep /etc/docker/daemon.json').read()
except:
check_docker['10.审计/etc/docker/daemon.json'] = '在' + '/etc/docker/daemon.json' + '未找到'

try:
check_docker['11.审计/usr/bin/docker-containerd'] = os.popen('auditctl -l | grep /usr/bin/docker-containerd').read()
except:
check_docker['11.审计/usr/bin/docker-containerd'] = '在' + '/usr/bin/docker-containerd' + '未找到'

try:
check_docker['12.审计/usr/bin/docker-runc'] = os.popen('auditctl -l | grep /usr/bin/docker-runc').read()
except:
check_docker['12.审计/usr/bin/docker-runc'] = '/usr/bin/docker-runc' + '未找到'

try:
f = os.popen('ps -ef | grep docker').read()
if 'icc=false' in f:
check_docker['13.限制容器之间的网络流量'] = 'qualified'
else:
check_docker['13.限制容器之间的网络流量'] = 'unqualified'
except:
check_docker['13.限制容器之间的网络流量'] = '未找到'

try:
f = os.popen('ps -ef | grep docker').read()
if 'iptables' in f or 'iptables=false':
check_docker['14.允许Docker更改iptables'] = 'unqualified'
else:
check_docker['14.允许Docker更改iptables'] = 'qualified'
except:
check_docker['14.允许Docker更改iptables'] = '未找到'

try:
f = os.popen('ps -ef | grep docker').read()
if 'insecure-registry' in f:
check_docker['15.使用安全的镜像库'] = 'qualified'
else:
check_docker['15.使用安全的镜像库'] = 'unqualified'
except:
check_docker['15.使用安全的镜像库'] = '未找到'

try:
# check_docker['12.审计/usr/bin/docker-runc'] = os.popen('auditctl -l | grep /usr/bin/docker-runc').read()
f_aufs = os.popen('docker info | grep -e "^Storage Driver:\s*aufs\s*$"').read()
if f_aufs == '':
check_docker['16.不使用aufs存储驱动'] = 'qualified'
else:
check_docker['16.不使用aufs存储驱动'] = 'unqualified'
except:
check_docker['16.不使用aufs存储驱动'] = '未找到'

try:
f_aufs = os.popen('ps -ef | grep docker').read()
if '--tlsverify' in f_aufs and '--tlscacert' in f_aufs and '--tlscert' in f_aufs and '--tlskey' in f_aufs:
check_docker['17.为Docker daemon配置TLS认证'] = 'qualified'
else:
check_docker['17.为Docker daemon配置TLS认证'] = 'unqualified'
except:
check_docker['17.为Docker daemon配置TLS认证'] = '未找到'

# try:
# check_docker['18.设置验证镜像库证书文件的权限'] = os.popen('stat -c %U:%G /etc/docker/certs.d/* | grep -v root:root').read()
# except:
# check_docker['18.设置验证镜像库证书文件的权限'] = '在' + '/etc/docker/certs.d/*' + '未找到'

# try:
# try:
# check_docker['19.设置验证TLS CA证书文件的权限'] = os.popen('stat -c %U:%G <path to TLS CA certificate file> | grep -v root:root').read()
# except:
# check_docker['19.设置验证TLS CA证书文件的权限'] = os.popen('stat -c %a <path to TLS CA certificate file>').read()
# except:
# check_docker['19.设置验证TLS CA证书文件的权限'] = 'not found'

# try:
# try:
# check_docker['20.设置验证Docker 服务器证书文件的权限'] = os.popen('stat -c %U:%G <path to Docker server certificate key file> | grep -v root:root').read()
# except:
# check_docker['20.设置验证Docker 服务器证书文件的权限'] = os.popen(
# 'stat -c %a <path to Docker server certificate key file>').read()
# except:
# check_docker['20.设置验证Docker 服务器证书文件的权限'] = 'not found'

with open('check_docker.txt', 'w') as file:
for v, k in check_docker.items():
file.write('{v}:{k}'.format(v=v, k=k))
file.write('\n')
~~~



### Linux安全基线

~~~powershell
cat <<EOF
*************************************************************************************
***** linux基线检查脚本 *****
*************************************************************************************
***** linux基线配置规范设计 *****
***** 输出结果/test.txt *****
*************************************************************************************
EOF

echo "***************************"
echo "空账号检测查询..."
echo "***************************"

passwd_null=`cat /etc/passwd | awk -F: '($2 == "") { print $1 }' /etc/password`

if [ !$passwd_null ];then
echo "不存在空密码用户符合要求" >> ./test.txt
else
echo "存在空密码用户，不符合要求,建议设置密码" >> ./test.txt
fi
#statements
#statements

echo "***************************"
echo "检查系统中是否存在UID与root帐户相同的帐户..."
echo "***************************"

UIDS=`awk -F[:] 'NR!=1{print $3}' /etc/passwd`
flag=0
for i in $UIDS
do
if [ $i = 0 ];then
echo "存在非root账号的账号UID为0，不符合要求" >> ./test.txt
else
flag=1
fi
done
if [ $flag = 1 ];then
echo "不存在非root账号的账号UID为0，符合要求" >> ./test.txt
fi



passmax=`cat /etc/login.defs | grep PASS_MAX_DAYS | grep -v ^# | awk '{print $2}'`
passmin=`cat /etc/login.defs | grep PASS_MIN_DAYS | grep -v ^# | awk '{print $2}'`
passlen=`cat /etc/login.defs | grep PASS_MIN_LEN | grep -v ^# | awk '{print $2}'`
passage=`cat /etc/login.defs | grep PASS_WARN_AGE | grep -v ^# | awk '{print $2}'`
echo "***************************"
echo "检查密码最小长度..."
echo "***************************"

if [ $passlen -ge 8 ];then
echo "口令最小长度为${passlen},符合要求" >> ./test.txt
else
echo "口令最小长度为${passlen},不符合要求，建议设置最小长度大于等于8" >> ./test.txt
fi


echo "***************************"
echo "检查密码过期时间..."
echo "***************************"

if [ $passmin -ge 6 ];then
echo "口令更改最小时间间隔为${passmin}天，符合要求" >> ./test.txt
else
echo "口令更改最小时间间隔为${passmin}天，不符合要求，建议设置大于等于6天" >> ./test.txt
fi


echo
if [ $passmax -le 90 -a $passmax -gt 0 ];then
echo "口令生存周期为${passmax}天，符合要求" >> ./test.txt
else
echo "口令生存周期为${passmax}天，不符合要求,建议设置不大于90天" >> ./test.txt
fi


echo "***************************"
echo "检查密码最大重试次数..."
echo "***************************"


passdeny=`cat /etc/pam.d/sshd | grep pam_tally2.so | grep -v ^# | awk -F '[= ]+' '{print $5}'`
#auth required pam_tally2.so deny=5 unlock_time=300 even_deny_root root_unlock_time=30
if [ !$passdeny ];then
if [ $passdeny -le 5 ];then
echo "密码最大重试次数为${passdeny}次，符合要求" >> ./test.txt
else
echo "密码最大重试次数为${passdeny}次，不符合要求，建议设置小于等于5次" >> ./test.txt
fi
echo "未设置密码最大重试次数，不符合要求，建议设置小于等于5次" >> ./test.txt
fi

echo "***************************"
echo "检查是否配置口令复杂度策略..."
echo "***************************"


complx=`cat /etc/security/user 2>/dev/null | egrep -i '^\s*min\w+\s*=\s*[0-9]+' | wc -l`

if [ ! -z $complx ] && [ $complx -ge 3 ]; then
echo "已配置口令复杂度，且复杂度符合要求" >> ./test.txt
else
echo "未配置口令复杂度或复杂度不符合要求" >> ./test.txt
fi


echo "***************************"
echo "检查密码重复使用次数限制..."
echo "***************************"

passreme=`cat /etc/pam.d/system-auth | grep pam_unix.so | grep -v ^# | awk -F '[= ]+' '{print $10}'`
#password sufficient pam_unix.so md5 shadow nullok try_first_pass use_authtok remember=5
if [ !$passreme ];then
if [ $passreme -ge 5 ];then
echo "密码重试次数为${passreme}次，符合要求" >> ./test.txt
else
echo "密码重试次数为${passreme}次，不符合要求，建议设置大于等于5次" >> ./test.txt
fi
else
echo "未设置密码重试次数，不符合要求，建议设置大于等于5次" >> ./test.txt
fi


echo "***************************"
echo "日志配置 检查是否开启日志功能..."
echo "***************************"

service auditd status
if [ $? = 0 ];then
echo "系统日志审核功能已开启，符合要求" >> ./test.txt
fi
if [ $? = 3 ];then
echo "系统日志审核功能已关闭，不符合要求，建议service auditd start开启" >> ./test.txt
fi




echo "***************************"
echo "日志配置 检查审计日志默认保存时间是否符合规范..."
echo "***************************"

#建议保存6个月内的日志
#若日志轮转周期设置为weekly（默认）则修改rotate值为27，
#同理若周期为daily则rotate设置为180，
#若周期为monthly则设置为6

#周期为weekly
passweek=`cat /etc/logrotate.conf|grep -v ^#|awk -F '[ ]' '{print $1}'|grep weekly`
rotatetime=`cat /etc/logrotate.conf|grep -v ^#|grep ^rotate|awk -F '[ ]' '{print $2}'`
#周期为daily
passdaily=`cat /etc/logrotate.conf|grep -v ^#|awk -F '[ ]' '{print $1}'|grep daily`
rotatetime=`cat /etc/logrotate.conf|grep -v ^#|grep ^rotate|awk -F '[ ]' '{print $2}'`

#周期为monthly
passmonthly=`cat /etc/logrotate.conf|grep -v ^#|awk -F '[ ]' '{print $1}'|grep monthly`
rotatetime=`cat /etc/logrotate.conf|grep -v ^#|grep ^rotate|awk -F '[ ]' '{print $2}'`

if [ !$passweek ];then
if [ $rotatetime -ge 27 ]; then
echo "日志保留时间${rotatetime}周 符合要求" >> ./test.txt
else
echo "日志保留时间${rotatetime}周 不符合要求，建议至少保留27周" >> ./test.txt
fi
elif [ !$passdaily ];then
if [ $rotatetime -ge 180 ]; then
echo "日志保留时间${rotatetime}天 符合要求" >> ./test.txt
else
echo "日志保留时间${rotatetime}天 不符合要求，建议至少保留180天" >> ./test.txt
fi
elif [ !$passmonthly ];then
if [ $rotatetime -ge 6 ]; then
echo "日志保留时间${rotatetime}月 符合要求" >> ./test.txt
else
echo "日志保留时间${rotatetime}月 不符合要求，建议至少保留6月" >> ./test.txt
fi
fi

echo "***************************"
echo "服务端口检查 检查是否启用SSH服务"
echo "***************************"

cat /etc/ssh/sshd_config | grep -v ^# |grep "PermitRootLogin no"
if [ $? -eq 0 ];then
echo "已经设置远程root不能登陆，符合要求" >> ./test.txt
else
echo "未设置远程root不能登陆，不符合要求，建议/etc/ssh/sshd_config添加PermitRootLogin no" >> ./test.txt
fi

echo "文件目录权限 ................"
echo "***************************"
echo "检查环境变量目录下是否存在权限为777的目录"
echo "***************************"

filenum=$(ls -l `echo $PATH | tr ":" " "` 2>/dev/null| grep [^d]rw[xsS]rw[xsS]rw[xsS] |wc -l)

if [ $filenum -eq 0 ];then
echo "环境变量下不存在权限777的文件，符合要求" >> ./test.txt
else
echo "环境变量下存在权限777的文件，不符合要求，建议'chmod + 相应权限 + 文件/目录'修改文件权限" >> ./test.txt
fi


echo "***************************"
echo "检查是否存在权限不安全的重要日志文件"
echo "***************************"

file1=`ls -l /var/log/boot.log | awk '{print $1}'`
#file2=`ls -l /var/log/maillog | awk '{print $1}'`
#file3=`ls -l /var/log/wtmp | awk '{print $1}'`
#file4=`ls -l /var/log/Utmp | awk '{print $1}'`
#file5=`ls -l /var/log/xferlog | awk '{print $1}'`
if [ $file1 = "-rw-r-----" ];then
echo "/var/log/boot.log文件权限为640，符合要求" >> ./test.txt
else
echo "/var/log/boot.log文件权限不为640，不符合要求，建议设置权限为640" >> ./test.txt
fi

echo "***************************"
echo "检查系统当前的umask"
echo "***************************"

file1=`umask`
echo $file1
if [ $file1 = 0022 ]; then
echo "umask为0022，符合要求" >> ./test.txt
else
echo "umask为${file1}，不符合要求" >> ./test.txt
fi


echo "***************************"
echo "检查系统是否允许root帐户ssh远程登录"
echo "***************************"

cat /etc/ssh/sshd_config | grep -v ^# |grep "PermitRootLogin no"
if [ $? -eq 0 ];then
echo "已经设置远程root不能登陆，符合要求" >> ./test.txt
else
echo "不已经设置远程root不能登陆，不符合要求，建议/etc/ssh/sshd_config添加PermitRootLogin no" >> ./test.txt
fi



echo "***************************"
echo "检查登录超时锁定时间"
echo "***************************"



TMOUT=`cat /etc/profile | grep TMOUT | awk -F[=] '{print $2}'`
if [ $TMOUT ];then
if [ $TMOUT -le 600 ];then
echo "账号超时时间${TMOUT}秒,符合要求" >> ./test.txt
else
echo "账号超时时间${TMOUT}秒,不符合要求，建议设置小于600秒" >> ./test.txt
fi
else
echo "账号超时不存在自动注销,不符合要求，建议设置小于600秒" >> ./test.txt
fi

echo "***************************"
echo "检查root用户的PATH环境变量是否包含相对路径"
echo "***************************"

path=`cat /etc/profile|grep -E "\.\/|\.\.\/"`
if [ !path ];then
echo "不包含相对路径，符合要求" >> ./test.txt
else
echo "包含相对路径，不符合要求" >> ./test.txt
fi

echo "***************************"
echo "检查启用系统core dump设置"
echo "***************************"

cat /etc/security/limits.conf | grep -V ^# | grep core
if [ $? -eq 0 ];then
soft=`cat /etc/security/limits.conf | grep -V ^# | grep core | awk {print $2}`
for i in $soft
do
if [ $i = "soft" ];then
echo "* soft core 0 已经设置" >> ./test.txt
fi
if [ $i = "hard" ];then
echo "* hard core 0 已经设置" >> ./test.txt
fi
done
else
echo "没有设置core，建议在/etc/security/limits.conf中添加* soft core 0和* hard core 0" >> ./test.txt
fi



echo "***************************"
echo "检查是否修改snmp默认团体字"
echo "***************************"

if [ -f /etc/snmp/snmpd.conf ];then
public=`cat /etc/snmp/snmpd.conf | grep public | grep -v ^# | awk '{print $4}'`
private=`cat /etc/snmp/snmpd.conf | grep private | grep -v ^# | awk '{print $4}'`
if [ $public = "public" ];then
echo "发现snmp服务存在默认团体名public,不符合要求" >> ./test.txt
fi
if [[ $private = "private" ]];then
echo "发现snmp服务存在默认团体名private,不符合要求" >> ./test.txt
fi
else
echo "snmp服务配置文件不存在，可能没有运行snmp服务"
fi
~~~

### Mysql数据库数据恢复

一、背景：数据文件格式

1.     .frm文件：存储数据表的框架结构，文件名与表名相同，每个表对应一个同名frm文件，
       与操作系统和存储引擎无关，即不管MySQL运行在何种操作系统上，使用何种存储引擎，都有这个文件。
       除了必有的.frm文件，根据MySQL所使用的存储引擎的不同（MySQL常用的两个存储引擎是MyISAM和InnoDB），存储引擎会创建各自不同的数据库文件。
2.     MyISAM数据库表文件：
       .MYD文件：即MY Data，表数据文件，存储密码（加密前或者加密后）
       .MYI文件：即MY Index，索引文件
3.     InnoDB采用表空间（tablespace）来管理数据，存储表数据和索引，
       InnoDB数据库文件（即InnoDB文件集，ib-file set）：
       ibdata1、ibdata2等：系统表空间文件，存储InnoDB系统信息和用户数据库表数据和索引，所有表共用
       .ibd文件：单表表空间文件，每个表使用一个表空间文件（file per
       table），存放用户数据库表数据和索引

二、数据恢复
1.从SQL文件中恢复
方法一：Mysql导入
(1). 首先进入mysql，创建好要导入的数据库
​      C:\mysql\bin>mysql -u root -p
​      mysql> create database mysql_test;
(2). 在cmd下执行命令  mysql -u root -p mysql_test < mysql_test_20190227.txt
​      //将mysql_test_20190227.txt里的SQL语句执行到 mysql_test数据库中

 方法二：source命令导入
(1).首先进入mysql，创建好要导入的数据库
​     C:\mysql\bin>mysql -u root -p
​      mysql> create database mysql_test;
(2). 导入备份数据库
mysql> use mysql_test;
mysql> source mysql_test_20190227.txt;

2.从数据库文件恢复（分以下3中情况）
(1)数据引擎为MYISAM
只要将相应.frm、 .MYD、.MYI文件拷贝到data\对应数据库目录下即可。重启数据库会发现多了一个数据库(db.opt文件标明数据库使用的字符集)

(2)数据引擎为Innodb，有idbdata1文件（共用表空间）
​        a) 用net stop mysql  //停止mysql服务
​        b) 将要恢复的主机上的数据库文件夹和idbdata1文件拷贝到我们的主机上（会替换掉我们本地的idbdata1文件）
​        c)用net start mysql  //开始mysql服务
​        d)连接mysql即可看到恢复出来的数据

(3)数据引擎为Innodb，无idbdata1文件时，只有frm和ibd文件
​        a)打开MySQL Utilities安装目录，执行以下命令，命令行会打印出创建表的sql语句
​                mysqlfrm --diagnostic C:\mysql\data\mysql_test\user.frm
​        b)连接mysql  创建对应的数据库，并设置默认存储引擎为Innodb,在对应数据库中执行刚才命令行中显示的创建表的SQL语句，执行完以后对应数据库文件              夹下就会有.frm和.ibd文件
​            set default_storage_engine=Innodb;
​        c) 登陆数据库执行以下语句：
​            ALTER TABLE `mysql_test`.`user` DISCARD TABLESPACE; //执行完以后就只有.frm文件
​        d)把备份的*.ibd文件拷贝到data目录下的数据库文件夹下；登陆数据库然后执行以下命令：
​            ALTER TABLE `mysql_test`.`user` IMPORT TABLESPACE;//执行完以后就可以查看表里的数据了

### Windows安全基线

~~~powershell
@echo off&setlocal enabledelayedexpansion
rem %1 mshta vbscript:CreateObject("Shell.Application").ShellExecute("cmd.exe","/c %~s0 ::","","runas",1)(window.close)&&exit
cd /d "%~dp0"
title windows基线检查 日期：%date% %time%
rem mode con cols=500 lines=9999
Rem '/*========获取本机的IP地址=========*/
for /f "tokens=2 delims=:" %%b in ('ipconfig^|findstr /i "ip"') do set IP=%%b
echo 本机IP是： %IP%
echo 当前时间是： %date% %time%
systeminfo
echo.
echo.
echo #####查看账户net user#####
echo.
net user
echo.
echo.
echo ########查看用户组########
echo.
net Localgroup
echo.
echo.
echo ########查看admin组#######
echo.
Net Localgroup administrators 2>1
Net Localgroup superman 2>1
Rem '/*========获取策略组文件=========*/
secedit /export /cfg secedit.inf /quiet
echo.
echo.
echo #########密码策略#########
echo.
for /f "delims=" %%i in ('type secedit.inf^|findstr /i "MinimumPasswordAge"') do echo 密码最短使用期限: %%i
for /f "delims=" %%i in ('type secedit.inf^|findstr /i "MaximumPasswordAge"') do (echo 密码最长使用期限: %%i
goto pc)
:pc
for /f "delims=" %%i in ('type secedit.inf^|findstr /i "MinimumPasswordLength"') do echo 密码长度最小值: %%i
for /f "delims=" %%i in ('type secedit.inf^|findstr /i "PasswordComplexity"') do echo 密码必需符合复杂性要求: %%i
for /f "delims=" %%i in ('type secedit.inf^|findstr /i "PasswordHistorySize"') do echo 强制密码历史: %%i
for /f "delims=" %%i in ('type secedit.inf^|findstr /i "ClearTextPassword"') do echo 用可还原的加密来储存密码: %%i
for /f "delims=" %%i in ('type secedit.inf^|findstr /i "RequireLogonToChangePassword"') do echo 下次登陆必需更改密码: %%i
for /f "delims=" %%i in ('type secedit.inf^|findstr /i "ForceLogoffWhenHourExpire"') do echo 强制密码过期: %%i
echo.
echo.
echo #######帐户锁定策略#######
echo.
for /f "delims=" %%i in ('type secedit.inf^|findstr /i "LockoutBadCount"') do echo 帐户锁定阀值: %%i
for /f "delims=" %%i in ('type secedit.inf^|findstr /i "ResetLockoutCount"') do echo 帐户锁定时间: %%i
for /f "delims=" %%i in ('type secedit.inf^|findstr /i "LockoutDuration"') do echo 重置账户锁定计数器: %%i
echo.
echo.
echo #######net accounts#######
echo.
net accounts
echo.
echo.
echo #########审核策略#########
echo.
echo 注：参数0未审核；参数1成功；参数2失败；参数3成功、失败
echo.
for /f "delims=" %%i in ('type secedit.inf^|findstr /i "AuditPolicyChange"') do echo 审核策略更改: %%i
for /f "delims=" %%i in ('type secedit.inf^|findstr /i "AuditLogonEvents"') do echo 审核登录事件: %%i
for /f "delims=" %%i in ('type secedit.inf^|findstr /i "AuditObjectAccess"') do echo 审核对象访问: %%i
for /f "delims=" %%i in ('type secedit.inf^|findstr /i "AuditProcessTracking"') do echo 审核进程跟踪: %%i
for /f "delims=" %%i in ('type secedit.inf^|findstr /i "AuditDSAccess"') do echo 审核目录服务访问: %%i
for /f "delims=" %%i in ('type secedit.inf^|findstr /i "AuditPrivilegeUse"') do echo 审核特权使用: %%i
for /f "delims=" %%i in ('type secedit.inf^|findstr /i "AuditSystemEvents"') do echo 审核系统事件: %%i
for /f "delims=" %%i in ('type secedit.inf^|findstr /i "AuditLogonEvents"') do echo 审核帐户登录事件: %%i
for /f "delims=" %%i in ('type secedit.inf^|findstr /i "AuditAccountManage"') do echo 审核帐户管理: %%i
echo.
echo.
echo #########安全选项#########
echo.
for /f "delims=" %%i in ('type secedit.inf^|findstr /i "NewAdministratorName"') do echo 帐户:重命名系统管理员帐户: %%i
for /f "delims=" %%i in ('type secedit.inf^|findstr /i "EnableAdminAccount"') do echo 帐户：管理员帐户状态: %%i
for /f "delims=" %%i in ('type secedit.inf^|findstr /i "EnableGuestAccount"') do echo 帐户：来宾帐户状态: %%i #0 已禁用；1已启用
for /f "tokens=8 delims=\" %%i in ('type secedit.inf^|findstr /i "DontDisplayLastUserName"') do echo 交互式登录:登录时不显示用户名: %%i #4,0 没有定义；4,1 已启用
for /f "tokens=7 delims=\" %%i in ('type secedit.inf^|findstr /i "ClearPageFileAtShutdown"') do echo 关机:清除虚拟内存页面文件: %%i #4,0 已禁用；4,1 已启用
echo.
echo.
echo #########检查屏保#########
echo.
echo /*------------------------------------------------------------------------------------*/
echo /*------此处查询ScreenSaveActive、ScreenSaveTimeOut、ScreenSaverIsSecure三个参数------*/
echo /*------ ------*/
echo /*---------------------若仅有ScreenSaveActive，则屏保功能未开启。---------------------*/
echo /*------------------------------------------------------------------------------------*/
echo.
for /f "tokens=3 delims= " %%i in ('reg query "HKEY_CURRENT_USER\Control Panel\Desktop" /v ScreenSaveActive^|findstr /i "ScreenSaveActive"') do echo 屏幕保护程序: ScreenSaveActive = %%i #参数1 已开启；参数0 未开启
for /f "tokens=3 delims= " %%i in ('reg query "HKEY_CURRENT_USER\Control Panel\Desktop" /v ScreenSaveTimeOut^|findstr /i "ScreenSaveTimeOut"') do echo 屏保时间: ScreenSaveTimeOut = %%i 秒
for /f "tokens=3 delims= " %%i in ('reg query "HKEY_CURRENT_USER\Control Panel\Desktop" /v ScreenSaverIsSecure^|findstr /i "ScreenSaverIsSecure"') do echo 在恢复时显示登录屏幕: ScreenSaverIsSecure = %%i #参数1 需口令；参数0 不需口令
echo.
echo.
echo ########远程端口号########
echo.
for /f "tokens=3 delims= " %%i in ('REG query HKLM\SYSTEM\CurrentControlSet\Control\Terminal" "Server\WinStations\RDP-Tcp /v PortNumber^|findstr /i "PortNumber"') do echo 远程管理端口号: PortNumber = %%i #远程管理端口号，默认0xd3d
echo.
echo.
echo ########已安装软件########
echo.
wmic product get name,version
echo.
echo.
echo #########默认共享#########
echo.
net share
echo.
echo.
echo ########检查防火墙########
echo.
echo ------------------netsh advfirewall show allprofiles------------------
netsh advfirewall show allprofiles
echo.
echo.
echo #########检查服务#########
echo.
net start
echo.
echo.
echo #########检查端口#########
echo.
netstat -ano
echo.
echo.
echo ##########检查IP##########
echo.
ipconfig
echo.
echo.
echo #########文件权限#########
echo.
cacls c:\
cacls c:\windows\system32\cmd.exe
cacls C:\Progra*
cacls c:\windows\system32
cacls C:\Windows\system32\config
cacls c:\windows\SysWOW64
cacls C:\Windows\SysWOW64\config
echo.
echo.
echo #########查看进程#########
echo.
tasklist
echo.
echo.
echo #########硬盘使用#########
echo.
WMIC LOGICALDISK WHERE MEDIATYPE='12' GET DESCRIPTION,DEVICEID,FILESYSTEM,SIZE,FREESPACE
echo.
echo.
echo #########硬盘配额#########
echo.
fsutil quota query c:
echo.
echo.
echo #########查注册表#########
echo.
reg query "HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\Crashcontrol" |find /i "AutoReboot" 2>1
reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" |find /i "NoDriveTypeAutoRun" 2>1
REG query HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\EventLog\Application /v MaxSize 2>1
REG query HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\EventLog\System /v MaxSize 2>1
REG query HKEY_LOCAL_MACHINE\system\CurrentControlSet\Services\EventLog\Security /v maxsize 2>1
reg query HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System /v dontdisplaylastusername 2>1
reg query "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management" /v ClearPageFileAtShutdown 2>1
reg query HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall /v displayname /s 2>1
reg query HKLM\SYSTEM\CurrentControlSet\Control\LSA /v RestrictAnonymousSAM 2>1
reg query HKLM\SYSTEM\CurrentControlSet\Control\LSA /v RestrictAnonymous 2>1
reg query HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile /v EnableFirewall 2>1
reg query HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\FirewallRules /v RemoteDesktop-In-TCP ^|find /i "true" 2>1
reg query HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\FirewallRules /v RemoteDesktop-In-TCP ^|find /i "true" 2>1
REG query HKLM\SYSTEM\CurrentControlSet\Control\Terminal" "Server\WinStations\RDP-Tcp /v PortNumber 2>1
reg query "HKEY_CURRENT_USER\Control Panel\Desktop" /v ScreenSaverIsSecure 2>1
reg query "HKEY_CURRENT_USER\Control Panel\Desktop" /v ScreenSaveTimeOut 2>1
REG query HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run /s 2>1
REG query HKEY_local_machine\Software\Microsoft\Windows\CurrentVersion\Run /s 2>1
reg query HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer /s 2>1
reg query HKEY_current_user\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer /s 2>1
reg query "HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon" |find /i "AutoAdminLogon" 2>1
reg query "HKLM\System\CurrentControlSet\Services\Tcpip\Parameters" |find /i "SynAttackProtect" 2>1
reg query "HKLM\System\CurrentControlSet\Services\Tcpip\Parameters" |find /i "EnableDeadGWDetect" 2>1
reg query "HKLM\System\CurrentControlSet\Services\Tcpip\Parameters" |find /i "KeepAliveTime" 2>1
reg query "HKLM\System\CurrentControlSet\Services\Tcpip\Parameters" |find /i "NoNameReleaseOnDemand" 2>1
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Wds\rdpwd\Tds\tcp" |find /i "PortNumber" 2>1
reg query "HKLM\SYSTEM\CurrentContro1Set\Control\Terminal Server\WinStations\RDP-Tcp" 2>1 |find /i "PortNumber"
reg query "HKLM\System\CurrentControlSet\Services\Tcpip\Parameters" 2>1 |find /i "DefaultTTL"
echo.
echo.
echo ##########组策略##########
echo.
type secedit.inf
rem /*------删除临时文件------*/
del 1
del secedit.inf
echo.
echo.
echo.
echo #########检查结束#########
exit
pause
~~~



## 安全加固

| 加固项目    | 加固目的                               |
| ------- | ---------------------------------- |
| 互联网资产扫描 | 发现未知资产和风险端口                        |
| 互联网漏洞信息 | 搜索引擎 、代码托管 、网盘、漏洞平台                |
| 安全基线检查  | 网络、安全、主机、应用等安全基线检查                 |
| 安全扫描    | 主机、应用安全漏洞扫描+验证                     |
| 渗透测试    | 漏洞验证、督促整改                          |
| 其他安全检查  | 运维终端安全检查、日志审计、备份有效性                |
| 安全意识培训  | 关键岗位运维人员、管理人员安全意识                  |
| 安全加固    | 针对发现的安全漏洞、安全隐患及时跟进、验证修复情况          |
| 安全风险检查  | 弱口令、管理后台外部访问权限、内部访问IP范围、补丁更新、上传目录权 |

1. 对APT设备、AiLPHA大数据平台等等可以开启钉钉提示功能，把对应的高危、中危以及低危险漏洞推送到钉钉消息上面来。
2. 对外网攻击ip及时进行封堵，以免造成让攻击者对系统造成更严重的危害，所封IP要做上记录，发现外网攻击者攻击成功了的可以结合对应的平台写相应的溯源报告。以及社工库
3. 从日志中发现攻击者已经突破外网进入内外，要采取紧急措施关闭服务器，防止攻击者进行横向渗透，拿下内网更多的资产，导致直接出具。攻击者在内网的横向渗透，日志里面不容易发现，和系统自身的日志混杂在一起就更不容易对其进行溯源了。





## 常规WEB安全配置信息和弱口令

###### 一.默认管理后台：

Tomcat默认管理后台：http://www.xxx.com/manager/html
常用弱口令：tomcat/tomcat、admin/tomcat、tomcat/admin等
Resin3默认管理后台：https://ip/resin-admin/
Resin4默认管理后台：https://ip:8443/resin-admin/
Weblogic10g-12c默认的管理后台是：http://localhost:7001/console
Weblogic10 以下默认后台地址是：http://localhost:7001/console/login/LoginForm.jsp
默认口令：weblogic/weblogic
Websphere管理后台：https://localhost:9043/ibm/console/logon.jsp
Jboss默认管理后台：http://localhost:8080/jmx-console
常用弱口令：admin/admin
Apache mq 默认管理后台：http://localhost:8161/admin/
默认口令：admin/admin

###### 二、默认控制台

Tomcat：http://IP:8080
WebLogic：http://IP:7001/console
WebSphere：http://IP:9060/ibm/console；http://IP:9060/ibm/console
Jboss：http://IP:8080/jmx-console

###### 三、控制台默认口令

admin/admin
tomcat/tomcat
admin/password
weblogic/weblogic
weblogic/weblogic123
system/system
portaladmin/portaladmin
guest/guest

###### 四、样例文件

Apache Tomcat默认安装包含”/examples”目录，尝试访问http://IP:8080/examples
五、默认日志路径：
1）IIS日志路径：C:\WINDOWS\system32\Logfiles
2）Apache日志路径：
Windows: \logs\access.log、\logs\error.log
Linux: /usr/local/apache/logs/access_log、/usr/local/apache/logs/error_log
3）Nginx日志路径：/usr/local/nginx/logs
在nginx.conf文件中可指定。
4）Tomcat日志，tomcat默认不记录日志，查看其配置文件${catalina}/conf/server.xml，
${catalina} 是 tomcat 的安装目录，server.xml文件中可开启日志，并设置日志路径。
5）Jboss日志默认未开启用户访问日志, 查看其配置文件，可开启日志，并设置日志路径。
${JBOSS_HOME}/server/defaul/deploy/jbossweb.deployer/server.xml
如果不是默认安装，可通过web服务配置文件中获取。











