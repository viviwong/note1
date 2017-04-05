---
title: npm的使用方法
---

npm 是随同node一起安装的包管理工具

- 允许用户从NPM服务器下载别人编写的第三方包到本地使用。
- 允许用户从NPM服务器下载并安装别人编写的命令行程序到本地使用。
- 允许用户将自己编写的包或命令行程序上传到NPM服务器供别人使用。

### 模块初始化
```bash
npm init
```
 `cd` 到项目文件夹下输入 `npm init` 这样就初始化了一个node的项目，文件夹里面生成了一个 `package.json` 文件，里面的信息为初始化项目时终端中输入的信息。

 > script 字段中可以定义我们自己的一些脚本
 >

### npm安装模块
```bash
npm install <Module Name> --save
```
这时，就会在我们的项目文件夹下生成一个 `node_modules` 的文件夹,里面会有安装的包。

- 本地安装

```bash
    npm install <Module Name> --save
    版本名和版本号记录在dependencies字段中
    npm install express --save-dev
    版本名和版本号记录在devDependencies字段中
```

    dependencies字段中的包信息是项目上线时依赖的包，devDependencies字段中的包信息是开发项目时需要的包工具。

- 全局安装

```bash
  npm install <Module Name> -g
```

```bash
  npm ls -g
  查看全局安装的模块
```

  不建议使用全局安装，因为多人协作时，可能会导致开发环境不同（包的版本不同）导致一些问题。

### npm 卸载模块

```bash
npm uninstall <Module Name>
```

### npm 跟新模块

```bash
npm update <Module Name>
```

### npm 搜索模块

```bash
npm search <Module Name>
```

### npm 发布模块
```bash
npm publish
```
发布前需要输入以下命令

```bash
npm adduser
```

### 其他常用命令
```bash
npm help
npm cache clear  //可以清空npm本地的缓存
```

### 后续学习资源
- [阮一峰的教程](http://www.ruanyifeng.com/blog/2016/10/npm_scripts.html)
