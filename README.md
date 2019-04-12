# 0 前言

Git 是一个开源的分布式版本控制系统。Git 与常用的版本控制工具 CVS, Subversion 等不同，它采用了分布式版本库的方式，不必服务器端软件支持。

git工作结构：

- 工作区（Working Directory）：个人电脑上项目开发的工作路径

- 版本库（Repository）：隐藏文件.git ，内含两部分

1.  暂存区（index）：更改后的文件可以使用 add 暂存在这里
2. 项目分支(HEAD）：从暂存区commit 过来

![](C:\Users\dzg\Desktop\git\1352126739_7909.jpg)

多个区域之间文件提交、回滚之后讲。

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

# 2 创建Git

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

添加工作目录下所有文件到暂存区：

```
git add .
```

设置身份标识：`只在当前仓库设置标识就去掉 --global`

```
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
```

## 2.3 提交（commit）到版本库HEAD分支

提交到版本库HEAD分支，并填写版本**说明**，以便以后回滚

```
git commit -m 'firstpull'
```

会生成对应的版本名和ID，`[master (root-commit) df47fr8] firstpull`

**需要注意的是：代码、目录文件修改了以后一定及时 add 到暂存区，当然也可以忽略暂存区的add过程，工作区修改后直接提交到版本库HEAD。**

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

如果上次 commit 后对目录内文件进行了修改，但是没有提交，可以使用diff看做了什么操作，修改了什么文件。

```
git diff
```

commit之后没有修改就没输出。

其他类型diff说明：

- 工作区和加到缓存区（index）的版本对比：**git diff**
- 已经加到缓存区（index）和HEAD库里版本的区别： **git diff --cached**
- 查看版本库(HEAD)和没加到index的工作目录区别：**git diff HEAD**

结构关系示例图：[参考图解git](http://marklodato.github.io/visual-git-guide/index-zh-cn.html#diff)

![](C:\Users\dzg\Desktop\git\捕获.PNG)

显示摘要信息而非完整比较信息 diff：

```
git diff --stat
```

## 3.3 仓库项目版本（log）

```
git log 
```

查看当前版本库的版本详细说明，包含用户信息和版本ID值等，方便不同版本代码回滚、切换传递。

简洁信息：**git log --oneline**

更多的log状态指令：[参考](https://git-scm.com/docs/git-log)

# 4 代码回滚

图示来源：[图解git](http://marklodato.github.io/visual-git-guide/index-zh-cn.html#diff)

![](C:\Users\dzg\Desktop\git\回滚.PNG)

## 4.1 版本库（HEAD）的回滚

先查看提交（commit）历史，确认恢复的版本和对应的版本commit ID：

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

回退版本之后反悔了想要再回到回滚之前的版本：

```
git reflog
```

先记住ID，使用**git reset --hard ID**就能再回去。

**注意：**

- 使用 --hard 选项是不仅更新了版本库，也更新了工作目录。
- 使用 --soft 选项是，工作目录和暂存区不变。只有HEAD回滚了。
- 不使用参数使用HEAD: git reset HEAD^  暂存区也变，工作目录不会。

其他说明参考：[博客](https://www.jianshu.com/p/e11c7ebcae4e)

## 4.2 撤销修改（checkout）

####  情况1

不小心进行了修改，不过没有add和commit，你想要撤销当前所做的修改。

```
git checkout -- file
```

checkout用于从**已经提交**（commit）或者未commit但**已经add到暂存区**（index）的文件中拷贝到本地工作目录，也可用于切换分支。

- 修改前已经commit时：撤销修改就回到和版本库相同的状态。
- 修改前仅add时：撤销修改就回到add到暂存区后的状态，不和版本库相同。

#### 情况2 

如果不幸修改了之后，还add到了暂存区：

先把add到暂存区的修改撤销掉，回到工作目录。

```
git reset HEAD file
```

再和上面相同，checkout掉工作区的

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

和linux一样：**rm file**

# 6 本地仓库和远程仓库连接

著名的github就相当于一个永远开机的远程服务器仓库，实际开发时项目组成员之间在不同地点和系统都可以协作对一个仓库里的代码进行编辑、优化。

## 6.0 前提条件

下载对应系统的git客户端

Git bash中输入指令，一直回车生成密钥：

```
ssh-keygen -t rsa -C "githubloginemail@example.com"
```

主目录也即用户user/xxx下查看隐藏文件 .ssh；里面有id_rsa和id_rsa.pub。复制pub里面的内容。打开github个人设置添加ssh，把内容复制进去，添加完成。

然后输入：

```
ssh -T git@github.com
```

会有个提示，输入yes看到：You've successfully authenticated, but GitHub does not provide shell access 表明成功了。

## 6.1 提交到github

1 在github创建一个新Repository，输入Repository Name然后创建

创建好后弹出的页面复制该项目对应的ssh内容：如

- git@github.com:dzglearning/UsingGit.git

之后就可以通过本地git创建上传文件了

2 本地目录创建文件：初始化 添加 提交到本地仓库

```
echo "# UsingGit" >> README.md
git init
git add README.md
git commit -m "first commit"
```

3 配置远程github仓库地址：

```
git remote add origin git@github.com:dzglearning/UsingGit.git
```

对应的你之前在github创建新项目时复制的ssh信息。

**也可以进入本地仓库文件.git 目录下的config 文件里进行配置。**

- git remote -V 查看当前配置的仓库

4 把本地库的内容推送到远程github仓库：  

```
git push -u origin master
```

之后只要修改了本地的内容，想同步推送到远程仓库时输入：

```
git push origin master
```

即可，第一次 -u 已经把本地和远程master分支进行了关联，之后会将分支。

参考：

廖雪峰老师：[链接](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/001374385852170d9c7adf13c30429b9660d0eb689dd43a000)

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

**从本地仓库clone本地：**

```
git clone /path/to/repository 
```

## 6.3 跟随远程仓库更新本地（pull、fetch、merge）

- git pull：是从远程获取最新版本到本地，并自动merge
- git fetch：是从远程获取最新版本到本地，不会自动merge
- git merge：是用于从指定的commit(s)合并到当前分支，用来合并两个分支

之前配置好了远程路径origin。

先获取：

```
git fetch origin
```

同步合并更新本地的分支：

```
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

