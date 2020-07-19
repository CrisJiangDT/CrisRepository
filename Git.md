<link href="http://cdn.bootcss.com/highlight.js/8.0/styles/monokai_sublime.min.css" rel="stylesheet">  
<script src="http://cdn.bootcss.com/highlight.js/8.0/highlight.min.js"></script>  
<script>hljs.initHighlightingOnLoad();</script>

# Git #

- Git是由Linux的创始人Linus发明的一种**分布式版本控制工具**

---
- 版本控制工具分为：
 - **集中式版本控制工具（如SVN）**：在一台服务器上保存所有信息，而我们主机上只保存本地信息，若服务器宕机则信息丢失（单点故障）
 - **分布式版本控制工具（如Git）**：在单台主机上就能实现完整的版本控制，可以避免单点故障
- **版本控制工具的功能：**
 - **协同修改、分支管理**：多人可以同时修改服务器端的同一文件
 - **数据备份**：能够保存文件每一个提交过的历史状态
 - **版本管理**：在保存文件的每一个版本时，应不保存重复数据，节约空间，如SVN采取的是增量式管理的方式，而Git采取的是文件系统快照的方式
 - **权限控制**：对参与开发的人员的权限进行控制，Git还能对团队外开发者贡献的代码进行审核（Git独有）
 - **历史记录**：可以查看修改人、修改时间、修改内容、日志信息等，还能将本地文件还原到某个历史状态
---
- Git的优势：
 - 大部分操作在本地完成，不需要联网
 - 完整性保证
 - 尽可能添加数据，而不是修改或删除数据，可以回退到历史状态
 - 分支操作非常流畅便捷
 - 与Linux命令兼容

#### Git的结构 ####

- Git分为工作区（写代码）、暂存区（临时存储）、本地库（历史版本）
![](.\MyPic\GitStructure.bmp)

#### 代码托管中心 #### 

- 代码托管中心的任务：维护远程库
- 代码托管中心：
 - 局域网环境下：GitLab服务器等
 - 外网环境下：GitHub、码云等
