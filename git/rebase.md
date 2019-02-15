## rebase
貌似之前我理解错了 rebase 的意思，rebase 的前提是不要 rebase 已经提交到远程仓库的 commit。

## 使用场景
1. 开发新版本 -> 拉 feature 分支 -> 根据需求 / bug 本地拉分支 （dev / hotfix）-> 不同分支开发

2. 阶段性开发完成 -> 切回 feature 分支 -> rebase 分支 dev 和 hotfix -> rebase -i 选择需要合并的 commit -> 完成
