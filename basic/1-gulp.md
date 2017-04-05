---
title: gulp自动化工具的安装和使用
---

### gulp的安装

第一步安装gulp自动化工具

```bash
npm i -g gulp
```

第二步初始化项目仓库

```bash
npm init
在项目文件夹中输入，生成一个package.json
```

第三步在项目的文件中安装一个gulp

```bash
npm i --save-dev gulp
等价于  npm i -D gulp
```

第四步安装gulp-sass编译插件

```bash
npm i -D gulp-sass
```

### gulp 的一些常用的插件

- gulp-autoprefixer
  - 主要给CSS文件中添加厂商前缀。
- gulp-sass
  - 把.scss或者.sass编译成.css文件。
- gulp-imagemin
  - 图片压缩工具，转换后大小变为原来的一般，图片质量不会损失。
- gulp-browsersync
  - 可以在本地搭建一个http服务器来，同时http开放链接，可以在手机等各个设备打开调试的页面，页面是的操作可以在各个设备上同步。
  - 页面自动加载，也就是源码修改之后，页面不需要刷新就可以页面自动加载，可以同时在多个设备上完成。
- gulp-wrap
  - 把页面中一样的部分抽离出来，如 header footer 部分，我们只写页面主体内容，最后通过 gulp 加上 header footer。
- gulp-minify-css
  - 压缩css代码
- gulp-purifycss
  - 可以帮我们去除 html/js 页面中根本就没有用到的 CSS 代码（死代码？），想想，如果咱们的项目中用了 bootstrap.css ，肯定会有很多 css 代码是没用的。

### gulp自动化脚本的写法

脚本代码写在gulpfile.js文件中。可以使用 gulp.src 来选取文件，这样里面的数据就会形式“流”，沿着“管道”（ pipe ）一直向前，管道上的每个节点上我们都可以让一个插件发挥作用，然后处理后的数据最终通过 gulp.dest 导出到一个文件夹中。

```js
gulp.src('src/*.scss')
//锁定我们要处理的一些文件
.pipe(sass())
//pipe（管道） 把文件交给工具流 sass 处理这些sass文件变成css文件
.pipe(prefix())
//接上一步的文件，添加厂商前缀
.pipe(gulp.dest('css/'));
//把最后处理好的文件导出到目标文件夹里面
```

### gulp `gulpfile.js` 文件中常用写法

```js
var gulp = require('gulp');
var sass = require('gulp-sass');
var prefix = require('gulp-autoprefixer');
var minifyCss = require('gulp-minify-css');

gulp.task('sass',function(){
  gulp.src('src/main.scss')
  .pipe(sass())      //scss或者sass转化为 css 文件
  .pipe(prefix())    // css文件中添加前缀
  .pipe(minifyCss()) // css文件压缩
  .pipe(gule.dest('dist/'));
})
//各个指令封装在 gulp.task之中，第一个参数为名字。
//然后到命令行中打开有 gulpfile.js 文件的位置，执行 gulp sass 命令，会发现，main.scss 文件转化成了 main.css 放在 dist文件夹下了。

gulp.task('copy-file',function(){
  gulp.src('src/*.html')
    .pipe(gulp.dest('dist/'))
})
//定义一个拷贝文件的指令，把src文件夹下的所有 .html 文件拷贝到 dist文件夹下。

gulp.task('default',['sass','copy-file'])
//同时执行sass copy-file 两个动作,在 bash 中只用输入 gulp 或者 gulp default 就行了
```

### 使用 imagemin 来压缩图片

需要安装两个包，一个是 gulp-imagemin ，另一个是 imagemin-pngquant(专门用来压缩PNG图片的 两个包配合使用)。

```bash
cnpm i -D  gulp-imagemin imagemin-pngquant
```

gulpfile.js 中的内容

```js
//code
var imagemin = require('gulp-imagemin');
var pngquant = require('imagemin-pngquant');

gulp.task('imagemin', function(){
  return gulp.src('src/images/*')
    .pipe(imagemin({
      progressive: true,
      svgoPlugins: [{removeViewBox: false}],
      use: [pngquant()]
    }))
    .pipe(gulp.dest('dist/images'));
});
//code
```

在命令行中输入 `gulp imagemin` 就可以压缩图片了。它会把 `src/images` 文件夹下面的所有图片全部压缩，放在了 `dist/images` 文件夹中。

更多文档参考github上的教程 [imagemin](https://github.com/imagemin/imagemin)
