---
title: nvm安装node.js的一些基本操作
---

安装node方式很多，最好使用nvm (*node.js的版本控制工具，可以同时安装多个node.js*).
使用 `nvm install v...` 安装node 不会覆盖之前使用nvm装的node版本，因为所有的 .node 文件夹都是在 .nvm 文件夹下的，不会被覆盖。

安装 nvm 可以去 github上去找安装 [nvm](https://github.com/creationix/nvm)

### 安装 nvm
```bash
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.0/install.sh | bash
```

```bash
nvm ls-remote
查看可以安装的node.js的版本
```
```bash
nvm install v7.4.0
安装 7.4.0版的node.js
```
> `npm` node的包管理工具
>

### 设置默认 node 版本

```bash
nvm alias default 5.10.1
```
 执行上面语句，重启 shell 之后，执行 `node -v` 查看切换后的node 版本
