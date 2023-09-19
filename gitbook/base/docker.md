### Docker

从2点半配到5点，下docker desktop for windows最新版本就下了20+分钟，然后下载hype-v，更改docker的默认安装地址，再之后wsl2，发现docker desktop settings打不开，找解决方法无果，遂尝试在我的华为云服务器下载，失败（提示）

`error: http://mirrors.neusoft.edu.cn/kali kali-rolling InRelease: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY ED444FF07D8D0BF6`

然后尝试在虚拟机中下载，本来都要放弃了，最后成功了...

#### 基本命令

###### 启动

systemctl start docker

###### 查看状态

systemctl status docker

docker version

[![IFrVQe.png](https://z3.ax1x.com/2021/11/02/IFrVQe.png)](https://imgtu.com/i/IFrVQe)

###### 测试

这里ubuntu:15.10 	/bin/echo	前者是选择运行的镜像环境，后者在启动的容器里执行的命令

docker run (ubuntu:15.10 /bin/echo) hello-world

###### 交互式的容器

docker run -i -t ubuntu:15.10 /bin/bash	这里-t是创建一个虚拟终端，-i是进行交互

使用exit或者ctrl+d退出

###### 登录/登出

docker login -u 用户名 -p 密码	/	docker logout

###### 创建容器

docker container run nginx

###### 查看容器（启动的）

docker container ls		or		docker container ps

###### 查看容器（所有）

docker container ls -a		or		docker container ps -a

###### 停止容器

docker container stop nginx / 94（ID）

###### 删除容器

docker container rm 94（ID）

###### 查看容器ID

docker container ps -aq

###### 获取镜像

docker pull ubuntu		 docker run -it ubuntu /bin/bash	运行并进入

###### 组合命令

docker container stop $(docker container ps -aq)

###### 强制删除容器

docker container rm 94(ID) -f 

###### 端口映射

docker container run -p 80:80 nignx

[![IF4xyt.png](https://z3.ax1x.com/2021/11/02/IF4xyt.png)](https://imgtu.com/i/IF4xyt)

docker container run -d -p 80:80 nginx

这里-d是后台运行

docker attach 6bf(ID)			或者	docker exec	ID

作用是再把后台的放到前台

###### 重启

docker restart

###### 导出和导入容器

docker export 1e560fca3906 > ubuntu.tar

可以使用 docker import 从容器快照文件中再导入为镜像，以下实例将快照文件 ubuntu.tar 导入到镜像 test/ubuntu:v1

 cat docker/ubuntu.tar | docker import - test/ubuntu:v1

也可以通过指定 URL 或者某个目录来导入

docker import http://example.com/exampleimage.tgz example/imagerepo

###### 运行一个web应用

docker pull training/webapp  # 载入镜像

docker run -d -P training/webapp python app.py

###### 查看 WEB 应用程序日志

docker logs -f bf08b7f2cd89

###### 查看WEB应用程序容器的进程

docker top wizardly_chandrasekhar

###### 停止web应用

docker stop wizardly_chandrasekhar

###### 重启WEB应用容器

docker start wizardly_chandrasekhar

###### 移除WEB应用容器

docker rm wizardly_chandrasekhar

列出镜像列表

docker images

查找镜像

docker search httpd

构建镜像

我们使用命令 **docker build** ， 从零开始来创建一个新的镜像。为此，我们需要创建一个 Dockerfile 文件，其中包含一组指令来告诉 Docker 如何构建我们的镜像。

docker build -t runoob/centos:6.7 

-t ：指定要创建的目标镜像名

. ：Dockerfile 文件所在目录，可以指定Dockerfile 的绝对路径

###### 设置镜像标签

docker tag 860c279d2fec runoob/centos:dev

~~~shell
runoob@runoob:~$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
runoob/centos       6.7                 860c279d2fec        5 hours ago         190.6 MB
runoob/centos       dev                 860c279d2fec        5 hours ago         190.6 MB
runoob/ubuntu       v2                  70bf1840fd7c        22 hours ago        158.5 MB
~~~

#### dockerfile

~~~shell
FROM nginx		# 这里from nginx引入nginx镜像
RUN echo '这是一个本地构建的nginx镜像' > /usr/share/nginx/html/index.html	# run运行echo输出一句话到该index.html文件
~~~

Dockerfile 的指令每执行一次都会在 docker 上新建一层。所以过多无意义的层，会造成镜像膨胀过大，以 && 符号连接命令，这样执行后，只会创建 1 层镜像

#### compose

Compose 是用于定义和运行多容器 Docker 应用程序的工具。通过 Compose，您可以使用 YML 文件来配置应用程序需要的所有服务。然后，使用一个命令，就可以从 YML 文件配置中创建并启动所有服务

docker-compose up -d

docker-compose down

参考链接：

https://www.runoob.com/docker/docker-tutorial.html

https://github.com.cnpmjs.org/jaywcjlove/docker-tutorial
