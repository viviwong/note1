---
title: coding github上传代码操作
---

### coding github上传代码操作基本是相同的。

- 首先是项目文件名字有点区别，coding项目文件夹叫 `用户名` ，github项目文件夹叫 `用户名.github.io`

- 然后是分支 `coding-pages`  `gh-pages` 不同
- `id_rsa.pub` 密钥是共用的

### 一般项目的push操作流程

- `mkdir yewenxiang.github.io` 创建项目文件夹

- `git init` 初始化项目文件夹，*添加了一个.git隐藏文件*

- 这步是里面放一些文件，然后做版本

- `git add -A`

- `git commit -m"改动内容"`

- github 上建一个同名的仓库，注意不能点击初始化仓库的选项,这会造成服务器仓库和本地仓库版本不一致，导致版本冲突

- 复制仓库地址到bash命令行 例如： `git remote add origingit@github.com:funnydeer/funnydeer.github.io.git` 记录仓库地址,在项目文件夹 `.git/config` 文件中可查看。这有两种方式：

    - 一种是 `http` 方式，每次推送都要输入用户名和密码,而且不安全，不推荐这种方法

    - 另一种是 `ssh` 方式,需要 bash命令行输入 `ssh-keygen` 会生成两个密钥 取公钥 `~/.ssh/id_rsa.pub` 添加到 `github` 设置上去 建立互信。

- `git push -u origin master` 首次执行时，在远端创建 `master` 分支，并且把本地的代码上传到这个分支之上. 后续推送操作都只需 `git push`就行了。

### 一些操作
```bash
git remote add origin git@git.coding.net:yewenxiang/yewenxiang.git
修改 `.git/config` 文件中的属性，添加仓库的地址
```



```bash
git push -u origin maste
推送仓库当前版本到主分支之上
```



```bash
ssh-keygen
生成一个公钥和私钥 在 `cd ~/.ss` 文件夹下
```


```bash
git branch
查看本地的分支
```


```bash
git checkout -b coding-pages
在本地创建一个 `coding-pages` 分支
```


```bash
git push -u origin coding-pages
首次执行时，在远端创建 `coding-pages` 分支，并且把本地的代码上传到这个分支之上
```
