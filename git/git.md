# git
## 简介
- Git 是一个开源的分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目。

## 安装
- windows下安装
```
https://gitforwindows.org/
```
完成安装之后，就可以使用命令行的 git 工具（已经自带了 ssh 客户端）了，另外还有一个图形界面的 Git 项目管理工具。
在开始菜单里找到"Git"->"Git Bash"，会弹出 Git 命令窗口，你可以在该窗口进行 Git 操作。

## git配置

### 用户信息
- 配置个人的用户名称和电子邮件地址
```
git config --global user.name "Bubble-0"

git config --global user.email miaojing_0904@163.com
```
如果用了 --global 选项，那么更改的配置文件就是位于你用户主目录下的那个，以后你所有的项目都会默认使用这里配置的用户信息。

如果要在某个特定的项目中使用其他名字或者电邮，只要去掉 --global 选项重新配置即可，新的设定保存在当前项目的 .git/config 文件里

### 查看配置信息
- 要检查已有的配置信息，可以使用 git config --list 命令
```
git config --list
```

### Git 工作区、暂存区和版本库
- 工作区：就是你在电脑里能看到的目录
- 暂存区：英文叫stage, 或index。一般存放在 ".git目录下" 下的index文件（.git/index）中，所以我们把暂存区有时也叫作索引（index）
- 版本库：工作区有一个隐藏目录.git，这个不算工作区，而是Git的版本库

### 配置SSH
- ssh -keygen -t rsa -C "你的邮箱"
- 从用户目录下的.ssh文件夹中将公钥复制到git的setting中的ssh选项中

## git基本操作

### 创建仓库
Git 使用 git init 命令来初始化一个 Git 仓库，Git 的很多命令都需要在 Git 的仓库中运行，所以 git init 是使用 Git 的第一个命令。
在执行完成 git init 命令后，Git 仓库会生成一个 .git 目录，该目录包含了资源的所有元数据，其他的项目目录保持不变（不像 SVN 会在每个子目录生成 .svn 目录，Git 只在仓库的根目录生成 .git 目录）。

- 使用当前目录作为Git仓库，我们只需使它初始化。
```
git init  --该命令执行完后会在当前目录生成一个 .git 目录。
```

- 使用指定目录作为git仓库
```
git init repname  --目录名称
```

- 提交文件  
如果当前目录下有几个文件想要纳入版本控制，需要先用 git add 命令告诉 Git 开始对这些文件进行跟踪，然后提交
```
git add README
git commit -m '初始化项目版本'

-- 以上命令将目录 README 文件提交到仓库中。
```

- 克隆仓库  
使用 git clone 从现有 Git 仓库中拷贝项目（类似 svn checkout）。
```
git clone <repo> -- 克隆仓库名称到当前目录

git clone <repo> <directory>   -- 克隆仓库名称到指定目录
```

### 常规操作
- 创建目录并创建git仓库
```
mkdir bubble-git-demo

cd bubble-git-demo

git init

ls -a

```

### 基本快照
Git的工作就是创建和保存你的项目的快照及与之后的快照进行对比。
- 创建文件并添加到缓存
```
touch README

touch helloworld.js

ls

git status -s
> ?? README
> ?? helloworld.js
-- 此时文件还未被git管理

git add README helloworld.js

git status -s
> A  README
> A  helloworld.js
-- 可以看到两个文件已经被加上去了

-- 可以添加当前目录所有文件
git add . 


-- 修改文件
vim helloworld.js

-- 添加内容后，可以看到修改文件的状态改变
git status -s
A  README
AM helloworld.js

> "AM" 状态的意思是，这个文件在我们将它添加到缓存之后又有改动。改动后我们再执行 git add 命令将其添加到缓存中
```

