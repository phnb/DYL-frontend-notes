# Git 学习笔记


## Git 基本概念

## 版本控制系统 Version Control System (VCS)
* 为了能够通过比较文件之间的细节来找出错误的来源
* 为了能够轻松、随时地回到之前某一时间点的状态

**1.1 本地版本控制系统**

利用简单的数据库来记录文件历次更新的差异<br/>
可以理解为在电脑上另存一个某文件的备份<br/>
这么做的缺点在于很容易混淆目录名称或是不小心覆盖掉备份文件

**1.2 集中式版本控制系统 Central Version Control System (CVCS)**

为了让不同电脑上的用户能协同工作<br/>
选定一个中央服务器<br/>
让不同用户通过连接到这台服务器进行版本的获取和更新
其缺点与本地版本控制系统相同，即数据保存在单一位置<br/>
一旦中央服务器受损，所有备份将完全消失.

**1.3 分布式版本控制系统 Distributed Version Control System (DVCS)**

客户端不止是获取项目的最新快照，而是把整个仓库都克隆到本地，包括完整的历史记录<br/>
这样一来用户可以凭借本地的仓库对远端服务器进行修复或更改.

> ###### <br> 在 git 仓库里文件一共有4中不同的状态:
> 1. **Untracked**: 此时git提交的文件快照中将不会有此文件
> 2. **Unmodified**: 存放着已被追踪但未被修改的文件
> 3. **Modified**: 又叫非暂存区，存放着已被追踪，已被修改，但在 git 仓库中尚未被暂存的文件
> 4. **Staged**: 又叫暂存区，存放着已被暂存的文件
<br>

## 1. Git 基础操作

#### 1.1 初始化

##### 1.1.1 新建一个仓库
>git init
###
##### 1.1.2 克隆（下载）一个现有的仓库
>git clone + url
##

#### 1.2检查版本状态

##### 1.2.1. 查看文件的跟踪和提交情况
> git status
##

#### 1.3 暂存和提交

##### 1.3.1. 将一个文件添加至暂存
> 

##### 1.3.2. 将暂存文件提交
> git commit
##

#### 1.4同步远程仓库

##### 1.4.1. 将已提交内容上传至远程仓库
> git push

##### 1.4.2. 获取远程仓库内容
> git pull

##### 1.4.3. 查看历史提交版本
> git log

##### 1.4.4. 恢复历史版本
> git reset --hard commit_id

##### 1.4.5. 查看指令历史，用来确定恢复到未来的哪个版本
> git relog

<br>

## 2. Git 进阶操作
#### 2.1 Git clone 非 master 分支的代码 (切换到指定 branch分支 或者 tag版本)

第一步: git 源代码到本地


> git clone git@github.com:AAAAgito/AGR-Src.git

<br>


第二步: 查看所有分支


> git branch -a

1 . 绿色的表示本地当前分支
2 . 红色的表示远程的分支
```
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/master
  remotes/origin/reset_API
  remotes/origin/server_connect
```
<br>


第三步: 切换到指定分支(比如说reset_API)

> git checkout reset_API
<br>

(For tag) 第二步：
> git tag
<br>

(For tag) 第三步：
> git checkout freenect-stack-0.2.2


