##### 备份文件

###### 网站源码

```shell
.rar
.zip
.7z
.tar.gz
.bak
.swp
.txt
.html
```

通常备份文件我们会先用扫描器快速扫一遍，源码泄露主要先猜测可能是哪种泄露

字典可以去github上搜，或者用[我提供的1](https://wws.lanzoui.com/ilr4ivlqe5c)，[2](https://wws.lanzoui.com/ir2mJvlqe6d)。

###### bak文件

index.php的备份文件是index.php.bak

###### vim缓存

vim在编辑文档的过程中如果异常退出，会产生缓存文件，第一次产生的缓存文件后缀为.swp，后面会产生swo等.，注意swp文件是隐藏文件，在linux中隐藏文件前有 . 即 `.index.php.swp`

###### 真题

> ctfhub的vim缓存
>
> ctfhub{ce55b6cb51aa598d0a2473e3}

###### .DS_Store文件泄漏

.DS_Store 是 Mac OS 保存文件夹的自定义属性的隐藏文件。.DS_Store可以知道这个目录里面所有文件的清单。

工具：[dsstoreexp](https://github.com/lijiejie/ds_store_exp)

用法：`python ds_store_exp.py http://www.example.com/.DS_Store`

或者直接在url后输入.DS_Store

###### 真题

> ctfhub的
>
> 5e2678c1e151924a311421f77f.txt中

##### 源码泄露

###### git 源码泄露

Git是分布式版本控制系统，在执行`git init`初始化目录的时候，会在当前目录下自动创建一个`.git`目录，用来记录代码的变更记录等。如果没有把`.git`这个目录删除，就直接发布到了服务器上，攻击者就可以通过它来恢复源代码。

漏洞利用工具：[GitHack](https://github.com/lijiejie/GitHack)（这里注意是使用python2的版本使用）

用法：`python2 GitHack.py http://www.openssl.org/.git/`

普通的git泄露题通常是和其他漏洞一起考察，但是见过有的考题是考察git的用法，所以下面介绍下git的用法（详细参考[菜鸟教程](https://www.runoob.com/git/git-tutorial.html)）：

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

###### 真题

> ctfhub的git泄露真题，三题（log，stash，index）

**log**

我做这题时发现拿下来的没有.git文件，应该是他们的服务器出错了，在.git那个目录输入`git log`，之后执行了add flag和remove flag。可以判断，flag应该是在add flag的那个版本，再输入`git diff HEAD^`即可

**stash**

git stash用于想要保存当前的修改,但是想回到之前最后一次提交的干净的工作仓库时进行的操作.git stash将本地的修改保存起来,并且将当前代码切换到HEAD提交上

`git diff 45c25c20bc571504e02c228f5536d0f2aa42849f`

**index**

和log差不多

###### hg源码泄漏

hg init的时候会生成.hg

漏洞利用工具：[dvcs-ripper](https://github.com/kost/dvcs-ripper)

[perl的下载地址](https://www.jb51.net/softs/27286.html#downintro2)

用法：rip-hg.pl -v -u http://www.example.com/.hg/

cmd中运行perl		perl -d example.pl

###### 真题

> ctfhub的.HG泄露
>
> ctfhub{3de718bb073a78967525a04a}

在cmd中切换到目录后输入

`perl -l rip-hg.pl -v -u http://challenge-8648e6b415415be7.sandbox.ctfhub.com:10800/.hg/`

得到了一堆文件，打开undo.dirstate这个文件发现了flag文件

[![52sioq.png](https://z3.ax1x.com/2021/10/23/52sioq.png)](https://imgtu.com/i/52sioq)

[![52sEWT.png](https://z3.ax1x.com/2021/10/23/52sEWT.png)](https://imgtu.com/i/52sEWT)

###### SVN源码泄露

当svn使用了checkout命令后就会生成.svn文件，里面存储着备份信息。svn信息泄露漏洞主要利用了里面的entrist文件，通过.svn/entrist可以下载里面的所有代码，但是只能作用在svn1.6之前的版本；第二个是作用在svn1.7后的版本，svn1.7后的版本引入一个名为wc.db的数据库数据存放文件来管理文件，通过访问.svn/wc.db可以下载到本地。

漏洞利用工具：[dvcs-ripper](https://github.com/kost/dvcs-ripper)

[perl的下载地址](https://www.jb51.net/softs/27286.html#downintro2)

用法：rip-svn.pl -v -u http://www.example.com/.svn/

cmd中运行perl		perl -d example.pl

###### 真题

> ctfhub的svn源码泄露
>
> 虽然我找到了flag，但是提交失败，ctfhub{e43e7ef8-82fb-4194-9673-81c29de69c33}

`perl -l rip-svn.pl -v -u http://challenge-0491266abd2615c7.sandbox.ctfhub.com:10800/.svn/`