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

### 合并分支

> 现在主流的合并分支的方法有两种，一种是采用**git merge**方法进行合并，另外一种采用**git rebase**方法进行合并

#### git merge合并分支

> 从下面的步骤中其实我们是可以看到其中使用该命令进行合并分支的缺点，即当分支被删除之后，分支的提交信息也被删除了。最后仅仅留下一个git merge的commit合并信息在主分支上，然而这并没有什么实际的帮助。

1. ```shell
   git merge subbranch # 合并需要进行合并的分支
   ```

2. ```shell
   # 使用gitkraken软件可视化进行跟踪修改
   ```

3. ```shell
   git add .&&git commit -m "description" 
   ```

4. ```shell
   git push origin master # 将合并信息推送到master主线上
   ```

#### git rebase合并分支

> 其实，仔细看看下面的步骤不难发现，该方法是没有**git commit**无用的合并信息，同时将子开发成员的commit信息都保存在了主开发线上。但是缺点就是解决冲突随着脱离主分支的时间越久，修改冲突实际上是越复杂的。

1. ```shell
   git rebase masterBranch # 将该分支合并到主分支上，即从该分支分离master的地点合并进入，同时分支的所有commit 信息都会保存到master分支上
   ```

2. ```shell
   # 使用gitkraken软件进行冲突编辑
   ```

3. ```shell
   git add . #保存所有修改的文件
   ```

4. ```shell
   git rebase --continue
   ```

5. ```shell
   git push # 推送到主分支
   ```

   * 注意：rebase实际上影响的是当前分支和主分支

## 合并远程仓库作为自己的子仓库

### 添加远程仓库作为子模块

```shell
git submodule add https://github.com/alicevision/AliceVision.git src/dependencies/AliceVision
```

## 仓库文件操作

### 远程分支下的文件操作

#### 删除远程分支下的指定文件夹

> 仅仅删除远程分支文件，不删除本地文件

```shell
git rm -r --cached directoryname
git commit -m "delete remote directory"
git push -u origin current_branch_name
```

#### 删除远程文件

```shell
git rm --cached filename
git commit -m "delete remote file"
git push -u origin current_branch_name
```

### 本地分支下的文件操作
