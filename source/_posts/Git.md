---
title: Git
date: 2019-05-08 10:15:09
tags: Git
---

### 服务器上的Git
Git 可以使用四种主要的协议来传输资料:本地协议(Local)，`HTTP` 协议(`https://`)，`SSH`(`Secure Shell`)协议(`user@server:path/to/repo.git`)及 `Git` 协议(`git://`)。

### .gitignore
* 所有空行或者以 # 开头的行都会被 `Git` 忽略
* 可以使用标准的 `glob` 模式匹配
* 匹配模式可以以(`/`)开头防止递归
* 匹配模式可以以(`/`)结尾指定目录
* 要忽略指定模式以外的文件或目录，可以在模式前加上惊叹号(`!`)取反

所谓的 `glob` 模式是指 `shell` 所使用的简化了的正则表达式
* 星号(`*`)匹配零个或多个任意字符
* [abc] 匹配任何一个列在方括号中的字符(这个例子要么匹配一个 a，要么匹配一个 b，要么匹配一个 c)
* 问号(`?`)只匹配一个任意字符
* 如果在方括号中使用短划线分隔两个字符，表示所有在这两个字符范围内的都可以匹配 (比如 [0-9] 表示匹配所有 0 到 9 的数字)
* 使用两个星号(*) 表示匹配任意中间目录，比如`a/**/z` 可以匹 配 a/z, a/b/z 或 `a/b/c/z`等。

