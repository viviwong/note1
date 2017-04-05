---
title: 用flex做一个响应式的demo流程
---

### 准备工作

- github 上建立一个 flex的仓库。
- `git clone git@github.com:yewenxiang23/flexbox.git` 克隆到桌面，在 `gh-pages` 这个分支上操作。
- 创建一个 `source` 文件夹，我们的源代码放在这个文件夹里面。
- 在 `source` 文件夹中初始化 `node.js` 项目，输入 `npm init` ，文件夹里面生成了一个 `package.json` 文件。
- 在 `source` 文件夹中安装需要的包， `npm i -D gulp gulp-sass gulp-autoprefixer` 。
- 装好之后，在 `source` 文件夹中创建 `.gitignore` 文件，输入 `node_modules` ，忽略 `node_modules` 文件夹,
- 在 `source` 文件夹中创建 `gulpfile.js` 文件文件中输入以下内容

  ```js
  var gulp = require('gulp');
  var sass = require('gulp-sass');
  var prefix = require('gulp-autoprefixer');

  gulp.task('sass', function(){
  gulp.src('styles/main.scss')
  .pipe(sass())
  .pipe(prefix())
  .pipe(gulp.dest('../styles'));
  })
  ```
> 中间两行的 .pipe() 后面没有分号
>

- 测试:在包含 `gulpfile.js` 的文件夹中,输入bash命令 `gulp sass` 。
- 在 `flexbox/source/index.html` 新建一个 `index.html` 文件。
- 我们需要 `gulp` 把 `index.html` 文件拷贝到 `flexbox` 文件夹下面，在 `gulpfile.js` 文件中添加以下代码。

  ```js
  gulp.task('copy',function(){
  gulp.src('index.html')
  .pipe(gulp.dest('..'));
  })

  gulp.task('default',['sass','copy']);
  // 每次在命令行中输入 `gulp` 就可以了。
  ```
- 每次修改我们都要输入命令 `gulp` 进行编译很麻烦，在 `gulpfile.js` 文件中添加以下代码

  ```js
  gulp.task('watch',function(){
  gulp.watch(['*.html'], ['copy']);
  //使用gulp自带的.watch 监控命令，第一个参数是监控的文件(当前目录下的所有.html文件)数组，第二个参数是执行的动作数组(执行拷贝命令)
  gulp.watch(['styles/*.scss'], ['sass']);
  //同上，文件内容改变后，自动编译。
  })

  gulp.task('default',['sass','copy','watch']);
  //添加 watch 动作
  ```
- 在命令行中输入 `gulp` 发现命令行中任务始终在执行，因为 gulp 一直在监控。
- 然后修改代码，刷新页面，发现自动进行编译了，但是还是需要手动刷新页面。
- 在写代码的时候，如果SCSS代码有语法错误，按了保存，后台的gulp 就自动退出了。下面方法解决

  ```js
  function handleError(err) {
  console.log(err.toString());
  this.emit('end');
  }

  gulp.task('sass', function () {
    return gulp.src('src/style/main.scss')
        .pipe(sass()).on('error', handleError)
        ...
  });
  //在.pipe(sass()) 后加上 .on('error', handleError) 这样有错误时会报错但是 gulp 不会退出了
  ```

