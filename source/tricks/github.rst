

## Git 

Git是非常强大的版本控制工具。Github是一个使用Git技术托管代码的网站。Android系统核心就是用Git进行管理的。

请大家阅读以下资料（欢迎补充），熟悉Git的一些基本概念和基本操作。



这里只是一部分，用Google搜索能`Github入门` `Git入门`能得到更多资料

- [学习Git分支](http://pcottle.github.com/learnGitBranching/?demo)
- [git - 简易指南](http://rogerdudler.github.com/git-guide/index.zh.html)
- [GotGitHub](http://www.worldhello.net/gotgithub/)
- [Pro Git](http://vdisk.weibo.com/s/vnApv)

##需要掌握的Git命令

Git有100多个命令。。。。不一定全部要学会，但是以下命令，希望大家能掌握：

### 提交一个版本
- git init 初始化目录
- git add . 把所有文件加入Git文件系统
- git commit 提交一个版本

### 上传到Github
- 在Github上创建一个git仓库或者fork一个仓库
- git clone 将github上的仓库克隆到本地
- git push 将本地仓库上传到Github

### 同步团队代码
- git add remote upstream http://xxxxxxxxx 增加远程upstream仓库的地址
- git fetch 从远程仓库拉取代码
- git pull 从远程仓库拉取代码并与当前自己本地代码合并

### 进行分支操作
- git checkout branchname 新建分支
- git merge branchname 将当前分支与另外一个分支合并

### 恢复/查看以前版本
- git reset XXXX  恢复以前版本
- git log 查看日志


## 国内Git服务提供商


觉得使用Github有困难的同学可以尝试使用一下国内的Git服务提供商的网站，先熟练使用Git：


- https://gitcafe.com
- https://gitshell.com
- https://geakit.com/

##关于命令行学习

虽然大家大多使用Windows系统，但想充分发挥Git的威力，还是需要需要一些命令的。打开Git-Bash就可以执行那些命令了。



