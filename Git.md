# git

## git add

`git add`命令是个多功能命令 ，根据目标文件的状态不同，此命令的效果也不同，可以用来：开始跟踪新文件、把已跟踪的文件放到暂存区，还能用于合并时把有冲突的文件标记为已解决状态。

1. 追踪新的文件

   ```shell
   $ git add documentation/*.txt
   ```

   追踪documentation目录之下的所有txt文件的状态，将要提交的文件的信息添加到索引库中(将修改添加到暂存区)，以准备为下一次提交分段的内容。 它通常将现有路径的当前内容作为一个整体添加，但是通过一些选项，它也可以用于添加内容，只对所应用的工作树文件进行一些更改，或删除工作树中不存在的路径了。

2. 添加到暂存区

   ```shell
   $ git add .  # 将所有修改添加到暂存区
   $ git add *  # Ant风格添加修改
   $ git add *Controller   # 将以Controller结尾的文件的所有修改添加到暂存区
   
   $ git add Hello*   # 将所有以Hello开头的文件的修改添加到暂存区 例如:HelloWorld.txt,Hello.java,HelloGit.txt ...
   
   $ git add Hello?   # 将以Hello开头后面只有一位的文件的修改提交到暂存区 例如:Hello1.txt,HelloA.java 如果是HelloGit.txt或者Hello.java是不会被添加的
   ```



## git branch

### branch

#### remote/local

远程分支其实就是远程代码仓库当中的分支，比如我们的repo如果是存在github的，那么这个远程仓库就是github，如果是存在gitlab的，那么这个仓库就是gitlab，如果是其他的仓库也是一样的道理。

当我们在使用git clone的时候，git会自动地将这个远程的repo命名为origin，拉取它所有的数据之后，创建一个指向它master的指针，命名为origin/master，之后会在本地创建一个指向同样位置的指针，命名为master，和远程的master作为区分。

也就是说，origin的含义指的是远程的仓库。它只是一个标记，就和默认分支叫做master一样，本身并没有特别的含义。如果我们愿意也可以起其他的名字，但是一般没有人这么干。
————————————————
版权声明：本文为CSDN博主「TechFlow」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/TechFlow/article/details/109352820

### branch commands

1. 查看分支

   `git branch`

   `git branch --list`

   ```shell
   $ git branch --list
     dev
   * main
   ```

   显示结果中显示目前有两个分支，master和dev，带星号(*)的表示目前处于这个分支。

2. 查看本地和远程分支

   查看远程分支

   ```shell
   $ git branch -r
     origin/HEAD -> origin/main
     origin/main
   ```

   查看所有分支

   ```shell
   $ git branch -a
   * main
     remotes/origin/HEAD -> origin/main
     remotes/origin/main
   ```

3. 新建一个分支

   `git branch dev`创建一个名为dev的分支

   ```shell
   $ git branch
   * main
     dev
     # 这个时候dev是一个本地分支，远程仓库不知道它的存在
     # 本地分支可以不同步到远程仓库，我们可以在dev开发，然后merge到master，使用master同步代码，当然也可以同步
   ```
   
   将新建的分支推送到远程仓库
   
   `git push origin dev:dev`，在远程仓库中也有了一个分支，与我们本地的dev同名，也叫做dev
   
4. 切换到指定分支

   `git checkout branch-name`

   ```shell
   $ git checkout dev
   Switched to branch 'dev'
   
   $ git branch
   * dev
     main
   ```

