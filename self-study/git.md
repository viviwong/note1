---
title: git
---


### 下载git

[git主页](https://git-scm.com/)
- mac
1.安装包版本管理工具homebrew 
2.安装git brew install git
3.查看版本号 git --version

- window
4.[window版下载地址](https://git-scm.com/download/win) 
5.安装


创建目录 mkdir
查看文件 ls


### 本地文件上传到github上 

文件变成本地git仓库       git init
查看所有文件          ls -a 本地多了一个文件夹 .git存放改版历史
所有文件添加到git版本历史中   git add -A 
制作版本            git commit -m "版本留言"
SSH和HTTPS地址作用         连接远程github仓库
SSH地址每次上传不需要输入密码  需要添加SSHKEY
临时使用用HTTPS

HTTPS连接远程github地址     git remote add origin https//
上传到github中          git push -u origin master 

本地内容提交到github中三步      git add -A 
                git commit -m"message" 
                git push

create a new repository on the command line
echo "# xiaohaizi123" >> README.md
git init
git add README.md
git commit -m "first commit"
git remote add origin https://github.com/jasonboy345/xiaohaizi123.git
git push -u origin master

push an existing repository from the command line
git remote add origin https://github.com/jasonboy345/xiaohaizi123.git
git push -u origin master


### gitpage

gitpage项目站点形式托管页面 页面不能放到master分支上
才能新分支         git checkout -b gh-pages   分支名必须叫gh-pages
查看分支        git branch
本地分支推送到远端   git push -u origin gh-pages
gitpage页面url      http://用户名.github.io/项目名





***

### 为何移动设备优先？

移动优先当代响应式设计的最佳流程：在制作响应式网站的时候，先搞定手机版，然后再去为更大设备去设计和开发。
无论从界面设计还是代码执行效率的角度而言，移动优先都有明显优势。

- 由简入繁

### 定制自己的色盘

- [Material Design Color Palette](https://www.materialpalette.com/cyan/pink) 选择两种颜色，第一种为主色，第二种为强调色。然后下载 SASS版本。
- 色盘上各颜色的作用:
  - primary color:主色,一般和白色和灰色混用，来显示页面的主体背景.他和白色灰色加起来要占到页面百分之八九十的面积。
  - accent color:强调色,来强调最重要的一些按钮或者是表单的位置，来突出显示。
  - text/icons:字体和图片颜色。
  - primary text:主要的字体颜色。
  - secondary text:从属内容字体颜色。
  - divider color:分割线的颜色。
  - dark primary color 和 light primary color:深主色和浅主色

### 什么是响应式？

*简单定义*

一份代码能够适应全部屏幕尺寸

*响应式的三要素*

- 弹性布局
- 弹性图片
- media 查询

### 四中常见的响应式模式

#### Column Drop列下沉
手机上每一个大块单独占据一行，随着屏幕尺寸拉伸会在同一行上形成多个 column 列
![Column Drop images](./reponsiveImg/column-drop.png)

#### Mostly Fuild
基本上跟 Column Drop 一样，但是有一点点“固定布局“的特点：当到达一定宽度后，主体内容部分不再变宽，成为固定宽度。
![Mostly Fuild images](./reponsiveImg/mostly-fuild.png)

#### Layout Shifter
变换式，也就是不必遵循原有内容顺序，可以根据最佳展示需要来调整大块顺序。
![Layout Shifter images](./reponsiveImg/layout-shifter.png)

#### Off Canvas
抽屉式，屏幕不够宽的时候，隐藏，通过按钮呼出。足够宽的屏幕上，始终显示。
![Off Canvas images](./reponsiveImg/off-canvas.png)

### viewport 设置

#### 问题是什么？
手机上任何设备都按照 960 的宽度来显示，造成很多设备上字体变成了原来的 1/3 。

#### 解决方案

```html
<meta name="viewport" content="width=device-width">
```
viewport 就是浏览器窗口。这个设置翻译成大白话就是：浏览器呀，你就按照你的自然宽度来来给我显示网站就行了，不要自作聪明。

这样就解决了上面所说的问题了。意思是把我的视窗 viewport 的宽度，设置为设备的实际宽度，比如说前面提到的 iphone5 ，本来默认视窗宽度是 960px ，那么有了这一行设置之后，视窗宽度就变成了设备实际宽度了 320px 。这样后面再来显示页面元素，当然就不会缩小了。

更为常见的设置:

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```
设备在水平放置的时候（手机横屏），字体会无缘无故的变的很大，所以需要加上 `initial-scale=1.0` (初始化缩放等于1) 这个设置。

### 像素尺寸
iphone5 对外宣传像素 640*1136像素 是物理像素，而我们实际开发中px是逻辑像素，两则之间的关系是不一样的。

- px: css pixels 逻辑像素，浏览器使用的抽象单位。
  - 可以根据不同的设备来变大变小。
- dp,pt: device independent pixels 设备无关像素
  - 物理像素的大小是固定的
- dpr: devicePixelRatio 设备像素缩放比
  - 来控制px 与 dp pt 之间的关系，计算公式 1px = dpr<sup>2</sup> * dp

>为什么iphone5是320px*568px? ==> 因为 dpr = 2
>

![px-devic-img](./reponsiveImg/px-devic.png)

*平面上*:1px = (2)<sup>2</sup> * dp

  - 也就是1个css像素等于4个物理像素，如上图。

*纬度上*:1px = 2 * dp

*因此*: 640dp*1136dp ==> 320px*568px

### 为什么iphone5 的dpr为2？

- DPI:打印机每英寸可以喷的墨汁点（印刷行业）
- PPI:屏幕每英寸的像素数量，即单位英寸内的像素密度
  - 目前，在计算机显示设备参数描述上，二者意思表达的是一样的。

*计算公式* :以 iphone5 为例子

ppi= &radic;(1136<sup>2</sup>dp + 640<sup>2</sup>dp) / 4 = 326ppi (视网膜Retina屏)

>PPI越高，像素越高，图像越清晰，但可视度越低，系统默认设置缩放比越大
>

![ppi-img](./reponsiveImg/ppi.png)

Retina 屏（高清屏）:dpr都是大于等于2

*整个换算流程以 iphone5 为例子 :*

设备分辨率 _1136*640 dp_ ==>

_&radic;(1136<sup>2</sup>dp + 640<sup>2</sup>dp) / 4 = 326ppi_ ==>

326 ppi属于 retina屏幕, _dpr=2_ ==>

_1px = dpr<sup>2</sup> * dp_ ==>

_iphone5_ 的屏幕为 _320 * 568 px_