#### Example:
1. 非 `.a` 结尾的文件(no .a files)
```
*.a
```
2. 跟踪 `lib.a` 文件，即使你忽略以`.a` 结尾的文件(but do track lib.a, even though you're ignoring .a files above)
```
!lib.a
```
3. 忽略当前目录下的 `TODO` 下的文件，子目录下的不忽略(only ignore the TODO file in the current directory, not subdir/TODO)
```
/TODO
```
4. 忽略所有 `build` 目录下的文件(ignore all files in the build/ directory)
```
build/
```
5.  忽略所有 `doc` 目录下一级以 `.txt` 结尾的文件(ignore doc/notes.txt, but not doc/server/arch.txt)
```
doc/*.txt
```
6. 忽略所有 `doc` 目录下以 `.pdf` 结尾的文件(ignore all .pdf files in the doc/ directory)
```
doc/**/*.pdf
```
[查看更多 `.gitignore` 示例](https://github.com/github/gitignore)

### `git config`
|命令|含义|
|:-|:-|
|git config --list|列出所有 Git 当时能找到的配置| 
|git config <key>|检查 Git 的某一项配置|
|git help config|获得 config 命令的手册|

### `git clone`
克隆远程仓库，`aaa1` 是自定义本地仓库的名字，可忽略。
```shell
git clone https://github.com/aaa/aaa aaa1
```

### `git branch`
提交对象的 master 分支。它会在每次的提交操作中自动向前移动。
`Git` 的 “master” 分支并不是一个特殊分支。它就跟其它分支完全没有区别。之所以几乎每一个仓库都有 `master` 分支，是因为 `git init` 命令默认创建它，并且大多数人都懒得去改动它。
`git branch -v` 查看每一个分支的最后一次提交，`--merged` 与 `--no-merged` 这两个有用的选项可以过滤这个列表中已经合并或尚未合并到当前分支的分支。
`git branch -d` 删除分支，如果分支包含了还未合并的工作，尝试使用 `git branch -d` 命令删除它时会失败，如果真的想要删除分支并丢掉那些工作，如同帮助信息里所指出的，可以使用 `-D` 选项强制删除它。
可以通过 `git ls-remote (remote)` 来显式地获得远程引用的完整列表，比如 `git ls-remote origin`，或者通过 `git remote show (remote)` 获得远程分支的更多信息。
设置已有的本地分支跟踪一个刚刚拉取下来的远程分支，或者想要修改正在跟踪的上游分支，你可以在任意时间使用 `-u` 或 `--set-upstream-to` 选项运行 `git branch` 来显式地设置（`git branch -u origin/serverfix`）。
如果想要查看设置的所有跟踪分支，可以使用 `git branch` 的 `-vv` 选项。这会将所有的本地分支列出来并且包含更多的信息，如每一个分支正在跟踪哪个远程分支与本地分支是否是领先、落后或是都有。
需要重点注意的一点是这些数字的值来自于你从每个服务器上最后一次抓取的数据。这个命令并没有连接服务器，它只会告诉你关于本地缓存的服务器数据。如果想要统计最新的领先与落后数字，需要在运行此命令前抓取所有的远程仓库。可以像这样做: `git fetch --all`; `git branch -vv`
`git push origin --delete serverfix`：删除远程分支

### `git status`
如果你使用 `git status -s` 命令或 `git status --short` 命令，你将得到一种更为紧凑的格式输出。
```shell
git status -s
   M README
  MM Rakefile
  A  lib/git.rb
  M  lib/simplegit.rb
  ?? LICENSE.txt
```
新添加的未跟踪文件前面有 ?? 标记。
新添加到暂存区中的文件前面有 A 标记。
修改过的文件前面有 M 标记，M 有两个可以出现的位置，出现在右边的 M 表示该文件被修改了但是还没放入暂存区，出现在靠左边的 M 表示该文件被修改了并放入了暂存区。

### `git rm`
要从 `Git` 中移除某个文件，就必须要从已跟踪文件清单中移除(确切地说，是从暂存区域移除)，然后提交。 可以用 `git rm` 命令完成此项工作，并连带从工作目录中删除指定的文件，这样以后就不会出现在未跟踪文件清单中了。
如果只是简单地从工作目录中手工删除文件，运行 `git status` 时就会在 “Changes not staged for commit” 部分(也就是 未暂存清单)看到删除文件。需要再运行 `git rm` 记录此次移除文件的操作。
如果删除之前修改过并且已经放到暂存区域的话，则必须要用 强制删除选项 -f(译注:即 force 的首字母)。 这是一种安全特性，用于防止误删还没有添加到快照的数据， 这样的数据不能被 `Git` 恢复。
我们想把文件从 `Git` 仓库中删除(亦即从暂存区域移除)，但仍然希望保留在当前工作目录 中。 换句话说，你想让文件保留在磁盘，但是并不想让 `Git` 继续跟踪。 当你忘记添加 `.gitignore` 文件，不小 心把一个很大的日志文件或一堆 `.a` 这样的编译生成文件添加到暂存区时，这一做法尤其有用。 为达到这一目 的，使用 `--cached` 选项：
`git rm --cached README`
`git rm` 命令后面可以列出文件或者目录的名字，也可以使用 `glob` 模式。比方说：
`git rm log/\*.log`
注意到星号 `*` 之前的反斜杠 `\`， 因为 Git 有它自己的文件模式扩展匹配方式，所以我们不用 `shell` 来帮忙展开。 此命令删除 `log/` 目录下扩展名为 `.log` 的所有文件。 

### `git checkout`
要特别注意的一点是当抓取到新的远程跟踪分支时，本地不会自动生成一份可编辑的副本(拷贝)。 换一句话说，这种情况下，不会有一个新的 `serverfix` 分支 - 只有一个不可以修改的 `origin/serverfix` 指针。
可以运行 `git merge origin/serverfix` 将这些工作合并到当前所在的分支。 如果想要在自己的 `serverfix` 分支上工作，可以将其建立在远程跟踪分支之上:
`git checkout -b serverfix origin/serverfix`。
这是一个十分常用的操作所以 `Git` 提供了 `--track` 快捷方式: 
`git checkout --track origin/serverfix`

### `git push`
运行 `git push origin serverfix:awesomebranch` 来将本地的 `serverfix` 分支推送到远程仓库上的 `awesomebranch` 分支。
#### `git config --global push.default simple`
push.default可用的值如下：
* nothing
不推送任何东西并有错误提示，除非明确指定分支引用规格。强制使用分支引用规格来避免可能潜在的错误。
* current
推送当前分支到接收端名字相同的分支。
* upstream
推送当前分支到上游@{upstream}。这个模式只适用于推送到与拉取数据相同的仓库，比如中央工作仓库流程模式。
* simple
在中央仓库工作流程模式下，拒绝推送到上游与本地分支名字不同的分支。也就是只有本地分支名和上游分支名字一致才可以推送，
就算是推送到不是拉取数据的远程仓库，只要名字相同也是可以的。在 `Git` 2.0中，`simple` 将会是 `push.default` 的默认值。`simple` 只会推送本地当前分支。
* matching
推送本地仓库和远程仓库所有名字相同的分支。
这是 `Git` 当前版本的缺省值。

### `git diff`
若要查看已暂存的将要添加到下次提交里的内容，可以用 `git diff --cached` 命令或者  `git diff --staged`。
`git diff` 本身只显示尚未暂存的改动，而不是自上次提交以来所做的所有改动。 所以有时候你一下子暂存了所有更新过的文件后，运行 `git diff` 后却什么也没有，就是这个原因。

### `git commit`
有时候我们提交完了才发现漏掉了几个文件没有添加，或者提交信息写错了。此时，可以运行带有 `--amend` 选 项的提交命令尝试重新提交
```shell
git commit -m 'initial commit'
git add forgotten_file
git commit --amend
```
当使用 `git commit` 进行提交操作时，`Git` 会先计算每一个子目录(本例中只有项目根目录)的校验和，然后在 `Git` 仓库中这些校验和保存为树对象。随后，`Git` 便会创建一个提交对象，它除了包含上面提到的那些信息外， 还包含指向这个树对象(项目根目录)的指针。
```shell
git add README test.rb LICENSE
git commit -m 'The initial commit of my project'
```
现在，`Git` 仓库中有五个对象:三个 `blob` 对象(保存着文件快照)、一个树对象(记录着目录结构和 `blob` 对象 索引)以及一个提交对象(包含着指向前述树对象的指针和所有提交信息)。

### `git remote`
|命令|含义|
|:-|:-|
|git remote|查看你已配置的远程仓库服务器| 
|git remote -v|显示需要读写远程仓库使用的 Git 保存的简写与其对应的 URL|
|git remote add <shortname> <url>|添加一个新的远程Git仓库，同时指定一个你可以轻松引用的简写|
|git remote show [remote-name]|查看某一个远程仓库的更多信息|
|git remote rename respA respB|重命名引用的名字|
|git remote rm respC|移除|

### `git rebase`
原理是首先找到这两个分支(即当前分支 `experiment`、变基操作的目标基底分支 `master`)的最近共同祖 先 C2，然后对比当前分支相对于该祖先的历次提交，提取相应的修改并存为临时文件，然后将当前分支指向目 标基底 C3, 最后以此将之前另存为临时文件的修改依序应用。
提取在 C4 中引入的补丁和修改，然后在 C3 的基础上应用一次。 在 Git 中，这种操作就叫做变基。你可以使用 `rebase` 命令将提交到某一分支上的所有修改都移至另一分支上，就好像“重新 播放”一样。
##### 变基的风险
### !!不要对在你的仓库外有副本的分支执行变基。
如果你遵循这条金科玉律，就不会出差错。否则，人民群众会仇恨你，你的朋友和家人也会嘲笑你，唾弃你。
变基操作的实质是丢弃一些现有的提交，然后相应地新建一些内容一样但实际上不同的提交。如果你已经将提交推送至某个仓库，而其他人也已经从该仓库拉取提交并进行了后续工作，此时，如果你用 `git rebase` 命令 重新整理了提交并再次推送，你的同伴因此将不得不再次将他们手头的工作与你的提交进行整合，如果接下来你 还要拉取并整合他们修改过的提交，事情就会变得一团糟。
```shell
git rebase master(target) (:into current)
git rebase master(target) server(into)
```

### 常用 `git alias`
```shell
git config --global alias.pl pull
```
* a => !git add . && git status
* aa => !git add . && git add -u . && git status
* ac => !git add . && git commit
* acm => !git add . && git commit -m
* alias => !git config --list | grep 'alias\.' | sed 's/alias\.\([^=]*\)=\(.*\)/\1\ => \2/' | sort
* au => !git add -u . && git status
* br => branch
* c => commit
* ca => commit --amend
* ci => commit
* cm => commit -m
* co => checkout
* d => diff
* l => log --graph --all --pretty=format:'%C(yellow)%h%C(cyan)%d%Creset %s %C(white)- %an, %ar%Creset'
* last => log -1 HEAD
* lg => log --color --graph --pretty=format:'%C(bold white)%h%Creset -%C(bold green)%d%Creset %s %C(bold green)(%cr)%Creset %C(bold blue)<%an>%Creset' --abbrev-commit --date=relative
* ll => log --stat --abbrev-commit
* llg => log --color --graph --pretty=format:'%C(bold white)%H %d%Creset%n%s%n%+b%C(bold blue)%an <%ae>%Creset %C(bold green)%cr (%ci)' --abbrev-commit
* master => checkout master
* pl => pull
* s => status
* spull => svn rebase
* spush => svn dcommit
* st => status
* unstage => reset HEAD --

### 参考书籍
[progit_v2.1.19](https://git-scm.com/book/zh/v2)