- 本地库和远程库交互的方式：[点击观看视频，超形象！！](https://www.bilibili.com/video/BV1pW411A7a5?p=9)

## Git操作 ## 

- `git 命令 --help`：查看该命令的帮助文档

#### 初始化本地库 ####

1. 安装好Git后，右键Git Bash，然后就可以用Linux的命令进行操作
2. 在某个目录中`git init`，在此初始化一个本地库，会自动创建一个隐藏目录.git，可以通过`ll -la`查看是否存在

#### Git设置签名 ####

- Git签名的作用：区分不同开发人员的身份，这里的签名与登录远程库（代码托管中心，如GitHub）的账号、密码没有任何关系
- Git签名：
 - 项目级别/仓库级别：仅在当前本地库范围内有效，设置后会保存在当前本地库的`./git/config`文件中
```
git config user.name Cris
git config user.email Cris_JiangDT@foxmail.com # 邮箱不需要真实存在
```
 - 系统用户级别：登录当前操作系统的用户范围有效，设置后会保存在当前系统用户根目录的`~/.gitconfig`文件中
```
git config --global user.name Cris
git config --global user.email Cris_JiangDT@foxmail.com # 邮箱不需要真实存在
```
- 项目级别的Git签名和系统用户级别的Git签名必须要有一个，否则Git无法区分不同开发人员的身份，若两者都有，就近原则以项目级别的Git签名为准

#### 查看Git状态、添加到暂存区、提交到本地库 ####

- `git status`：可以查看当前Git工作区、暂存区的状态，可以查看到当前目录下是否有仍未被Git管理的文件、暂存区里是否有未提交的文件、是否有经过修改的文件等
- `git add [fileName]`：将工作区的新建/修改的指定文件添加到暂存区中
- `git commit -m "commitMessage" [fileName]`：将暂存区的指定文件提交到本地库中，并指定提交信息

#### 查看历史记录 ####

- `git log`：完整的历史记录，只显示当前版本之前的历史版本
```
$ git log
commit 88866b9b8d11fef846d9a052d3638fb52d649c3e (HEAD -> master)
Author: Cris <Cris_JiangDT@foxmail.com>
Date:   Tue Jul 7 16:40:33 2020 +0800
    Third Committed
...
```
- `git log --pretty=oneline`：将每个历史记录显示在一行中，更简洁，只显示当前版本之前的历史版本
```
$ git log --pretty=oneline
88866b9b8d11fef846d9a052d3638fb52d649c3e (HEAD -> master) Third Committed
8ed56332d73b06db02aea239a0c934425aa21547 Second Committed
62635447d00dee0cce8eb40821501e52716cda6f First Committed
```
- **`git log --oneline`**：将每个历史记录显示在一行中，且截取了哈希值，只显示当前版本之前的历史版本
```
$ git log --oneline
88866b9 (HEAD -> master) Third Committed
8ed5633 Second Committed
6263544 First Committed
```
- **`git reflog`**：增加了`HEAD@{1}`，表示回退到该历史记录需要回退几步，且**回退到某个版本后，仍能显示后来的版本**
```
$ git reflog
88866b9 (HEAD -> master) HEAD@{0}: commit: Third Committed
8ed5633 HEAD@{1}: commit: Second Committed
6263544 HEAD@{2}: commit (initial): First Committed
```

#### 前进后退版本 ####

- 前进后退版本的实质是移动HEAD指针
1. 基于哈希索引的前进后退版本操作，可以前进或后退
 - `git reset --hard [局部哈希值]`
1. 使用`^`符号的前进后退版本操作，只能后退
 - `git reset --hard HEAD^^`：后退2步，有几个`^`就后退几步
1. 使用`~`符号的前进后退版本操作，只能后退
 - `git reset --hard HEAD~n`：后退n步

###### 理解`git reset`命令的三个参数：`--hard`、`--mixed`、`--soft` ######

- **`git reset --hard`的效果：**
 - **在本地库移动HEAD指针**
 - **重置暂存区**
 - **重置工作区**
 - `git status`显示`nothing to commit, working tree clean`
- **`git reset --mixed`的效果：**
 - **在本地库移动HEAD指针**
 - **重置暂存区**
 - `git status`显示`Changes not staged for commit`和红字`modified:   a.txt`，因为此时本地库和暂存区往后退了，显得工作区前进了，也就是**工作区中有未add到缓存区的修改**
- **`git reset --soft`的效果：**
 - **在本地库移动HEAD指针**
 - `git status`显示`Changes to be committed`和绿字`modified:   a.txt`，因为此时本地库往后退了，显得暂存区和工作区前进了，也就是**暂存区中有未commit到本地库的修改**

#### 找回删除的文件 ####

- 能找回删除的文件的前提：删除前，文件还存在时的状态commit到了本地库
- 找回删除文件的操作：`git reset --hard [指针位置]`
 - 若删除操作已经commit到了本地库，则指针应指向历史记录中还未执行删除操作的历史版本
 - 若删除操作还未commit到本地库，则指针应指向当前记录，即`git reset --hard HEAD`，也就是暂存区和工作区都会更新到与本地库的当前状态一致

#### 比较文件 ####

- `git diff [文件名]` 比较工作区和暂存区中的指定文件，如`git diff a.txt`表示比较工作区的a.txt和暂存区的a.txt
- `git diff [本地库中的历史记录] [文件名]` 比较工作区和本地库中指定历史记录的指定文件，如`git diff HEAD^ a.txt`表示比较工作区的a.txt和本地库中上一历史版本的a.txt
- 若不指定文件名，则会将所有文件的差异显示出来

#### 分支管理 ####

- 分支管理是指在版本控制过程中，同时推进多个任务
- 分支管理的好处：每个分支相互独立，可以并行多个功能的开发，提高开发效率
![](.\MyPic\branch.jpg)
- Git分支管理的本质是创建和移动指针，因此效率很高
![](.\MyPic\branch1.bmp)
--
- `git branch -v`：查看分支
- `git branch [分支名]`：创建分支
- `git checkout [分支名]`：切换到指定分支
- 合并分支，例如将hot_fix分支合并到master分支中：
 1. 切换到接受合并的分支：`git checkout master`
 2. 使用`git merge [分支名]`执行合并：`git merge hot_fix`

##### Git工作流 #####

- Git工作流示意图：
![](.\MyPic\gitWorkFlow.bmp)

##### 解决合并分支后产生的冲突 #####

- 合并两个分支时，若两个分支对同一文件的同一位置做了不同的修改，则会产生冲突
- 冲突的表现：
![](.\MyPic\branchMergeConflict.bmp)
- 冲突的解决：
 1. `vim`编辑有冲突的文件，将Git添加的特殊字符删除，修改文件到满意的状态，保存退出
 2. `git add 文件名`
 3. **`git commit -m "message"`** （注意：此时是在合并过程中解决合并冲突，**不能加文件名**）

##### Git保存版本的机制 #####

- **集中式版本控制工具**（如SVN）采用**增量存储**，保存的是一组基本文件以及每个文件累计的差异
![](.\MyPic\svn.bmp)
- **Git**把数据看作是文件的**快照**，**每次提交更新**时都会对当前的全部文件**制作一个快照**，并**保存这个快照的索引**，同时将该快照的**parent指针指向前一个历史版本**，其中**没有修改的文件**，Git会**保存一个指向之前存储的文件的指针（历史记录之间是链表结构）**
![](.\MyPic\gitSnapshot.bmp)
 - Git的“提交对象”：每个文件都有哈希值，它们组合成一个树，这个树也有哈希值，而“提交对象”又包含这个树和parent指针等，也有一个哈希值
![](.\MyPic\gitCommitObject.bmp)

## Git与GitHub交互 ##

#### 本地库push到远程库 ####

- 本地库push到远程库步骤：
 1. 在本地创建本地库，在GitHub创建远程库
 2. 在本地查看远程库的别名：`git remote -v`
 2. 在本地创建远程库的别名：`git remote add [远程库别名] [远程库的网址]`
 3. 将本地库push到远程库：`git push [远程库别名/网址] [要推送到的远程库分支]`
 4. 输入GitHub的账号及密码即可

#### 远程库clone到本地库 ####

- 远程库clone到本地库步骤：
 1. `git clone [远程库的仓库名称]`即可
- `git clone`命令的效果：
 - 完整地将远程库中的内容下载到本地库
 - 自动创建origin的远程库别名
 - 初始化本地库

#### 加入团队 ####

- 团队协作示意图
![](.\MyPic\githubTeam.bmp)
- A在GitHub创建了一个远程库，B想直接push到该仓库，是无法完成的，需要A将B邀请到团队中，需要A在GitHub中该仓库的`Setting > Manage Access`进行邀请B，然后B同意，才能对该仓库进行push操作

#### 远程库pull到本地库 ####

- pull操作是指要将本地库的内容更新为远程库的内容，而前面所说的clone操作是指本地库还没有内容
- 如果项目内容较简单，可以直接执行pull操作：
 - `git pull [远程库别名] [远程库分支]`
- 如果项目内容比较复杂，想先抓取到本地，查看后再更新本地库，则**可以先fetch再merge**，即**`pull=fetch+merge`**，步骤如下：
 - `git fetch [远程库别名] [远程库分支]`：将远程库内容fetch到本地
 - `git checkout [远程仓库别名/远程库分支]`：切换到远程库分支来查看fetch到的内容，例如`git checkout origin/master`
 - `git checkout [本地库分支]`：切换回本地库的分支
 - `git merge [远程仓库别名/远程库分支]`：将远程库分支中的内容merge到本地库中，例如`git merge orgin/master`

#### 解决团队冲突 ####

- **GitHub只接收基于最新版本所做修改的版本的push，若不是基于最新版本所做的修改，必须先pull到本地库，按照“分支冲突”操作解决后，再push**
- 团队冲突问题：A和B都clone/pull版本1，A修改后push版本2，B修改后push版本3，若B能push成功，则A push的版本3会被覆盖，造成丢失，因为B是在版本1的基础上进行的修改

#### 跨团队合作 ####

- 跨团队合作示意图
![](.\MyPic\githubTeams.bmp)
- 团队A拥有一个远程库RA，需要团队B协作，则步骤：
 1. 团队B可以在GitHub上fork RA到自己团队的远程库RB
 2. 团队B可以从自己的远程库RB中clone代码到本地库进行修改，修改完后再push到RB
 2. 团队B在GitHub上发起“pull request”请求，请求将修改好的代码更新到团队A的远程库RA中
 3. 团队A需要在GitHub上审核代码，审核通过后在GitHub上执行merge操作，将B团队的远程库RB中的修改更新到自己的远程库RA中

### SSH登录GitHub ###

- 使用SSH登录GitHub，就不需要每次push都输入账号密码，但每个操作系统用户只能设置一个用SSH登录的GitHub账号，步骤：
 1. 进入当前操作系统用户的根目录：`cd ~`
 2. 删除之前的.ssh目录：`rm -rf .ssh`
 3. 生成.ssh密钥目录：`ssh-keygen -t rsa -C [GitHub账号邮箱]`（注意`C`是大写）
 4. 进入.ssh目录，查看id_rsa.pub文件内容：`cat .ssh/id_rsa.pub`
 5. 在GitHub中`头像 > Settings > SSH and GPG keys > New SSH Key`，将id_rsa.pub文件内容粘贴进去
 6. 回到Git Bash，创建远程库别名，如`git remote add origin_ssh [远程库SSH地址]`，其中远程库SSH地址可以在GitHub的远程库中找到
 7. 以后push的时候用SSH的远程库别名即可，如`git push origin_ssh master`

## 在IDEA中使用Git ##

#### 准备工作 ####

1. `Settings -> Version Control > Git > Path to Git executable`选中安装好的git.exe
1. `Settings -> Version Control > GitHub`添加GitHub账户并选中该账户

#### 创建本地库 ####

1. `IDEA导航栏 > VSC > Import into Version Control > Create Git Repository`，选中项目的上一级目录，IDEA就会自动在项目的上一级目录下创建.git目录
1. IDEA中自动创建的一些文件在版本控制时可以忽略，`右键项目 > Git > Add to .gitignore > .git/info/exclude`，配置：
```
.idea
*.iml
```
1. 之后项目的add、commit、查看历史版本等都可以通过`右键项目 > Git > Repository`或点击上下操作栏图标或点击Terminal的命令行来完成
2. 查看历史版本时，右键某个历史版本，可以复制其哈希值，再`右键项目 > Git > Repository > Reset HEAD...`，输入参数，可以回退到该历史版本

#### 解决分支冲突 ####

1. 在两个分支中的同一位置添加不同的内容，`右键项目 > Git > Repository > Merge Changes...`合并分支时会出现冲突，可以选择`Merge`来自定义解决冲突内容

#### push操作、pull操作 ####

1. 略

#### clone操作 ####

1. `操作栏VCS > Git > Clone`，添加要clone的远程库的URL，并选择本地目录（注意必须是空目录/新创建的目录）

#### SSH登录 ####

- 按照前面“SSH登录GitHub”的步骤生成私钥后，在IDEA中push时添加SSH的远程库地址并选择它即可

## Gitee码云 ##

- Gitee码云的操作和GitHub类似

## GitLab服务器 ##

- GitLab服务器是搭建在局域网环境下的，功能和GitHub、Gitee（码云）类似
- 如果需要用到GitLab，搭建的教程：[点击观看教程](https://www.bilibili.com/video/BV1pW411A7a5?p=57)