5. 将修改添加到新建的分支上

   ```shell
   $ git status
   On branch dev2
   Untracked files:
     (use "git add <file>..." to include in what will be committed)
   
           newfile.txt
   
   nothing added to commit but untracked files present (use "git add" to track)
   
   Administrator@MY-PC /D/worksp/sample (dev2)
   
   $ git add newfile.txt
   
   Administrator@MY-PC /D/worksp/sample (dev2)
   
   $ git commit newfile.txt -m "commit a new file: newfile.txt"
   [dev2 c5f8a25] commit a new file: newfile.txt
    1 file changed, 2 insertions(+)
    create mode 100644 newfile.txt
   
   Administrator@MY-PC /D/worksp/sample (dev2)
   
   $ git push origin dev2
   Username for 'http://git.oschina.net': 769728683@qq.com
   Password for 'http://769728683@qq.com@git.oschina.net':
   Counting objects: 12, done.
   Delta compression using up to 4 threads.
   Compressing objects: 100% (8/8), done.
   Writing objects: 100% (11/11), 965 bytes | 0 bytes/s, done.
   Total 11 (delta 3), reused 0 (delta 0)
   To http://git.oschina.net/yiibai/sample.git
    * [new branch]      dev2 -> dev2
   ```

6. 删除远程分支

   `git branch -d`

   `git branch --delete`

   `git push origin :dev`上传一个空的分支，也就是把这个分支删除



## git commit

commit命令主要是将暂存区里的改动提交到本地的版本库。每次使用`git commit`命令我们都会在本地版本库生成一个 40 位的哈希值，这个哈希值也叫 commit-id。

commit-id 在版本回退的时候是非常有用的，它相当于一个快照，可以在未来的任何时候通过与`git reset`的组合命令回到这里。

1. 将暂存区的修改提交到本地的版本库中

   `git commit -m 'commit-message'`

   提交到版本库，并指定提交信息

2. 将已跟踪的文件中修改或者删除过的文件提交到本地版本库，即使没有git add

   `git commit -a -m 'commit-message'`

3. 追加提交

   `git commit -amend`可以在不增加一个commit-id的情况下将修改过的代码追加到前一次的commit-id中。

[Git教程2(工作区和暂存区) - 云+社区 - 腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1409341)



### git commit message

**`feat(feature)`**
意为：新功能

```shell
git commit -m 'feat(xxx):xxx'
```

**`style`**
意为：样式上的变动，不影响代码逻辑

```shell
git commit -m 'style(xxx):xxx'
```

**`fix`**
意为：修复（修复bug）有时可在相关commit上加上修复的bug的等级
如：

Blocker (中断) : 客户端程序无响应，无法执行下一步操作
Critical (严重)：功能点缺失
Major (较严重)：功能点没有满足需求
Normal (普通)：数值计算错误，js错误
Minor (次要)：界面UI与需求不符
Trivial (轻微)：辅助描述说明不清楚，提示语句错误之类…

```shell
git commit -m 'fix(xxx):xxx'
```

**`test`**
意为：测试代码

```shell
git commit -m 'test(xxx):xxx'
```

**`chore`**
意为：构建过程或辅助工具的变动，比如项目新加了别的js插件之类的

```shell
git commit -m 'chore(xxx):xxx'
```

**`refactor`**
意为：重构，既不是修复bug也不是样式上的变动，有时可理解为优化

```shell
git commit -m 'refactor(xxx):xxx'
```



## git fetch





## git merge

git merge用于将两个或者两个以上的开发历史合并到一起。

```shell
git merge [-n] [--stat] [--no-commit] [--squash] [--[no-]edit]
    [-s <strategy>] [-X <strategy-option>] [-S[<keyid>]]
    [--[no-]allow-unrelated-histories]
    [--[no-]rerere-autoupdate] [-m <msg>] [<commit>…​]
git merge --abort
git merge --continue
```



## git pull 

git pull = git fetch + git merge

git pull --rebase = git fetch + git rebase







## git push





## git rebase

