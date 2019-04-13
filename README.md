# 0 前言

Git 是一个开源的分布式版本控制系统。Git 与常用的版本控制工具 CVS, Subversion 等不同，它采用了分布式版本库的方式，不必服务器端软件支持。

git工作结构：

- 工作区（Working Directory）：个人电脑上项目开发的工作路径

- 版本库（Repository）：隐藏文件.git ，内含两部分

1.  暂存区（index）：更改后的文件可以使用 add 暂存在这里
2. 项目分支(HEAD）：从暂存区commit 过来

![在这里插入图片描述](https://img-blog.csdnimg.cn/2019041317273770.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2R6Z19jaGF0,size_16,color_FFFFFF,t_70)

涉及到多个区域之间文件提交、回滚请看下文。

```
git --help   #查看常用指令
```

# 1 安装

不同系统环境安装参考：[链接](https://git-scm.com/downloads)

ubuntu安装

```
apt-get install git
```

更稳定的版本：

```
sudo add-apt-repository ppa:git-core/ppa
sudo apt update
sudo apt install git
```

# 2 创建一个本地Git

## 2.1 初始化（init）

```
git init 
```

初始化后，在当前目录创建 .git 文件夹，是Git中最重要的文件夹，Git相关文件以及版本都将保存在该文件夹中，Git命令可以将所有版本保存在 .git 文件中。

创建到指定目录下：`git init foldername`

## 2.2 添加文件（add）

添加当前**工作目录**下指定文件到**暂存区**：

```
git add *.c #所有c文件
git add hell0.py #指定的一个
```

添加工作目录下**所有文件**到暂存区：

```
git add .
```

设置身份标识：`只在当前仓库设置标识就去掉 --global`

```
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
```

## 2.3 提交（commit）到版本库HEAD

提交到版本库HEAD主支，并填写版本**说明**，以便以后回滚

```
git commit -m 'firstcommit'
```

会生成对应的版本名和ID，**[master (root-commit) df47fr8] firstcommit**

**需要注意的是：代码、目录文件修改了以后及时 add 到暂存区，当然也可以忽略暂存区的add过程，工作区修改后直接提交到版本库HEAD里。**

```
git commit -am ‘第几次修改’
```

# 3 工作目录文件状态

## 3.1查看状态（status）

```
git status   
```

查看当前**工作目录**git状态，**上次提交（commit）后**是否变化了。没变化就是干净的。

## 3.2 比较文件（diff）

如果上次 **commit 后**对目录内文件进行了修改，但是**没有commit提交**，可以使用diff看做了什么操作，修改了什么文件。

```
git diff
```

commit之后没有修改就没输出。

其他类型diff说明：

- 工作区和加到缓存区（index）的版本对比：**git diff**
- 已经加到缓存区（index）和HEAD库里版本的区别： **git diff --cached**
- 查看版本库(HEAD)和没加到index的工作目录区别：**git diff HEAD**

结构关系示例图：[参考图解git](http://marklodato.github.io/visual-git-guide/index-zh-cn.html#diff)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190413172846548.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2R6Z19jaGF0,size_16,color_FFFFFF,t_70)

显示摘要信息而非完整比较信息 diff：

```
git diff --stat
```

## 3.3 仓库项目版本（log）

```
git log 
```

查看当前版本库HEAD的版本详细说明，包含用户信息、版本ID值、操作日期等，方便不同版本代码回滚、切换传递。

不同参数的信息：**git log --oneline** **--decorate --graph**

更多的log状态指令：[参考](https://git-scm.com/docs/git-log)

# 4 代码回滚

不同区域文件传递如下图所示，图示来源：[图解git](http://marklodato.github.io/visual-git-guide/index-zh-cn.html#diff)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190413172912929.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2R6Z19jaGF0,size_16,color_FFFFFF,t_70)

## 4.1 版本库（HEAD）的回滚

先查看提交（commit）历史，确认想恢复的版本和对应的版本commit ID：

```
git log
```

回滚到指定版本：**ID前6位左右就行**

```
git reset --hard 540a7cb
```

或者使用：**HEAD^**上一次  ；**HEAD^^**上两次； **HEAD~N** 上N次

```
git reset --hard HEAD^

》》HEAD 现在位于 a522571 firstpull
```

回退版本之后**反悔了**想要再回到回滚之前的版本：

```
git reflog
```

此时先记住ID，然后使用**git reset --hard ID**就能再回去了。

**注意：**

- 使用 --hard 选项时不仅更新了版本库，也更新了工作目录。
- 使用 --soft 选项是，工作目录和暂存区不变。只有HEAD回滚了。
- 不使用参数使用HEAD: git reset HEAD^  暂存区也变，工作目录不会。

其他参数说明参考：[博客](https://www.jianshu.com/p/e11c7ebcae4e)

## 4.2 撤销修改（checkout）

####  情况1

对文件进行了修改，但是还没有add和commit，你想要撤销当前所做的修改。

```
git checkout -- file
```

checkout用于从**已经提交**（commit）或者未commit但**已经add到暂存区**（index）的文件中拷贝到本地工作目录，也可用于切换分支。

- 修改前已经commit时：checkout就回到和版本库相同的状态。
- 修改前仅add时：checkout就回到add到暂存区后的状态，不和版本库相同。

#### 情况2 

如果不仅修改了，还add到了暂存区：

此时先把add到暂存区的修改撤销掉，回到工作目录。

```
git reset HEAD file
```

此时暂存区（index）和版本库（HEAD）中的相同，和工作目录中的不同。

再进行情况1的操作，checkout掉工作区的部分（从变了的index里）。

```
git checkout -- file
```

# 5 删除Git文件

## 5.1 版本库HEAD中的删除

Git 版本库中删除文件：首先删除，然后提交（commit）确认删除。

这样工作目录和版本库里的这个file都被删掉了。

```
git rm file
git commit -m 'delatefile'
```

## 5.2 暂存区（index）中的删除

当本地路径改动的文件已经 add 到暂存区，但是没有提交 commit 的时候：、

这个命令会将暂存区和本地目录的文件一起删掉：

```
git rm -f file
```

想删除暂存区但是保留本地：

```
git rm --cached file
```

## 5.3 只删除本地

和linux一样：**rm file** **rm ***   **rm -r folder**

# 6 本地仓库和远程仓库连接

著名的github就相当于一个永远开机的远程服务器仓库，实际开发时项目组成员之间在不同地点和系统都可以协作对一个仓库里的代码进行编辑、优化。

## 6.0 前提条件

先下载对应操作系统的git客户端。

Git bash中输入指令，一直回车生成密钥对：

```
ssh-keygen -t rsa -C "githubloginemail@example.com"
```

主目录也即用户user/xxx/下查看隐藏文件夹 .ssh；里面有id_rsa和id_rsa.pub一对生成的公钥和私钥。

复制id_rsa.pub里面的内容。打开github在个人设置中心添加ssh，把内容复制进去，添加完成。

然后输入：

```
ssh -T git@github.com
```

会有个提示，输入yes看到：You've successfully authenticated, but GitHub does not provide shell access 表明本地git成功连接到github了。

## 6.1 提交到github

1 在github创建一个新Repository，输入Repository Name然后创建。

创建好后弹出的页面复制该项目对应的ssh协议内容：如

- git@github.com:dzglearning/UsingGit.git

之后就可以借助这个地址通过本地git创建上传文件到github了。

2 本地目录下创建README > git初始化 > add到index > commit到本地仓库HEAD

```
echo "git使用方法" >> README.md
git init
git add README.md
git commit -m "first commit"
```

3 配置远程github仓库地址：

```
git remote add origin git@github.com:dzglearning/UsingGit.git
```

origin是别名，后面对应的之前在github创建新项目时复制的ssh信息。

**也可以进入本地仓库文件.git 目录下的config 文件里直接进行配置。**

- git remote -V 查看当前配置的仓库

4 把本地库HEAD的内容推送到远程github仓库：  

```
git push -u origin master
```

之后只要修改了本地的内容，想同步推送到远程仓库时输入：

```
git push origin master
```

即可，第一次 -u 已经把本地和远程master分支进行了关联，之后会介绍分支（branch）。

参考：

廖雪峰老师教程：[链接](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/001374385852170d9c7adf13c30429b9660d0eb689dd43a000)

Runoob教程：[链接](http://www.runoob.com/git/git-remote-repo.html)

## 6.2仓库克隆（clone）

**从远程git仓库克隆（clone）文件到本地：**

```
git clone 协议路径 myproject
```

-  `git://` 协议：git://github.com/project/grit.git
-  `http(s)://`协议：https://github.com/project/grit.git
-  `user@server:/path.git`  SSH 传输协议等

一般ssh最快。

**从本地仓库跨文件夹clone：**

```
git clone /path/to/project2 
```

## 6.3 从远程仓库更新本地（pull、fetch、merge）

- git pull：是从远程获取最新版本到本地，并自动merge
- git fetch：是从远程获取最新版本到本地，不会自动merge
- git merge：是用于从指定的commit(s)合并到当前分支，用来合并两个分支

之前配置好了远程路径origin。

先获取：再同步合并更新本地的主支：

```
git fetch origin
git merge origin/master
```

或者直接使用pull：

```
git pull origin
```

## 6.4 删除远程仓库

```
git remote rm [别名]
```

例子：

```
git remote add origin2 git@github.com:xxx/test.git  #先添加
git remote rm origin2   #再删除
```

# 7 分支

分支就是在开发新特性的时候不影响项目功能的正常运行，开发后可以将新功能合并到主支master上。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190413172959954.PNG)

## 7.1分支的基础应用

###  7.1.1创建分支

```
git branch newbranch  #创建
git checkout newbranch  #切换到了新建的分支
```

或者使用如下命令直接创建并切换：

```
git checkout -b newbranch
```

将分支推送到远程git仓库

```
git push origin <branch>
```

### 7.1.2 删除分支

删掉创建的新分支：

```
git branch -d newbranch
```

切换回主分支：

```
git checkout master
```

强制删除没有合并的分支：

```
git branch -D branch
```

## 7.2 合并分支到主支master

###  7.2.1 普通合并
```
git merge branchname
```

### 7.2.2 合并冲突(conflict)

当分支branch和主支master都对**同一个文件**进行了修改，合并的时候就**可能**报错冲突（CONFLICT ）
- Merge conflict in xxxfile。

这时可以手动修改 有问题的文件，然后再 git add >> commit 即可 。

### 7.2.3 保留分支信息合并

参数--no-ff，不使用fast-forward方式合并，保留分支的commit历史信息

```
git merge --no-ff -m "merge with no-ff" branchname
```

参考：[廖雪峰老师分支管理策略](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/0013758410364457b9e3d821f4244beb0fd69c61a185ae0000)  [其他论坛](https://segmentfault.com/q/1010000002477106)

合并时可以使用：**git diff branch1 branch2**对比不同分支branch的差异。

## 7.3 bug分支

开发新功能中》主程序突发bug》先把开发的功能保存》创建bug修复分支》修复bug合并到主支》新功能继续开发》新功能合并到主支

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190413173037778.PNG)

使用branch相关的命令进行多次创建、合并、删除分支即可：

- git branch 分支名称             创建分支
- git checkout 分支名称          切换分支
- git branch -m 分支名称        创建并切换到指定分支
- git branch                          查看所有分支
- git branch -d 分支名称         删除分支
- git merge 分支名称              将指定分支合并到当前分支

或者使用**stash**：

先把当前工作封存：

```
git stash
```

解决bug中。。。。。ok解决了。

恢复之前开发过程：恢复时会把临时存放的stash文件也删了

```
git stash pop
```

等同于：

```
git stash apply  #先恢复
git stash drop  #删除临时文件
```

参考链接：[廖雪峰老师](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/00137602359178794d966923e5c4134bc8bf98dfb03aea3000)  [另一个幽默风趣的博客](https://www.cnblogs.com/wupeiqi/p/7295372.html)

## 7.4 多人协作

之前使用clone都是获取的项目master主支，多人协作时需要再主支master和分支branch上进行各自任务块的开发，然后推送到远程仓库。

**首先确认远程仓库github上有主支master和分支branch等**

大体流程：

1 克隆（clone）项目master到本地。

```
git clone git@github.com:xxx/xxx.git
```

使用**git branch**可以看到本地只有master没有testbranch这个分支。

2 创建自己本地分支mybranch和远程仓库结构一样。

```
git checkout -b testbranch origin/testbranch
```

然后开发自己负责的某一功能模块。

3 开发完毕推送分支testbranch到远程仓库

```
git push origin testbranch
```

同事的其他功能模块已经推送到这个分支了，所以你的文件可能和他冲突而报错。

4 先把最新的远程文件更新（pull）到本地，更新前可能报错，先关联再pull

```
git branch --set-upstream-to=origin/testbranch testbranch
git pull
```

5 更新的时候可能和本地冲突，手动修改add>commit之后再push到远端。

```
git push origin testbranch
```

参考教程：[廖雪峰老师git多人协作](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/0013760174128707b935b0be6fc4fc6ace66c4f15618f8d000)

# 8 git标签 

标签类似commit ID 比较重要的节点使用标签更简洁方便的区分版本。

## 8.1 打标签

```
git tag -a v1.0
```

创建一个带注解说明的标签，会弹出窗口让你输入注解。写入保存退出即可。

也可以给之前的某个版本追加标签，要有commitID

```
git tag -a v0.5 64c2ef3
```

## 8.2 查看已有标签

```
git tag
```

## 8.3 删除标签

```
git tag -d v1.1
```

## 8.查看标签版本的详细信息

```
git show v1.0
```

其他内容如git的用户、系统、项目配置，搭建小型git服务器，gitignore文件、更多高级的git指令可以访问官方社区。

学习参考资料：

廖雪峰老师的git教程：[链接](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)		runoob网站git教程：[链接](http://www.runoob.com/git/git-tutorial.html)

git社区（英文）：[链接](https://book.git-scm.com)			（中文）：[链接](http://gitbook.liuhui998.com/index.html)

