Git 基础
=======

创建别名
-------

使用 `git log` 查看提交历史，但是输出冗杂。通常使用
```sh
git log --oneline --abbrev-commit --all --graph --decoreate --color
```
来获得更美观易读的输出。但是每次输入这么多肯定很烦人，使用
```sh
git config --global alias.graph "log --graph --oneline --decorate=short"
```
增加一个全局别名，这个别名对于任何地方的 `git` 都适用。如此一来，键入 `git graph` 会等效于
```bash
git log --graph --oneline --decorate=short
```
样例输出：
```sh
$ git graph

* 36f2d65 (HEAD -> master, origin/master, origin/HEAD) Forget it
* 9b4a6d7 Update ref list
* 3931d4d Using relative path for image
* ba18821 Upload pics
* ceca69a fixed reference
* be15df2 fixed picture address
* 97a36f3 Initial commit
```

基本操作
-------

### 忽略已经添加的文件

```bash
git rm --cached <somefiles>
```

### 推送本地分支到远程

```bash
# 远程分支如果不存在，则自动创建。
git push origin <local_brach>:<remote_branch>
```

### 拉取远程分支到本地

```bash
# 从远程分支切换（并创建，如果不存在）本地分支
git checkout -b <local_branch> origin/<remote_branch>

# 另：取回远程分支并创建对应的本地分支，不换自动切换到该分支
git fetch origin <remote_brach>:<local_branch>
```

### 从另一个分支检出某个文件并重命名

有时候开了一个孤立分支，但是想参考其他分支的代码，而当前分支又有同名文件，此时就需要从其他分支检出文件并重命名。
```bash
# show the content of a.cpp in specific commit HEAD^
git show HEAD^:a.cpp

# that's done
git show HEAD^:a.cpp > b.cpp
```

Ref: search "git-checkout older revision of a file under a new name" in stack overflow

### 查看已被跟踪的文件

```bash
git ls-files
```

Ref: search "how can i make git show a list of the files that are being tracked" in stack overflow

### 合并某个文件到当前分支

例如当前在 master 分支，希望合并某个分支 dev 的某个或多个文件到当前分支：
```bash
git checkout dev file1 file2 ...
```
但是上述做法会强行覆盖当前分支的文件，没有冲突处理，更安全的做法是先从当前分支新建分支 master_temp，然后在 master_temp 中 checkout，最后再将 master_temp 分支 merge 到 master 分支：
```bash
# Create a branch based on master
git checkout -b master_temp

# Chechkout file1 from dev to master_temp
git checkout dev file1
git commit -m "checkout file1 from dev"

# Switch to master and merge, then delete
git checkout master
git merge master_temp
git branch -d master_temp
```

Ref: https://segmentfault.com/a/1190000008360855

### 如何撤销本地 commit

有时候本地 add 了一写 diff，随手 commit 了，接着又有些 diff 可以共用这个 commit，就想撤销刚刚的 commit，把所有的 diff 合并在一起作为一次 commit。
```bash
# for more info, type git reset -h
git reset --soft <commit_id>
```

### 修改已提交的 commit message

```bash
# commit_id 至少比要修改的那个 commit 早一个版本
git rebase -i <commit_id>

# 列出 rebase 的 commit 列表，不包含 <commit id>
$ git rebase -i <commit id>
# 最近 3 条
$ git rebase -i HEAD~3
# 本地仓库没 push 到远程仓库的 commit 信息
$ git rebase -i

# vi 下，找到需要修改的 commit 记录，`pick` 修改为 `edit` 或 `e`，`:wq` 保存退出
# 或者较新版本的 `reword`
# 重复执行如下命令直到完成
$ git commit --amend --message="modify message by daodaotest" --author="jiangliheng <jiang_liheng@163.com>"
$ git rebase --continue

# 中间也可跳过或退出 rebase 模式
$ git rebase --skip
$ git rebase --abort

# 如果只是更改 last commit
git commit --amend
```

