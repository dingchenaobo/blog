## git
最常用的基本git操作就是：

```git add .``` -> ```git commit -m "xxx"``` -> ```git push```。

基本概念也清楚，```add``` 工作区存入暂存区，```commit``` 存入本地仓库，```push``` 推向远程仓库，那就是有这么几个区域：
* Workspace：工作区。
* Index：暂存区。
* Repository：本地仓库。
* Remote：远程仓库。

![基本流程](https://img.alicdn.com/tfs/TB1GQcEGb2pK1RjSZFsXXaNlXXa-1000-290.webp)

## 初始化本地仓库（拉取远程仓库到本地）
一般都是先在平台上（gitlab、github、gitea等）创建远程仓库，然后本地拉取：
```git
git clone <url>
```
拉取一般分两种：```https``` 和 ```ssh```。
* https: 需要平台账号密码。
* ssh: 需要在平台上配置 ssh 公钥 (rsa.pub)，git 生成。
拉取完成即本地仓库初始化完成，会多一个 ```.git``` 文件夹，看不到设置隐藏文件可见。

## 初始化本地仓库（初始化本地仓库连接远程）
```git
git init
```
```init``` 后就初始化了一个本地仓库，生成了一个 ```.git``` 文件夹，添加个占位文件 ```.gitkeep```，然后提交到本地仓库：
```git
git add .
git commit -m "feat: .gitkeep"
```
连接远程仓库：
```git
git remote add origin <url>
```
将本地master分支同步到远程master分支
```
git push --set-upstream origin master
# 或者
git push -u origin master
```
```--set-upstream``` 和 ```-u``` 是一样的，参考：
* [What is the exact difference between “git push -u” and “git push --set-upstream”?](https://stackoverflow.com/questions/53407243/what-is-the-exact-difference-between-git-push-u-and-git-push-set-upstream)
* [文档](https://git-scm.com/docs/git-push#git-push--u)

## 提交到暂存区
* git add . ：添加当前目录所有的文件都进入暂存区。
* git add < dir >：添加指定目录所有的文件都进入暂存区。
* git add < filename >：添加指定文件进入暂存区。

最常用的就是 ```git add .``` ，基本上都是用这个。

> 从暂存区回退到工作区: ```git reset```，和 add 的命令是相对的，.、< dir >、< filename >，意思也是相对的，将 全部 \ 某一目录下的所有文件 \ 某一文件 从暂存区回退到工作区。

## 查看暂存区和工作区状态
```git
git status
```
![截图](https://img.alicdn.com/tfs/TB1yTXXGwDqK1RjSZSyXXaxEVXa-1622-1086.jpg)
结果一般最多为三组：
* Changes to be committed：已在暂存区，待提交到本地仓库。
* Changes not staged for commit：有修改, 但是没有被添加到stage区(就是暂存区，下面有说明)的文件。
* Untracked files：没有tracked过的文件, 即从没有add过的文件。

> ```staged``` 和 ```index``` 其实都是暂存区 ，[文档中有提到过](https://git-scm.com/book/en/v2/Getting-Started-Git-Basics)：The staging area is a file, generally contained in your Git directory, that stores information about what will go into your next commit. Its technical name in Git parlance is the “index”, but the phrase “staging area” works just as well.


