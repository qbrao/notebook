# git

## 参考资料
- [Git的奇技淫巧](https://github.com/521xueweihan/git-tips)
- [一个小时学会Git](https://www.cnblogs.com/best/p/7474442.html)
- [Git 使用基础篇](http://www.open-open.com/lib/view/open1332904495999.html)
- [Git 教程-廖雪峰](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)
- [Git 飞行规则](https://github.com/k88hudson/git-flight-rules/blob/master/README_zh-CN.md)
- [一份值得收藏的 Git 异常处理清单](https://juejin.im/post/5edcf3a36fb9a047fa04fbc3)
- [三年 Git 使用心得 & 常见问题整理](https://juejin.im/post/5ee649ff51882542ea2b5108)
- [Git配置多账号切换，GitLab和GitHub分开账户](https://zhuanlan.zhihu.com/p/105359388?utm_source=wechat_session)
- [git修改文件名大小写敏感问题解决](https://blog.csdn.net/chaiqunxing51/article/details/52055631)
- [在开发过程中使用 git rebase 还是 git merge，优缺点分别是什么？](https://www.zhihu.com/question/36509119/answer/1990894567)

## Git 经典图

![Git经典图](images/git%E7%BB%8F%E5%85%B8%E5%9B%BE.png)

- Workspace: 工作区
- Index / Stage: 暂存区
- Repository: 本地仓库
- Remote: 远程仓库

## branch

基于远程分支创建一个本地分支

```bash
git checkout -b 本地分支名 origin/远程分支名
```

切换分支

```bash
git checkout <name>
# 或者
git switch <name>
```

合并某分支到当前分支：

```bash
git merge <name>
```

查看分支：

```bash
# 查看本地分支。分支前面有 `*` 代表是当前分支。
git branch

# 查看远程分支
git branch -r

# 来查看所有分支，包括本地和远程分支。
git branch -a
```

推送分支到远程：

```bash
git push origin <name>
```

删除分支：

```bash
# 删除本地分支
git branch -d <name>

# 强制删除
git branch -D

# 删除远程分支
git push origin --delete <name>
```

## add

git add 的几种参数区别

```bash
# 保存所有的修改
git add -A

# 保存新的添加和修改，但是不包括删除
git add .

# 保存修改和删除，但是不包括新建文件
git add -u
```

## commit

**最近一次 `commit` 的代码有问题怎么办？**

```bash
git add 我是修改内容.txt
git commit --amend
```

**刚刚写的提交信息有问题，想修改怎么办？**

```bash
git commit --amend -m "新的提交信息"
```


## reset

今天 pull 代码后，打包出现问题，但是找不到出问题的具体文件。使用排除法来查找具体是哪次提交的问题。
先查看提交的 log，按字母 q 可以退出日志模式。

```bash
git log
```

回滚到具体的某个版本

```bash
git reset --hard e377f60e28c8b84158
```

强制提交

```bash
git push -f origin master
```

恢复版本到一个具体的状态，建议加上 `––hard` 参数，git 支持无限次回滚

```bash
# 回退到上一个版本：
git reset ––hard HEAD^

# 回退到上上一个版本：
git reset ––hard HEAD^^

# 回退到上N个版本：
git reset ––hard HEAD~N（N是一个整数）

# 回退到任意一个版本：
git reset ––hard 版本号（版本号用7位即可）
```

```bash
# 回滚到某个版本，并且 xxxxx hash之前的 commit 内容会被保留下来
git reset --soft xxxxxx
```

## stash

```bash
# 添加暂存栈
git stash

#或
git stash save -m '暂存'

# 查看暂存栈
git stash list

# 推出暂存栈
git stash pop

# 取出特定暂存内容
git stash apply stash@{1}

# 清空暂存区的所有内容
git stash clear

# 清除指定某一条暂存
git stash drop stash@{0}
```

> 注意：没有被 track 的文件（即从来没有被 add 过的文件不会被 stash 起来，因为 Git 会忽略它们。如果想把这些文件也一起 stash，可以加上 -u 参数，它是 --include-untracked 的简写。就像这样：git stash -u

## HEAD

HEAD 是一个对当前检出记录的符号引用 —— 也就是指向你正在其基础上进行工作的提交记录。

HEAD 总是指向当前分支上最近一次提交记录。大多数修改提交树的 Git 命令都是从改变 HEAD 的指向开始的。

HEAD 通常情况下是指向分支名的（如 bugFix）。在你提交时，改变了 bugFix 的状态，这一变化通过 HEAD 变得可见。

分离的 HEAD 就是让其指向了某个具体的提交记录而不是分支名。

## 解决冲突

`pull` 或者 `merge` 分支时，有时候会遇到这个界面，如下图：

![merge提示图](images/merge%E6%8F%90%E7%A4%BA%E5%9B%BE.png)

可以不管(直接下面 3,4 步)，如果要输入解释的话就需要:

1. 按键盘字母 `i` 进入 insert 模式。
2. 修改最上面那行黄色合并信息,可以不修改。
3. 按键盘左上角 `Esc`。
4. 输入 `:wq`，回车。

1、先将本地修改存储起来

```bash
git stash
# 这样本地的所有修改就都被暂时存储起来
```

使用 `git stash list` 可以看到保存的信息

2、pull 内容

```bash
git pull
```

3、还原暂存的内容

```bash
git stash pop stash@{0}
```

4、解决文件中冲突的的部分

其中 `Updated upstream` 和 `=====` 之间的内容就是 pull 下来的内容，`====` 和 `stashed changes` 之间的内容就是本地修改的内容。

碰到这种情况，git 也不知道哪行内容是需要的，所以要自行确定需要的内容。解决完成之后，就可以正常的提交了。

## `git warning: LF will be replaced by CRLF in` 解决办法

> LF 和 CRLF 分别是 MacOS 和 Windows 两个系统的默认换行符，建议在仓库中统一配置。

在使用 git 的时候，每次执行

```bash
git add .
```

都会提示这样一个警告消息：

```bash
warning: LF will be replaced by CRLF in XXXXXXXXXXXXXX.
```

虽然说没有什么影响，但是就是觉得太碍眼了，按照这样设置就没有问题了。

```bash
git config core.autocrlf false
```

这样设置 git 的配置后在执行 add 操作就没有问题了。