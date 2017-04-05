---
title: git的一些命令操作
---

### 安装git

```bash
sudo apt-get update
sudo apt-get install git
sudo npm install git
```

> `apt-get` 是 `ubuntu` 系统（deepin其实就是ubuntu的一个变种）的软件安装命令
>

```bash
git --version
获取git的版本号
```

### git本地化的步骤

- 第一步：在项目文件夹中运行 `git init` 来初始化一个仓库,会建立一个.git 的隐藏文件夹。
- 第二步：`git add -A` 添加修改后的版本到 `.git` 文件夹中， `-A`是添加所有文件的意思
- 第三步: `git commit -m"留言内容"` 做成一个git本地的一个版本，`-m` 是 `message` 留言的意思，这个是必须的。

>这一步对于新装的git用户来说还要告诉git 用户名和邮箱。
>

```bash
git config --global user.name "yewenxiang"
git config --global user.email "yewenxiang23@gmail.com"
```


### git 其他的一些命令

```bash
git log -p
```
`log` 是日志的意思, `-p`是patch (补丁,就是修改内容)的缩写

使用 `brew install tig` 安装tig包后，查看版本日志方便了很多，选中其中一个版本按 `D` 可以查看详细信息 按 `Q` 可以退出

### git 回滚的操作

- 修改后没做版本 回到上个 `git commit -m"..."` 的版本可以使用

```bash
git reset --hard HEAD
```

- 修改后做了版本 回到上个版本可以使用

```bash
git reset --hard HEAD~
```

- 在用户的主目录文件夹下有一个 `.gitconfig` 的隐藏文件，可以修改里面的属性来配置git.

```json
[alias]
throw = reset --hard HEAD
throwh = reset --hard HEAD~
```

>添加上述代码后 每次回滚操作只需输入 git throw 或者 git throwh 就可以了
>

### git 取消回滚的操作

`git log` 可以查看项目各个版本 也就是 `git commit -m"..."` 做的本地版本，有时候我们做了 *回滚* 的操作（此时，输入`git log`，由于回滚到了上一个版本，所以回滚前的版本不见了。 ），但是又需要改变为 *回滚前* 的状态使用以下步骤。

- `git reflog` 来查看记录的HEAD历史，当做reset,checkout等操作时候，这些操作会被记录在 `reflog` 中,就可以查看 *回滚前* 操作的版本的哈希值，取前7位

- `git reset --hard 7位哈希值` 就可以被找回reset操作的那个版本

>如果你因为reset等操作丢失一个提交的时候，你总是可以把它找回，除非你的操作已经被git当做垃圾处理了，一般是在30天后执行。
>

### git clone 命令

要想把 github 上的一个项目代码下载到本地有两种方式，一种就是普通下载（ download ）。但是，开发者 基本上会选择另外一种方式，就是 clone 。
```
git clone git@github.com:happypeter/digicity.git
```
clone 的特点就是不仅仅可以得到最新代码，而且可以得到整个改版历史。而普通下载只能得到最新版本。

### git 各个命令的作用

- `git push` 把本地仓库中有，而远端对应仓库中没有的版本推送到远端

- `git pull` 把远端仓库中有，而本地对应仓库中没有的版本拉到本地

- `git clone` 把远端仓库，克隆到本地

### 如何协同合作
在github 上面打开项目仓库，点 `setting` -> `Collaborators` 然后输入需要合作的用户名称，添加后点击 `Copy invite link` 把网址发给对方确认，就可以分工合作了。

在存在本地版本与服务器版本不同时，`git push` 会失败。此时需要 `git pull`来更新最新的版本。
前提是修改的地方没有冲突 `git pull` 才会成功。

如果别人修改了文件中一个地方，你本地又修改了同一个地方，此时就会造成冲突，`git pull` 会不成功，并且会在冲突的位置添加注释，需要和对方商量到底用哪个人的方案，如果用我的方案，只需要把注释删除，然后再 `git pull` 即可，此时服务器上面就合并成了你的方案，然后再版本三步走，推送你的代码就行啦。

### git 分支的操作

每次我们对新仓库推送代码时都要执行类似于以下两行代码

- `git remote add origin git@github.com:happypeter/digicity.git` 设置仓库的地址，修改 `.git/config` 文件中的属性

- `git push -u origin master` 推送仓库当前版本到主分支之上,同时在远端创建了主分支

```
git checkout -b dev
在本地创建新分支bev
```
本地创建新分之后，新分支的指针是指向 `master` 分支的，也就是新分支的文件和主分支的文件是相同的（做了一次拷贝），一般是修改新分支下的文件后，做版本，然后 `git push -u origin dev` 在远程创建 *dev* 分支，同时把本地的内容推送到远端的 dev 分支之上，注意这需要切换到 dev 分支上操作，

```
git branch
查看本地分支
```

```
git checkout 分支名
切换分支
```
---

### 如何删除分支

- 如果有两个分支 `master` `dev` , 首先需要切换到 `master` 分支之上
- `git branch -D dev` 这样本地 `dev` 分支就没有了, 但是 github 上的没有受到影响
- `git push origin :dev` 这样可以把 github上的 `dev` 分支删除

### 合并分支
一般 `master` 分支上的代码是随时可以部署的项目，有时候我们需要添加新功能，这需要创建新的分支去测试，如果没问题了，则合并到主分支之上

- 首先切换到主分支之上。
- `git merge dev` 把 `dev` 分支上的内容合并到 `master` 分支之上。
- 只是在本地进行了合并，还需要 `git push` 推送到远端仓库。
- 如果需要删除这个分支，则进行上面的删除分支操作。

---

### 补充

`.gitignore` 文件中填写要忽略的文件或者文件名字，也就是不做版本的时候不会跟踪，也不会上传到github服务器上面，一般文件建在 `.git` 同级目录下面。

### 学习git资源

- [Atom 爱上 JS](http://haoqicat.com/atom-love-js)
- [驾驭命令行怪兽](http://haoqicat.com/ride-cli-monster)
- [Git北京](http://haoqicat.com/gitbeijing)
