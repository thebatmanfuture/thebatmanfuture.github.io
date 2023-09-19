#### 用户，组管理

用户SID：S-1-5-21-426206823-2579496042-14852678-500

系统SID：S-1-5-21-426206823-2579496042-14852678

• 用户UID：500 

• windows系统管理员administrator的UID是500 

• 普通用户的UID是1000开始

查看sid值：whoami /user

账户密码存储位置：c:\windows\system32\confifig\SAM 

~~~
用户管理命令

net user #查看用户列表 
net user 用户名 密码 #改密码 
net user 用户名 密码 /add #创建一个新用户 
net user 用户名 /del #
删除一个用户 net user 用户名 /active:yes/no #激活或禁用账户

内置组

1）administrators # 管理员组 
2）guests # 来宾组 
3）users # 普通用户组，默认新建用户都属于该组 
4）network # 网络配置组 
5）print # 打印机组 
6）Remote Desktop # 远程桌面组

组管理命令

net localgroup # 查看组列表
net localgroup 组名 # 查看该组的成员 
net localgroup 组名 /add # 创建一个新的组 
net localgroup 组名 用户名 /add # 添加用户到组 
net localgroup 组名 用户名 /del # 从组中踢出用户 
net localgroup 组名 /del # 删除组
~~~

#### 服务器远程管理


windows远程管理有2种类型：

1、远程桌面（图形）

2、telnet（命令行）

netstat -an
查看本机开放的所有端口
telnet：23
远程桌面协议RDP：3389

#### 文件系统

•FAT windows 

•NTFS windows 

•EXT linux常见

#### 文件共享服务器

在开始运行/或我的电脑地址栏中，输入UNC地址： 

\\文件共享服务器IP 

\\文件共享服务器IP\共享名

~~~
net share #列出共享列表 
net share 共享名 /del #删除共享
~~~

#### DHCP部署与安全

地址池/作用域：（IP、子网掩码、网关、DNS、租期），DHCP协议端口是UDP 67/68

1）客户机发送DHCP Discovery广播包

客户机广播请求IP地址（包含客户机的MAC地址）

2）服务器响应DHCP Offffer广播包

服务器响应提供的IP地址（但无子网掩码、网关等参数）

3）客户机发送DHCP Request广播包

客户机选择IP（也可认为确认使用哪个IP） 

4）服务器发送DHCP ACK广播包

服务器确定了租约，并提供网卡详细参数IP、掩码、网关、DNS、租期等

~~~
ipconfig /release 释放IP（取消租约，或者改为手动配置IP，也可以释放租约） 
ipconfig /renew 重新获取IP（有IP时，发送request续约，无IP时发送Discovery重新获取IP）
~~~

#### DNS

53端口

~~~
ipconfig /flushdns
~~~



























