##### ftp

21端口

~~~
ftp 127.0.0.1
get flag.txt
~~~

##### smb

~~~
smbclient -L 127.0.0.1

        ADMIN$          Disk      Remote Admin
        backups         Disk      
        C$              Disk      Default share
        IPC$            IPC       Remote IPC

这里带美元符号的是管理员权限，这里我们使用-N符号连接backups，发现有一个dtsConfig文件，这个文件是SSIS的配置文件，SSIS是SQL Server Integration Services，先get下来

smbclient -N \\\\127.0.0.1\\backups
~~~



##### telnet



##### mssql

1433端口

~~~shell
#	impacket-mssqlclient
#	这里ARCHETYPE/sql_svc是用户名，M3g4c0rp123是密码,10.129.201.51是对方的IP地址
mssqlclient.exe ARCHETYPE/sql_svc:M3g4c0rp123@10.129.201.51 -windows-auth
#	这里cmd就是你执行的命令
xp_cmdshell {cmd}

~~~

##### mysql

~~~
~~~

##### ssh

~~~
ssh -p 22 root@127.0.0.1
~~~

