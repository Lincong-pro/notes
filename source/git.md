# Git技术学习

**其实也不是闲着没得事情干，很多东西只有自己从实践中总结之后这个才会变成自己的东西！**

## 分支管理

### 远程仓库地址管理

#### 远程地址添加

> 意外着我可以将一个项目添加多个远程的仓库地址，只不过需要注意的一点就是git push后面需要追加origin之类的仓库地址。

```shell
# 其中翻译为中文就是远程添加origin的仓库地址，其链接为xxxx.git
git remote add origin xxxx.git
```

#### 远程仓库地址删除

```shell
# 其中翻译为中文就是远程添加origin的仓库地址，其链接为xxxx.git
git remote add origin xxxx.git
```

### 创建本地分支

```shell
# 完整写法
git branch -M localBranchName
# 简写形式
git branch localBranchName
```

### 创建远程分支

> 注意如果想要创建远程分支，并不需要提前在本地创建分支，然后再使用如下命令

```shell
# 完整写法
git push origin --set-upstream remoteBranchName
# 简写形式
git push origin -u remoteBranchName
```

### 删除本地分支

```shell
# 完整写法
git branch --delete localBranchName
# 简写形式
git branch -d localBranchName
```

### 删除远程分支

```shell
# 完整写法
git push origin --delete remoteBranchName
# 简写形式
git push origin -d remoteBranchName
```

