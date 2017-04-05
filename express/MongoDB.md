---
title: MongoDB如何搭建数据库
---

### MongoDB 基本概念

- [ MongoDB 的官网](https://www.mongodb.com/)
- [ MongoDB 中文社区](http://www.mongoing.com/)
- [ MongoDB 中文网](http://www.mongodb.org.cn/)
- MongoDB：是一个数据库软件，有时候我们简称它叫一个数据库，但是其实一个 MongoDB 运行起来 可以里面同时运行多个数据库
- Database: 数据库。一般做法是，一个项目对应一个数据库。
- Collection: 集合。类似于关系型数据库下的表的概念，例如全班同学信息。
- Document：文档。一个集合中会包含多个文档（一个文档中存储一个同学的信息）。文档对应关系型数据库中的 记录 这个概念。

![mongodbstructure](./img/mongodbstructure.png)

![mongovue](./img/mongovue.png)

### 安装

```bash
brew install mongodb
```

### MongoDB启动与使用

##### 启动

```bash
mkdir -p data/db
mongod --dbpath=./data/db
```
一般在项目文件夹主目录建立一个  `data/db` 文件夹来当做数据库地址。
`mkdir -p`  创建一个目录的时候，若其父目录不存在，则自动创建，而不是默认的报错,例如： `mkdir /home/a/b` 若`/home`目录下不存在a目录，则会报错。加上-p选项后，就会先建立a目录，然后在a目录下再建立b目录。

- 注意：`--dbpath`后的值表示数据库文件的存储路径,而且后面的路径必须事先创建好，必须已经 **存在** ，否则服务开启失败。
- 注意：这个命令窗体绝对不能关,关闭这个窗口就相当于停止了mongodb服务
- 也可以在命令后面加上参数 `--port 27017` 来指定端口

一直处于运行状态，说明 MongoDB 数据库可以使用了。查看 mongod 命令的帮助文档，可以在命令行中输入命令 `mongod -h`

##### 使用

既然 MongoDB 已经启动了，那如何操作它呢？通过 MongoDB 提供的 mongo shell 工具，可以很方便的和 MongoDB 数据库进行通信，也可以使用图形化的界面。

### 使用 mongo shell 工具操作数据库

首先要确保在MongoDB 数据库运行的状态下，才能启动 mongo shell，在命令行中输入:

```
mongo
```

切换到 mongo shell 运行环境，在这里可以调用 MongoDB 提供的接口操作数据库中存储的数据

- `show dbs` 查看所有的数据库名称
- `db 或 db.getName()` 查看当前使用的数据库,db代表的是当前数据库。
- `use react-express-api` 创建新的数据库 react-express-api,但是`react-express-api`数据库并不存在，只有当数据存入数据库时候才会真正的创建数据库,如果此数据库存在，则切换到此数据库下。
- `db.createCollection('posts')` 在数据库中创建一个新的 `collection`,例子中的是 `posts` 集合。
- 往posts 集合存入数据

```js
db.posts.insert({category: 'db', title: 'learning mongodb', content: 'mongodb is a nosql database'})
```

- `db.posts.find()` 查找 posts 集合中的所有记录
- 更新 posts 集合中的一条记录

```
db.posts.update({_id: ObjectId('xxx')}, {$set: {title: 'mongodb'}})
```

- `db.posts.remove({_id: ObjectId('xxx')})` 删除 posts 集合中的一条记录
- `db.posts.remove({})` 删除 posts 集合中的所有记录
- 删除数据库 react-express-api

```bash
use react-express-api
db.dropDatabase()
```

### 使用图形化界面

-  是一个用 express 技术开发的，MongoDB 的 GUI (图形界面)。可以方便美观的 操作 MongoDB 中的数据。
- 参考：http://haoqicat.com/hand-in-hand-react/4-mongo-express

```bash
npm install -g mongo-express
```
mongo-express 装好之后，我们需要通知它到底要连接到哪个数据库，通过修改 mongo-express 的配置文件来搞定。

首先下面的这个命令可以帮我们找到 mongo-express 的安装位置：

```bash
$ npm list -g mongo-express
/Users/wenxiangye/.nvm/versions/node/v7.4.0/lib
```

找到后就可以进入安装文件夹来修改配置文件了。

```bash
cd /Users/wenxiangye/.nvm/versions/node/v7.4.0/lib
cd node_modules
cd mongo-express
cp config.default.js config.js
```

最后一步，就是把示例配置文件 config.defualt.js（这个名字程序是不会读的） ，改名为真实的配置文件　config.js , 也就是说是程序会自动读到的配置文件。
打开配置文件，把

```js
mongo = {
  db:       'db',
  username: 'admin',
  password: 'pass',
  ...
  url:      'mongodb://localhost:27017/db',
};
```
改为

```js
mongo = {
  db:       'digicity',
  username: '',
  password: '',
  ...
  url:      'mongodb://localhost:27017/digicity',
};
```
上面的　digicity 就是我们要操作的数据库的名字，这个是通过　mongo shell 中，执行 `show dbs` 看到的。由于我们的 digicity 这个数据库本身没有设置密码，所以上面 username 和 password 两项都改成空字符串就可以了。
同时，mongo-express 这个软件自己还有自己登陆的用户名和密码，并且有默认值，通过　config.js 中这几行：

```js
basicAuth: {
  username: process.env.ME_CONFIG_BASICAUTH_USERNAME || 'admin',
  password: process.env.ME_CONFIG_BASICAUTH_PASSWORD || 'pass',
},
```
默认的用户名是　admin ，密码是　pass 。
在命令行中启动 mongo-express

```bash
$ mongo-express
```
浏览器中打开 http://localhost:8081 可以开始使用 mongo-express 了。
