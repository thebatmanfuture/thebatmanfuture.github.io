web安全进阶：

对各种web漏洞的原理，防御方式，武器化poc和exp，编写代码复现

https://www.yuque.com/tidesec/codeaudit/sql_sql_wj

[![bbgdVs.png](https://s1.ax1x.com/2022/03/13/bbgdVs.png)](https://imgtu.com/i/bbgdVs)

[![bbgWZ9.jpg](https://s1.ax1x.com/2022/03/13/bbgWZ9.jpg)](https://imgtu.com/i/bbgWZ9)

[![bbg2qJ.jpg](https://s1.ax1x.com/2022/03/13/bbg2qJ.jpg)](https://imgtu.com/i/bbg2qJ)





浏览器发送数据，服务器接收数据响应过程

[![HmUE24.png](https://s4.ax1x.com/2022/02/05/HmUE24.png)](https://imgtu.com/i/HmUE24)

[![HmUeM9.png](https://s4.ax1x.com/2022/02/05/HmUeM9.png)](https://imgtu.com/i/HmUeM9)

![obGZY4.jpg](https://s4.ax1x.com/2021/12/12/obGZY4.jpg)

[![oq839J.png](https://s4.ax1x.com/2021/12/12/oq839J.png)](https://imgtu.com/i/oq839J)

[![butMIe.png](https://s4.ax1x.com/2022/02/28/butMIe.png)](https://imgtu.com/i/butMIe)

[![bGnPr6.png](https://s4.ax1x.com/2022/03/02/bGnPr6.png)](https://imgtu.com/i/bGnPr6)

##### 本地host文件地址

C:\Windows\System32\drivers\etc\hosts

##### 系统自启动文件地址

C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp

##### 指定自启动用户文件地址

C:\Users\batma\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup

##### SAM文件地址

C:\Windows\System32\config\sam

##### 常见组合：

```
asp+access或者iis（windows上运行）
php+mysql
aspx+mssql（windows上运行）
jsp+mssql或oracle
python+mongodb
```

##### CDN绕过

###### cdn判断：多地点ping

###### 绕过

1. 子域名，邮箱服务器：可以扫网段，如果大部分都一样可能就是真实ip地址；有专门地址邮件查ip

   比如m.baidu.com，www.baidu.com

2. 国外地址请求：国外ping

3. 遗留文件，扫描全网：比如phpinfo.php

![](https://s4.ax1x.com/2021/12/12/oqANvQ.png)

1. 黑暗搜索引擎搜索特定文件：shodan，fofa，zoomeye搜索ico图标文件，或者特殊js文件
2. dns历史记录，以量打量：ip反查域名网站；ddos把对面流量打光，就会访问真实ip地址






{"code":200,"msg":"ok","data":{"result":"åéæå"}}