- 使用gulp-wrap包来抽离公共的头部的底部,具体使用方法:

  ```js
  var wrap = require('gulp-wrap');
  gulp.task('build', function(){
  gulp.src('pages/*.html')
  //锁定主体内容文件
  .pipe(wrap({src:'layout/default.html'}))
    //传递给 wrap 接口，里面用src指定布局文件的位置，
  .pipe(gulp.dest(".."));
  })
  ```

  - 首先在 `source` 文件夹下面新建两个文件夹 `layout` 和 `pages` 。
  - 然后在两个文件夹下面新建文件，例如 `layout/default.html` `pages/index.html` 和 `pages/about.html`。
  - 流程是这样的： `layout/default.html` 里面放的是各个页面的公共头部和底部的布局， `pages` 文件夹下面放的是各个页面的主体内容。
  - 结合上面的代码来看, `gulp.src` 锁定了 `pages` 文件夹下面的所有页面，然后把它们全部交给 `gulp-wrap` 这个包来注入头部和尾部，注入的头部和尾部在哪呢？ `.pipe(wrap({src:'layout/default.html'}))` 这条代码指定了公共样式布局的位置。
  最后把合并后的文件放到上一层目录，也就是 `flexbox` 文件夹下面。
  - 所以 `copy` 这个命令可以删除了，改成 `build` 。
  - `default.js` 里面 用 `<%= contents %>` 来代表注入的主体内容，这行代码放在哪里，主体内容就注入在哪里。

  >退出 gulp 监控任务 敲 `control+c`
  >

- 下面来设置自动刷新的包，需要安装 `browser-sync` 。然后 `gulpfile.js` 文件中加入下面的代码。

  ```js
  var browserSync = require('browser-sync');
  gulp.task('browser',['sass','build'],function(){
  browserSync({
    server:{
      baseDir: '..'
      //基地建在哪个文件夹中
      }
    });
  });
  ```

  - 需要注意的是，`gulp.task('default',['browser','watch']);` 这行代码的改变， `sass` `build` 指令加在了 `browser` 指令的前面，如上面的代码。
  - `baseDir` 的值是一个目录，也就是你主页所在的文件夹。

- 上面的代码还不能实现自动刷新的功能，下面来添加上自动刷新，自动刷新有两种：一种是改变SCSS和另一种改变了HTML。
  - 按照上面的思路，我们修改完毕代码后的数据流需要交给 `browserSync` 来处理。

    ```js
    gulp.task('sass', function(){
      gulp.src('styles/main.scss')
      .pipe(sass()).on('error', handleError)
      .pipe(prefix())
      .pipe(gulp.dest('../styles'))
      .pipe(browserSync.reload({stream:true}));
      //新加的代码，修改sass文件后，把文件交给 `browserSync` 来自动刷新
    });

    gulp.task('rebuild',['build'],function(){
      browserSync.reload();
    })
    //新加的函数，为了在build 前刷新页面
    gulp.task('watch',function(){
    gulp.watch(['**/*.html'], ['rebuild']); //这一处改为rebuild
    gulp.watch(['styles/*.scss'], ['sass']);
  });
  ```
- 来实现多设备同时显示和刷新：
  - 首先命令行输入 `ifconfig|grep 192` 命令来查看局域网的IP地址，我的是 `192.168.1.102`
  - 然后输入 `gulp` 开启监控
  - 在手机输入 `192.168.1.102:3000`（浏览器也可以输入这个）就能实现多设备同步实时刷新啦。

- end

---

### 这个是 `gulpfile.js` 文件中完整的配置代码

```js
var gulp = require('gulp');
var sass = require('gulp-sass');
var prefix = require('gulp-autoprefixer');
var wrap = require('gulp-wrap');
var browserSync = require('browser-sync');

gulp.task('browser',['sass','build'],function(){
  browserSync({
    server:{
      baseDir: '..'
    }
  });
});

function handleError(err) {
console.log(err.toString());
this.emit('end');
}

gulp.task('build', function(){
  gulp.src('pages/*.html')
  .pipe(wrap({src:'layout/default.html'}))
  .pipe(gulp.dest(".."));
});

gulp.task('sass', function(){
  gulp.src('styles/main.scss')
  .pipe(sass()).on('error', handleError)
  .pipe(prefix())
  .pipe(gulp.dest('../styles'))
  .pipe(browserSync.reload({stream:true}));
});

gulp.task('rebuild',['build'],function(){
  browserSync.reload();
})

gulp.task('watch',function(){
  gulp.watch(['**/*.html'], ['rebuild']);
  gulp.watch(['styles/*.scss'], ['sass']);
});

gulp.task('default',['browser','watch']);
```