Cf. [github doc](https://docs.github.com/zh/pull-requests/committing-changes-to-your-project/creating-and-editing-commits/changing-a-commit-message).

删除 commit 历史
---------------

如果不小心将隐私信息推送至远程仓库（如 github），那么仅仅删除再更新再推送到远程仓库覆盖是不够的，别人还是可以通过你的 commit 历史查到你所做的更改，所以这种情况下必须删除之前所有的 commit history. 大致思路是创建一个孤立分支，然后重新添加文件，再删除 master 分支，将新建的分支重命名为 master，再推送到远程强制覆盖 [^a]。
```bash
# Check out to a temporary branch:
git checkout --orphan TEMP_BRANCH

# Add all the files:
git add -A

# Commit the changes:
git commit -am "Initial commit"

# Delete the old branch:
git branch -D master

# Rename the temporary branch to master:
git branch -m master

# Finally, force update to our repository:
git push -f origin master
```

[^a]: https://gist.github.com/heiswayi/350e2afda8cece810c0f6116dadbe651


Git merge
---------

当你觉得很多时候对于一个命令的很多子命令或者选项不是很清晰，而且查了忘，忘了查，那多半是你不理解它的工作机制。或者说它对你来说不是那么自然易懂，这个时候就需要深入以下，了解以下它的基本原理，帮助自己理解，以便记忆。

`git merge`就是如此，你要知道 merge 的含义是什么？它其实就是在被 merge 的分支上重现要 merge 的 commits. 比如说：
```
a---b---c---d---e (master)
    \
     `--A---B---C (dev)
```
你当前在 master 分支的 e 节点，你要 merge dev 分支。其实就是将 A、B、C 三个 commit 在 master 分支上重现，仿佛 master 分支上曾经也做过这些改动。那么冲突的来源就是你在两个分支中，对同一个文件作了不同的改动，如何解决不言而喻。

小朋友，你是否有很多？

Q: 我想只重现 B 节点怎么办？<br />
A: `git checkout master && git cherry-pick 62ecb3`，这里`62ecb3`是节点 B 的 commit 标识。

Q: 我想重现 A-B，但不要 C 怎么办？<br />
A: `git checkout -b newbranch 62ecb3 && git rebase --onto master 76cada^`，这里`76cada`是 A 节点的 commit 标识。先基于 B 创建一个分支，这个分支包含了 A 节点的改动，然后 rebase 到 master 上去，结果就是 A 和 B 重现在 master 分支上。

Ref:

1. https://stackoverflow.com/questions/161813/how-to-resolve-merge-conflicts-in-git
2. [Cherry-Picking specific commits from another branch](https://www.devroom.io/2010/06/10/cherry-picking-specific-commits-from-another-branch/)

Git rebase
----------

Cf. https://www.atlassian.com/git/tutorials/rewriting-history/git-rebase

`rebase`和`merge`都是将另一分支的提交（commit）集成到当前分支的方法。而 merge 会保留两条分支的所有 commit，然后解决冲突，然后形成一个 merge commit，从 git log 上来看，原本线性的提交历史分了叉，然后又合了并。而 rebase 则是基于当前分支的某次提交去重现另一个分支，rebase 之后依然能够保留提交历史的线性状态。

```
a---b---c---d---e (master)
    \
     `--A---B---C (dev)
```
> From a content perspective, rebasing is changing the base of your branch from one commit to another making it appear as if you'd created your branch from a different commit. Internally, Git accomplishes this by creating new commits and applying them to the specified base. It's very important to understand that even though the branch looks the same, it's composed of entirely new commits.
>
> The primary reason for rebasing is to maintain a linear project history. For example, consi der a situation where the main branch has progressed since you started working on a feature branch. You want to get the latest updates to the main branch in your feature branch, but you want to keep your branch's history clean so it appears as if you've been working off the latest main branch.
>
> You have two options for integrating your feature into the main branch: merging directly or rebasing and then merging. The former option results in a 3-way merge and a merge commit, while the latter results in a fast-forward merge and a perfectly linear history. The following diagram demonstrates how rebasing onto the main branch facilitates a fast-forward merge.
>
> Rebasing is a common way to integrate upstream changes into your local repository. Pulling in upstream changes with Git merge results in a superfluous merge commit every time you want to see how the project has progressed. On the other hand, rebasing is like saying, “I want to base my changes on what everybody has already done.”

注：写这个的时候，我自己对 rebase 的理解也很模糊。

任何时候不清楚的时候请终止 rebase:
```bash
git rebase --abort
```
反复操练几次，git 有友好的提示信息。

### 撤销上次 rebase

```bash
# 先使用 reflog查看分支变动历史
$ git reflog

# 选中rebase前的commit id (hash)
$ git reset --hard <commit_id>
```

参考：[此处](https://juejin.cn/s/git%20%E6%92%A4%E9%94%80rebase)。

Fork 之后如何同步 fork 源的更新
-----------------------------

```bash
# see remote status
git remote -v

# add upstream if not exist one
git remote add upstream https://github.com/<origin_owner>/<origin_repo>.git
git remote -v
```
从上游仓库 fetch 分支和提交点，提交给本地 master，并会被存储在一个本地分支 upstream/master
```bash
git fetch upstream
```
切换到任意分支，merge 已经 fetch 的分支即可：
```bash
git checkout somebrach
git merge upstream/master
```

see: https://www.zhihu.com/question/28676261

Ref:

1. [Configureing a remote for a fork](https://docs.github.com/en/github/collaborating-with-issues-and-pull-requests/configuring-a-remote-for-a-fork)
2. [Syncing a fork](https://docs.github.com/en/github/collaborating-with-issues-and-pull-requests/syncing-a-fork)


Git submodule
-------------

git submodule 本质上是指向一个其他仓库的链接，默认 clone 不会将 submodule 对应的仓库克隆下来。
```bash
# help
git submodule --help

# 添加 submodule
#   1. 进入目标子文件夹
git submodule add https://github.com/imtianx/liba.git

# 更新 submodule
cd xxx
git pull
git submodule update --recursive

# 在主目录下更新 submodule liba
git submodule update --remote liba

# 删除 submodule
vim .gitmodules # 删除相应条目
vim .git/config # 删除相应条目
rm -rf .git/modules/liba # 删除对应的 git 文件夹

# 在克隆时连同 submodule 一并克隆
git clone https://github.com/imtianx/MainProject.git --recursive
# is equivalent to
git clone https://github.com/imtianx/MainProject.git
git submodule init
git submodule update
```

一般地，当某仓库中包含 submodule ./dir1 时，如果你只提交了 dir1 的内容，那么当前仓库是不会用上最新版本的 dir1 的。这在远程仓库中尤为显著。我的博客文件夹 BlogHugo 中包含了 themes/even 的 submodule, 每当我在 even 中改完样式推送到远端后（这里我 BlogHugo 仓库没有任何修改），发现 build 出来的网站压根没有使用最新的 submodule 里面的内容。究其原因，其实是父仓库默认会跟踪 submodule 的一个版本号。如果不在父仓库中显示更新要跟踪的版本号，则父仓库一直会跟踪之前的版本号。这是合理的，因为父子仓库独立开发，为了避免子仓库（submodule）的频繁提交对父仓库的构建产生影响，所以默认会跟踪一个版本号。

正确的做法是，当 submodule 更新后，父仓库中 submodule 的版本号会产生一个修改，在父仓库中 add-commit 这个修改，就可以更新父仓库中引用的 submodule 版本号。

Ref:

1. [Git-工具 - 子模块](https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E5%AD%90%E6%A8%A1%E5%9D%97)
2. [Git 子模块：git submodule](https://juejin.im/post/6844903572950401038)
