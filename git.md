# git

git是一个开源分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目。



## Git安装

- windows

[Git (git-scm.com)](https://git-scm.com/)

![image-20230412150856344](https://img.betazhu.top/blog/essayimage-20230412150856344.png)



- ubuntu

```bash
$ apt-get install libcurl4-gnutls-dev libexpat1-dev gettext \
  libz-dev libssl-dev

$ apt-get install git

```



## git配置

登录用户信息

```bash
git config --global user.name "你的用户名"
git config --global user.email 你的邮箱
```

查看配置信息

```bash
git config --list
```

由于本地 Git 仓库和 GitHub 仓库之间的传输是通过SSH加密的，所以我们需要生成 SSH Key配置验证信息

```
#生成ssh
ssh-keygen -t rsa -C "youremail@example.com"
```

- Ubuntu：

​		一路点回车，以下代码查看公钥，复制公钥

```bash
cat /root/.ssh/id_rsa.pub
```

![image-20230412152031831](https://img.betazhu.top/blog/essayimage-20230412152031831.png)



​	复制公钥在github上左上角头像——>`settings`——>左边栏的`SSH and GPG keys`——>`New SSH key`。粘贴即可

![image-20230412152412633](https://img.betazhu.top/blog/essayimage-20230412152412633.png)

- windows则是在，此目录中，打开id_rsa.pub，复制文本进入SSH即可。

  ![img](https://img-blog.csdnimg.cn/img_convert/d5e03b988fc3570e23472741de094e7e.png)

# Git创建仓库

## git 本地仓库
**Git 使用 git init 命令来初始化一个 Git 仓库，Git 的很多命令都需要在 Git 的仓库中运行，所以 git init 是使用 Git 的第一个命令。**

进入到工作目录中，并使用当前目录作为仓库。

```bash
git init
```

>Initialized empty Git repository in F:/NoteBook/program_env_doc/.git/

该命令执行完后会在当前目录生成一个 .git 目录。所有 Git 需要的数据和资源都存放在这个目录中

如果当前目录下有几个文件想要纳入版本控制，需要先用 git add 命令告诉 Git 开始对这些文件进行跟踪，然后提交

```bash
git add . #*.py  表示所有.py结尾的 添加文件到仓库
git add README
git commit -m '初始化项目版本' #  提交暂存区到本地仓库

```

以上命令将目录下所有文件及 README 文件提交到仓库中。



## Git 远程仓库(Github)

前文的仓库指的是**本地仓库**，如需要上传自己代码至云端供他人贡献，则需要使用git远程仓库远程仓库。



### 查看远程仓库

如果想查看你已经配置的远程仓库服务器，可以运行 `git remote` 命令。 它会列出你指定的每一个远程服务器的简写。 如果你已经clone了自己的仓库，那么至少应该能看到 origin ——这是 Git 给你克隆的仓库服务器的默认名字：

你也可以指定选项 `-v`，会显示需要读写远程仓库使用的 Git 保存的简写与其对应的 URL。

```console
$ git remote -v
origin	https://github.com/schacon/ticgit (fetch)
origin	https://github.com/schacon/ticgit (push)
```

如果你的远程仓库不止一个，该命令会将它们全部列出。 例如，与几个协作者合作的，拥有多个远程仓库的仓库看起来像下面这样：

```console
$ cd grit
$ git remote -v
bakkdoor  https://github.com/bakkdoor/grit (fetch)
bakkdoor  https://github.com/bakkdoor/grit (push)
cho45     https://github.com/cho45/grit (fetch)
cho45     https://github.com/cho45/grit (push)
defunkt   https://github.com/defunkt/grit (fetch)
defunkt   https://github.com/defunkt/grit (push)
koke      git://github.com/koke/grit.git (fetch)
koke      git://github.com/koke/grit.git (push)
origin    git@github.com:mojombo/grit.git (fetch)
origin    git@github.com:mojombo/grit.git (push)
```

### 添加远程仓库

**在GitHub网站上先建好仓库！**

方法一：使用git clone 拉取远程仓库

方法二：自行添加

运行 `git remote add <shortname> <url>` 添加一个新的远程 Git 仓库，同时指定一个方便使用的简写：

```console
git remote add shortname url
```

现在你可以在命令行中使用字符串 `shortname` 来代替整个 URL。 例如，如果你想拉取 Paul 的仓库中有但你没有的信息，可以运行 `git fetch shortname`：

### 从远程仓库中抓取git fetch 与拉取git pull

就如刚才所见，从远程仓库中获得数据，可以执行：

```console
$ git fetch <remote>
```

这个命令会访问远程仓库，从中拉取所有==**你还没有的数据**==。 执行完成后，你将会拥有那个远程仓库中所有分支的引用，可以随时合并或查看。

如果你使用 `clone` 命令克隆了一个仓库，命令会自动将其添加为远程仓库并默认以 “origin” 为简写。 所以，**`git fetch origin` 会抓取克隆（或上一次抓取）后新推送的所有工作。 必须注意 `git fetch` 命令只会将数据下载到你的本地仓库——它并不会自动合并或修改你当前的工作。** 当准备好时你必须**手动将其合并入你的工作。**

如果你的当前分支设置了跟踪远程分支（阅读下一节和 [Git 分支](https://git-scm.com/book/zh/v2/ch00/ch03-git-branching) 了解更多信息）， 那么可以用 `git pull` 命令来自动抓取后合并该远程分支到当前分支。 这或许是个更加简单舒服的工作流程。

`git clone` 命令会自动设置本地 master 分支跟踪克隆的远程仓库的 `master` 分支（或其它名字的默认分支）。

 运行 `git pull` 通常会从最初克隆的服务器上抓取数据并**自动尝试合并**到当前所在的分支。

### 推送git push到远程仓库

当你想分享你的项目时，必须将其推送到上游。 这个命令很简单：`git push <remote> <branch>`。 当你想要将 `master` 分支推送到 `origin` 服务器时（再次说明，克隆时通常会自动帮你设置好那两个名字）， 那么运行这个命令就可以将你所做的备份到服务器：

```console
$ git push origin master
```

只有当你有所克隆服务器的写入权限，并且之前没有人推送过时，这条命令才能生效。 当你和其他人在同一时间克隆，他们先推送到上游然后你再推送到上游，你的推送就会毫无疑问地被拒绝。 **你必须先抓取他们的工作并将其合并进你的工作后才能推送。** 阅读 [Git 分支](https://git-scm.com/book/zh/v2/ch00/ch03-git-branching) 了解如何推送到远程仓库服务器的详细信息。



### 远程仓库的重命名与移除

你可以运行 `git remote rename` 来修改一个远程仓库的简写名。 例如，想要将 `pb` 重命名为 `paul`，可以用 `git remote rename` 这样做：

```console
$ git remote rename pb paul
$ git remote
origin
paul
```

值得注意的是这同样也会修改你所有远程跟踪的分支名字。 那些过去引用 `pb/master` 的现在会引用 `paul/master`。

如果因为一些原因想要移除一个远程仓库——你已经从服务器上搬走了或不再想使用某一个特定的镜像了， 又或者某一个贡献者不再贡献了——可以使用 `git remote remove` 或 `git remote rm` ：

```console
$ git remote remove paul
$ git remote
origin
```

一旦你使用这种方式删除了一个远程仓库，那么所有和这个远程仓库相关的远程跟踪分支以及配置信息也会一起被删除。

## git分支管理

几乎每一种**版本**控制系统都以**某种形式支持分支。使用分支意味着你可以从开发主线上分离开来，然后在不影响主线的同时继续工作。**

有人把 Git 的分支模型称为必杀技特性，而正是因为它，将 Git 从版本控制系统家族里区分出来。

`git branch`命令允许对分支进行创建、列举、重命名以及删除的操作。它不能进行切换分支或者将分叉的commit记录扔到其他分支里。因此`git branch`总是与`git checkout`以及`git merge`命令共同出现在使用场景中。

**分支命令：**

```bash
git branch ## 列出所有分支 等于 git branch --list
```

创建一个名为 crazy-experiment的分支。但此命令并不会自动检出新创建的分支。

```bash
git branch crazy-experiment
```

![image-20230412155033177](https://img.betazhu.top/blog/essayimage-20230412155033177.png)

要注意此时你只是`创建`了这个分支。如需开始对新分支进行提交，要先选择这个新的分支，使用`git checkout`命令，然后再使用标准流程`git add` 、`git commit`等命令。