[![bGZOyV.jpg](https://s4.ax1x.com/2022/03/02/bGZOyV.jpg)](https://imgtu.com/i/bGZOyV)

###### git的基本使用

Git 常用的是以下 6 个命令：**git clone**、**git push**、**git add** 、**git commit**、**git checkout**、**git pull**。

![](https://z3.ax1x.com/2021/10/21/5yVPzt.png)

![](https://z3.ax1x.com/2021/10/21/5ymywT.png)



```shell
# git init
初始化一个 Git 仓库，在执行完成 git init 命令后，Git 仓库会生成一个 .git 目录，该目录包含了资源的所有元数据，其他的项目目录保持不变
# git init batmanfuture
初始化后，会在 batmanfuture 目录下会出现一个名为 .git 的目录，所有 Git 需要的数据和资源都存放在这个目录中
# git add
如果当前目录下有几个文件想要纳入版本控制，需要先用 git add 命令告诉 Git 开始对这些文件进行跟踪，然后提交
# git commit -m '初始化项目版本'

# git clone
使用 git clone 从现有 Git 仓库中拷贝项目
# git config
git config --list		显示当前的 git 配置信息
git config -e    		编辑 git 配置文件
git config -e --global  针对系统上所有仓库
# git status
查看仓库当前的状态，显示有变更的文件
# git diff 
比较文件的不同，即暂存区和工作区的差异
# git log 
查看历史提交记录
# git pull
下载远程代码并合并
# git push
上传远程代码并合并
# git fetch
从远程获取代码库
```

###### markdown语法

略