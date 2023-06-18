### 主页劫持

原理：主页被劫持的原理是一段通过WMI发起的定时自动运行脚本，WMI（Windows Management Instrumentation）可以理解成Windows系统后台运行的一个事件管理器。

为查看WMI事件，先去下载WMITools并安装：WMI工具

链接： <https://share.weiyun.com/51AShol> （密码：6666）

安装完成之后打开WMI Event Viewer ，点击左上角的笔的图标（Register For Events）

[![bTBk8A.png](https://s1.ax1x.com/2022/03/12/bTBk8A.png)](https://imgtu.com/i/bTBk8A)

这里我们点OK

[![bTBEvt.png](https://s1.ax1x.com/2022/03/12/bTBEvt.png)](https://imgtu.com/i/bTBEvt)

点开左侧栏的EventFilter，再点击下级目录的项目

[![bTBZKP.png](https://s1.ax1x.com/2022/03/12/bTBZKP.png)](https://imgtu.com/i/bTBZKP)

在右侧栏右键点击ActiveScriptEventConsumer，并通过view instant properties查看属性

[![bTBmb8.png](https://s1.ax1x.com/2022/03/12/bTBmb8.png)](https://imgtu.com/i/bTBmb8)

在Script Text那一栏我们可以看到这段脚本

[![bTBuVS.png](https://s1.ax1x.com/2022/03/12/bTBuVS.png)](https://imgtu.com/i/bTBuVS)

终于抓到了幕后黑手。可以看到这是一段VBScript代码，攻击目标涵盖了包括Chrome、360、Firefox、搜狗等30余种常见的浏览器。脚本以浏览器的安装地址为切入点，创建WshShell对象，进而生成植入了流氓网站的快捷方式。360浏览器有限定主页格式，于是这段脚本还特地修饰了流氓网站的链接

[![bTBKUg.png](https://s1.ax1x.com/2022/03/12/bTBKUg.png)](https://imgtu.com/i/bTBKUg)

[![bTBYrV.png](https://s1.ax1x.com/2022/03/12/bTBYrV.png)](https://imgtu.com/i/bTBYrV)

然后我们来删除它，这里记得要给管理员权限才能删除它

[![bTBUVU.png](https://s1.ax1x.com/2022/03/12/bTBUVU.png)](https://imgtu.com/i/bTBUVU)

之后还要把各个快捷方式都改回不带流氓网站的版本，包括桌面上的、开始菜单里的以及快速访问栏里的快捷方式

[![bTBtbT.png](https://s1.ax1x.com/2022/03/12/bTBtbT.png)](https://imgtu.com/i/bTBtbT)





