- 查看状态
```
git status
-- 以查看在你上次提交之后是否有修改。加上-s参数可以获得短的输出

git diff
-- 执行 git diff 来查看执行 git status 的结果的详细信息。

> 尚未缓存的改动：git diff
> 查看已缓存的改动： git diff --cached
> 查看已缓存的与未缓存的所有改动：git diff HEAD
> 显示摘要而非整个 diff：git diff --stat

-- git status 显示你上次提交更新后的更改或者写入缓存的改动， 而 git diff 一行一行地显示这些改动具体是啥。修改之后用git diff命令查看，提交之后用git diff --cached命令查看

```
- git删除
```
-- 直接删除
git rm <文件>

-- 如果有修改或者已经add
git rm -f <file>

-- 如果只是从跟踪清单中删除
git rm --cached <file>
```
- 提交改动  
使用 git add 命令将想要快照的内容写入缓存区， 而执行 git commit 将缓存区内容添加到仓库中。

- git reset HEAD  
git reset HEAD 命令用于取消已缓存的内容
```
-- 同时修改两个文件

$ git status -s
 M README
 M helloworld.js

git add .

git reset HEAD README

$ git status -s
 M README
M  helloworld.js

-- 可以看到README文件已经取消了缓存
-- 此时执行git commit 只会提交helloworld.js文件

-- 此时可以执行
git commit README -m 'commit readme'

-- 简而言之，执行 git reset HEAD 以取消之前 git add 添加，但不希望包含在下一提交快照中的缓存。

```

### git分支管理
```
-- 创建分支
git branch (branchname)

-- 切换分支
git checkout (branchname)
-- 当你切换分支的时候，Git 会用该分支的最后提交的快照替换你的工作目录的内容， 所以多个分支不需要多个目录。

-- 合并分支
git merge 
-- 你可以多次合并到统一分支， 也可以选择在合并之后直接删除被并入的分支。

eg：
$ git branch test1
$ git branch
* master
  test1
-- 现在我们可以看到，有了一个新分支 testing。当你以此方式在上次提交更新之后创建了新分支，如果后来又有更新提交， 然后又切换到了 testing 分支，Git 将还原你的工作目录到你创建分支时候的样子。

echo 'this is test1' > test1.txt
git add .
git commit -m 'add test'
git checkout test1
ls
>>> $ ls
>>> helloworld.js  README

-- 先创建一个文件提交之后在切换分支，可以发现git将还原至之前的文件状态中

$ git checkout master
Switched to branch 'master'
$ ls
helloworld.js  README  test1.txt  test2.txt

-- 切换回主分支就又恢复了

-- 我们也可以使用  git checkout -b (branchname) 命令来快速创建分支并切换

-- 删除分支
git branch -d (branchname)  -- 普通删除

git branch -D (branchname)  -- 当分支有提交内容，即分支未合并时需要强制删除

-- 合并分支
git merge xxx


-- 解决冲突
$ git status -s
UU helloworld.js
git add  helloworld.js

-- 对于合并之后冲突的文件状态会显示UU，修改完毕之后要使用add命令告诉git文件冲突已经解决

```

### git查看提交历史
```
git log 

git log --oneline 
-- 查看简介版本

```

### git标签
```
git tag -a v1.0 
-- 当你执行 git tag -a 命令时，Git 会打开你的编辑器，让你写一句标签注解，就像你给提交写注解一样。

git tag
-- 查看标签
```

### git远程仓库
```
-- 添加远程仓库
git remote add [shortname] [url]

-- 添加我的远程代码仓库
git remote add bubble-blog-code https://github.com/bubble-0/Bubble-blog-code.gi

-- 提交代码到bubble-blog-code 的主干分支
git push -u bubble-blog-code master

-- 查看当前配置有哪些远程仓库
git remote

git remote -v

-- 获取远程仓库的更新
-- 1、从远程仓库下载新分支与数据：
git fetch
-- 该命令执行完后需要执行git merge 远程分支到你所在的分支。

-- 2、从远端仓库提取数据并尝试合并到当前分支：
git merge
-- 该命令就是在执行 git fetch 之后紧接着执行 git merge 远程分支到你所在的任意分支。

```