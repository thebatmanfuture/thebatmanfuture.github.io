#### ldifde

适用场景：内网环境有域环境，掌握一个域用户的账号密码

作用：能够把域里面的所有信息导出来（域用户域机器等详细信息）

##### 使用

~~~
ldifde.exe -f 导出的文件名 -s 域控IP -b 普通域用户名 域名 普通域用户密码

ldifde.exe -f out.ldf -s 192.168.52.2 -b iis_user hack 1qaz@WSX
~~~

![](https://s3.bmp.ovh/imgs/2022/03/2dce84c9fbc053de.png)

![](https://s3.bmp.ovh/imgs/2022/03/b53e7742b718f931.png)

##### 筛选

###### 域用户：

![](https://s3.bmp.ovh/imgs/2022/03/e66a8de6d9781a44.png)

域机器：

![](https://s3.bmp.ovh/imgs/2022/03/dcecba474d998370.png)

还有各种详细的信息，可以自行深入研究

比如所在组，上次登陆时间等

![](https://s3.bmp.ovh/imgs/2022/03/286879f69124fbd2.png)

































































