---
title: git使用
date: 2023-2-7
tag: 
    - tools 
categories: 
    - backend
---
<!-- more -->



推荐

[玩游戏学git](https://learngitbranching.js.org/?locale=zh_CN)

[git命令小贴士](https://training.github.com/downloads/zh_CN/github-git-cheat-sheet/)

[github官方文档](https://docs.github.com/zh/authentication/troubleshooting-ssh/error-permission-denied-publickey)

[gitignore文档](https://github.com/github/gitignore)

精通Git（第2版）Soctt Chacon    Ben Straub

[linux命令](https://wangchujiang.com/linux-command/c/tree.html)
>打消学习兴趣的一个因素是网络连接问题，等弄明白，精力也耗没了 

## 基础命令

`git staus -s `

- 左边标记：是否暂存
- 右边标记：是否修改

```Git
 M readme.md
?? latex-tutorial-chinese-master/
```

`git rm --cached readme.md`

- 从暂存区移除。和`git restore --staged <file>`很像
- (因为--cached和--staged是同义词)

`git mv from.md to.md` 重命名，相当于3条指令

```Git
$ mv from.md to.md
$ git rm from.md
$ git add to.md
``` 

`git diff` 

- 不带参数：工作目录的内容和暂存区比较
- 带参数：暂存区和上一次提交比较 eg：`git diff --stage(或者--cached)`

`git log --pretty="%h - %an,%ar : %s"`

- 暂时自定义格式

`git log --pretty=format:"%h - %an,%ar : %s"`

- 永久自定义输出格式

|命令|说明|
|:----|:---|
|git commit -m "test of git commit message"|
|git commit -a -m "change something"|跳过git add直接提交
|git log -p -2|查看最近2次提交所引入的差异（-2 -p没有顺序要求）|
|git log --stat|查看统计信息|
git log nameA nameB --not nameC|在A或B中，但不在C中的提交


## 远程仓库

查看远程

|命令|说明|
|:----|:---|
|git remote -v||
|git remote show gitexer|
|git remote add gitexer git@github.com:beautifulwq/gitexercise.git|

## 远程操作

|命令|说明|
|:----|:---|
|git fetch gitexer|把本地没有的取过来，但是不合并
|git pull|
|git push remote-name branch-name|本地到远程
|git remote rename old new|同时会改远程分支名称
|git remote rm |

## tag

|命令|说明|
|:----|:---|
git tag|查看标记
git tag -a v0.1 |打标记
git show v0.1|show
git tag -a v0.09 a3nb3ah1(校验和)| 补加标记
git push gitexer v0.1|推送标签
git push gitexer --tags|推送所有标签

## 别名

|命令|说明|
|:----|:---|
git config --global alias.st status | git st
git config --global alias.last 'log -1 HEAD'| git last

## 分支

|命令|说明|
|:----|:---|
git branch|查看分支
git branch -v|
git branch --merged|查看已经合并了的分支
git branch --no-merged|查看没合并的分支
git checkout|切换分支
git branch testing|创建分支
git checkout -b hotfix|相当于git branch hotfix git checkout hotfix
git branch -d hotfix|删除branch

!!! info "git checkout && git switch"
    
    [外部链接](https://juejin.cn/post/7211316525512507447)


## 变基 rebase

[变基时的 `reword` 和 `edit`的区别](https://stackoverflow.com/questions/30194796/git-rebase-whats-the-difference-between-edit-and-reword)

- `reword` 只改变commit的信息，不改变文件的修改内容
- `edit` 两者都改变

好处挺多，建议看书上的图。**推荐在本地变基，但是不要对已经推送到远程服务器的提交进行变基。**

我的理解是可以把并行的分支合并成看起来线性进行的，更加人性化

`git pull --rebase`可能会解决有人在远程变基，你又要拉取的问题 


## git 交互式
|命令|说明|
|:----|:---|
git add -i| 进入交互式


### 补丁 patch
git add -patch|进入补丁模式或者先进交互式再进补丁模式

### 储藏 stash
|命令|说明|
|:----|:---|
git stash| 储藏
git stash list|查看储藏
git stash apply| 应用最新的一次,也可以加参数来指定某一次
git stash drop stash@{0}|删除储藏
**git stash --keep-index** |不储藏已经git add的内容
**git stash --include-untracked** |同时储藏未跟踪的文件
**git stash -u**|同上
**git stash branch newbrname** |从储藏创建分支（省时省力）

### 清理工作目录 clean
|命令|说明|
|:----|:---|
git clean | 删除所有未跟踪的文件
git clean -d -n|预览会删除哪些 
git clean -x |连.gitignore里忽略的文件也会删除

!!! info -n
    - 可以先用`-n`参数先预览删除哪些文件
    - 可以用`-i`参数交互式地删除

## 修改提交信息
|命令|说明|
|:----|:---|
git commit --amend|修改最近一次提交信息，但是会改变sha1值，类似一次微型变基

`git cherry-pick`选择性的rebase

??? info ":x 和 :wq 的区别"

    (1) :wq 强制性写入文件并退出（存盘并退出 write and quite）。即使文件没有被修改也强制写入，并更新文件的修改时间。
    (2) :x 写入文件并退出。仅当文件被修改时才写入，并更新文件修改时间；否则不会更新文件修改时间。
    这两者一般情况下没什么不一样，但是在编程方面，对编辑源文件可能会产生重要影响。因为文件即使没有修改，":wq" 强制更新文件的修改时间，
    这样会让 make 编译整个项目时以为文件被修改过了，然后就得重新编译链接生成可执行文件。这可能会产生让人误解的后果，当然也产生了不必要的系统资源花销。不过像是版本控制的软件一般首选还是比较文件内容，修改时间一般不加以理会。



