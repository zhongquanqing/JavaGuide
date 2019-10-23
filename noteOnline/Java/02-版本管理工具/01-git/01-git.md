# 第一章  Git基础命令
## 1.1.Git的概念
    版本控制工具（命令）
    git是一个开源的分布式版本控制系统；
    分布式版本控制系统；
    多人同步开发；
    中央服务器上存储着这些文件（即版本库）的当前版本和历史版本；
    工作区、版本库、暂存区；
    传统：集中式版本控制系统（例如CVS或Subversion）;
## 1.2.Git命令 
    Interceptor主要就是用来做请求的拦截。
    
### 1.2.1.git clone
    克隆远程仓库的项目
### 1.2.2.git checkout
    切换分支或恢复工作树文件（每次切换分支，项目代码，术语叫工作树(Working Tree)
    迁出一个分支的特定版本。默认是迁出分支的HEAD版本
### 1.2.3.git checkout as
    将..作为...，别名git checkout as guest（以***为别名，检出一个项目）Use a knife as a weapon(用一把刀做为武器）。checkout aaa as bbb.检出aaa分支作为bbb来使用
    checkout with rebase： 就是checkout的时候，同时使用rebase。rebase的用法，参考这里：http://gitbook.liuhui998.com/4_2.html
### 1.2.4. git checkout <file>
    撤消本地修改
    将内容从上次提交复制到工作目录
    git pull：进行pull拉取远程仓库的更新，同步更新最新版本到本地
### 1.2.5.暂存git stash 和git stash pop
    如果出现冲突，可以暂存git stash文件，然后进行冲突处理或先同步更新到本地，然后git stash pop：
    将所有的已开发的还原，再继续开发。
###1.2.6.git add
    将文件内容添加到索引(将修改添加到暂存区)；
    也就是将要提交的文件的信息添加到索引库中，等待commit提交；
    git commit：将修改（暂存区的文件）提交到本地库；
###1.2.7.git merge
    用于合并两个分支
    git merge b： # 将b分支合并到当前分支；
    git rebase b：也是把 b分支合并到当前分支（不保留分支记录）；  
    git stash：暂存修改的文件（保存当前工作状态，等开发人员bug提交以后再git stash pop）
    命令行：git stash save “修改的信息"；
    git stash pop：将所有的已开发的还原，再继续开发；
###1.2.8.git reset
    (将文件内容从上次提交复制到暂存区)
    git reset HEAD <file>：撤消暂存区修改
### 1.3.git cherry-pick 
    本地提交：git cherry-pick 版本号
    例子：git cherry-pick 03dc89085aea0d3025f3bdd67c36ae9f3d3dd8dc 