

~~~
注册表开机自启动路径

[HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run]

[HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run]

隐藏
[HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Policies\Explorer\Run]

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer\Run]
~~~



c语言命令执行

~~~http
#include "stdlib.h"

int main(){
    system("pause");
    system("ipconfig /all");
    system("pause");
}
~~~

