## git
最常用的基本git操作就是：

```bash add .``` -> ```bash commit -m "xxx"``` -> ```bash push```。

基本概念也清楚，```add``` 工作区存入暂存区，```commit``` 暂存区存入本地仓库，```push``` 本地仓库推向远程仓库，那就是有这么几个区域：
* Workspace：工作区。
* Index：暂存区。
* Repository：本地仓库。
* Remote：远程仓库。

![基本流程](https://img.alicdn.com/tfs/TB1GQcEGb2pK1RjSZFsXXaNlXXa-1000-290.webp)

## 初始化本地仓库（拉取远程仓库到本地）
一般都是先在平台上（gitlab、github、gitea等）创建远程仓库，然后本地拉取：
```bash
git clone <url>
```
拉取一般分两种：```https``` 和 ```ssh```。
* https: 需要平台账号密码。
* ssh: 需要在平台上配置 ssh 公钥 (rsa.pub)，git 生成。
拉取完成即本地仓库初始化完成，会多一个 ```.git``` 文件夹，看不到设置隐藏文件可见。

## 初始化本地仓库（初始化本地仓库连接远程）
```bash
git init
```
```init``` 后就初始化了一个本地仓库，生成了一个 ```.git``` 文件夹，添加个占位文件 ```.gitkeep```，然后提交到本地仓库：
```bash
git add .
git commit -m "feat: .gitkeep"
```
连接远程仓库：
```bash
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

最常用的就是 ```bash add .``` ，基本上都是用这个。

> 从暂存区回退到工作区: ```bash reset```，和 add 的命令是相对的，.、< dir >、< filename >，意思也是相对的，将 全部 \ 某一目录下的所有文件 \ 某一文件 从暂存区回退到工作区。

## 查看暂存区和工作区状态
```bash
git status
```
![截图](https://img.alicdn.com/tfs/TB1yTXXGwDqK1RjSZSyXXaxEVXa-1622-1086.jpg)
结果一般最多为三组：
* Changes to be committed：已在暂存区，待提交到本地仓库。
* Changes not staged for commit：有修改, 但是没有被添加到stage区(就是暂存区，下面有说明)的文件。
* Untracked files：没有tracked过的文件, 即从没有add过的文件。

> ```staged``` 和 ```index``` 其实都是暂存区 ，[文档中有提到过](https://git-scm.com/book/en/v2/Getting-Started-Git-Basics)：The staging area is a file, generally contained in your Git directory, that stores information about what will go into your next commit. Its technical name in Git parlance is the “index”, but the phrase “staging area” works just as well.

## 暂存区提交到本地仓库
```bash
git commit -m <message>
```
```message``` 中一般填写本次提交的原因/目的。如果单行不够写，可以直接 ```bash commit``` 进入文本编辑。虽然理论上说，commit可以随便写，但是一般遵守的是 angular 的 commit 规范
> [AngularJS Git Commit Message Conventions](https://docs.google.com/document/d/1QrDFcIiPjSLDn3EL15IJygNPiHORgU1_OOAqWjiDU5Y/edit#heading=h.em2hiij8p46d)

根据规范，一次 commit 应该是这样的：
```
<type>(<scope>): <subject>
<BLANK LINE>
<body>
<BLANK LINE>
<footer>
```
1. ```type```：就是这次 commit 的类型，```必填```，包括：
* feat：新功能(feature)
* fix：修复bug(bug fix)
* docs：文档(documentation)
* style：样式，不是指css样式，而是代码风格(formatting, missing semi colons, …)
* refactor：重构（既不是新功能，也不是修复 bug）
* test：添加测试(when adding missing tests)
* ~~chore：小改动，这个其实会出现歧义，阮大翻译的是 ```构建过程或辅助工具的变动``` (maintain)~~
* build: 影响系统构建和外部依赖（webpack、npm、gulp 等）
* perf: 性能提升
* ci: 持续集成相关（持续集成脚本等）
2. ```scope```：用于说明 commit 影响的范围，比如数据层、控制层、视图层等等，视项目不同而不同，```非必填```。
3. ```subject```：简短地描述，可以理解为 title，以动词开头，```必填```。
4. ```BLANK LINE```：空一行。
5. ```body```：对这次 commit 的详细描述，```非必填```，尽量详细。
6. ```footer```：只在两种情况下使用
* 不兼容变动：以 ```BREAKING CHANGE``` 开头。
* 关闭 issue：Closes #id

例子：
```bash
# commit 的内容
feat: create some file

Some describe for this commit
```

## 版本回退（reset）
按步骤回退：
```bash
# 回退到上次提交
git reset head~1
# 回退上上次提交
git reset head~2
.
.
.
```
回退到某个版本：
```bash
# 回退到上次提交
git reset --hard 3aa3e5ae7fa592de05e2662ef1316d3985e281d6
```
```--hard``` 参数为reset的一种模式，表示从当前到指定的 commit 所有数据全部丢弃，直接回到指定的 commit 版本。
还有 ```--mixed``` 和 ```--sort```。

## 本地仓库提交到远程仓库
* git push
* git push --set-upstream origin < brnachname > / git push -u origin < brnachname > 提交新分支
* git push --force 强制提交

## 参考
* [Commit message 和 Change log 编写指南](http://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html)

* ~~[AngularJS Git Commit Message Conventions](https://docs.google.com/document/d/1QrDFcIiPjSLDn3EL15IJygNPiHORgU1_OOAqWjiDU5Y/edit#heading=h.4a9jrkze4u4a)，里面有一些符合规范的 commit 的例子。~~
新版本 angular 做了一点的修改：[Contributing to Angular](https://github.com/angular/angular/blob/master/CONTRIBUTING.md#contributing-to-angular)

* [git reset回滚代码](https://juejin.im/post/5b87e75d6fb9a019d74769a6)