[闲谈 git merge 与 git rebase 的区别 - SegmentFault 思否](https://segmentfault.com/a/1190000005013964)

rebase总是只有一条分支，在最新的该分支的最新版本后面合并新的提交。

merge会保留版本信息。

综上：

- merge结果可以保留时间线，但是rebase会打乱时间线。
- 但是rebase看起来更加简洁，merge看起来比较杂乱。
- 最终结果都将代码的版本合并了。





## git stash

[git-stash用法小结 - Tocy - 博客园 (cnblogs.com)](https://www.cnblogs.com/tocy/p/git-stash-reference.html)

git stash适用的几种情形：

1. 有一个类是多余的，想要删除它但是又担心以后需要以后查看他的代码，想要保存又不想增加一个脏的提交。
2. 在当前的开发分支上有很多修改记录，但是现在需要切换到另外的分支，之后再改回来当前的分支。使用git stash就可以将本地的修改推入Git栈中，这时候的工作区间和上一次是完全相同的，切换回当前目录之后再使用`git stash apply`将以前的工作切换回来。



**stash当前的修改**

git stash会将所有未提交的修改（包括暂存的非暂存的）都保存起来，用来恢复以后的工作目录。git stash是本地的，不会提交到git服务器。

实际应用中，使用`git stash save 'initial stash'`给git stash加一个message用于版本记录，

```shell
$ git status
On branch master
Changes to be committed:

new file:   style.css

Changes not staged for commit:

modified:   index.html

$ git stash
Saved working directory and index state WIP on master: 5002d47 our new homepage
HEAD is now at 5002d47 our new homepage

$ git status
On branch master
nothing to commit, working tree clean
```



**重新应用缓存的stash**

可以使用`git stash pop`恢复之前的工作目录，这个指令将缓存堆栈中的第一个stash删除，并将对应修改应用到当前的工作目录下。

```shell
$ git status
On branch master
nothing to commit, working tree clean
$ git stash pop
On branch master
Changes to be committed:

    new file:   style.css

Changes not staged for commit:

    modified:   index.html

Dropped refs/stash@{0} (32b3aa1d185dfe6d57b3c3cc3b32cbf3e380cc6a)
```

或者使用`git stash apply`

```shell
$ git stash apply
On branch master
Changes to be committed:

    new file:   style.css

Changes not staged for commit:

    modified:   index.html
```



**查看现有的stash**

```shell
$ git stash list
stash@{0}: WIP on master: 049d078 added the index file
stash@{1}: WIP on master: c264051 Revert "added file_size"
stash@{2}: WIP on master: 21d80a5 added number to log
```



**移除现有的stash**

```shell
$ git stash list
stash@{0}: WIP on master: 049d078 added the index file
stash@{1}: WIP on master: c264051 Revert "added file_size"
stash@{2}: WIP on master: 21d80a5 added number to log
$ git stash drop stash@{0}
Dropped stash@{0} (364e91f3f268f0900bc3ee613f9f733e82aaed43)
```

或者可以使用`git stash clear`可以清除所有的stash。





## # git dir

**工作区（Working Directory）** 

是我们直接编辑的地方，例如eclipse打开的项目，记事本打开的文本等，肉眼可见，直接操作。 

**暂存区（Stage 或 Index）** 

数据暂时存放的区域，可在工作区和版本库之间进行数据的友好交流。

 **版本库（commit History）** 

存放已经提交的数据，push 的时候，就是把这个区的数据 push 到远程仓库了。



## # conflict

### How conflicts take place

冲突来自两个分支的冲突，具体来说是，**两个已经提交的分支的相同文件的相同位置的不同操作的合并导致的冲突。**

> 实际上测试下来，似乎只要是相同文件，都会产生冲突。

**有效减少冲突的好习惯是，修改文件之前先merge别的分支。**



在master分支上创建一个文件，添加并且提交，此时切换到dev分支，此时dev分支是没有这个文件的，这说明各个分支之间是相互独立的。通过git merge master把master上的新增文件可以merge到dev分支，因为0+x=x，不会产生冲突。

如果此时在dev分支上把master对应行的数据给修改了，再切换回到master，git merge dev，也不会产生冲突，因为被dev的分支修改覆盖了。

但是如果切换到分支dev，修改了第n行代码，再切换到master分支，也修改了第n行代码，并且提交，这时候就会在git merge dev或者git merge master的时候出现冲突，不解决冲突的话是没法提交和切换到别的分支的。

解决冲突之后重新add->commit->push。





































