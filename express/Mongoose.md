---
title: mongoose 笔记 (如何在 JS 代码中来操作 mongodb)
---

Mongoose 是一个 JS 库，作用是把 MongoDB 的数据，封装成 JS 对象。便于 我们用 JS 代码来操作 Mongodb 。
有了 Mongoose 之后，我们就可以用 JS 代码来进行增删改查了。

- [中文参考](http://ourjs.com/detail/53ad24edb984bb4659000013)
- [官网](http://mongoosejs.com/)
- [nodeclass](http://www.nodeclass.com/api/mongoose.html#quick_start)
- [操作步骤](http://haoqicat.com/react-express-api/3-mongoose)

### 装包

安装到后台 express 代码中。

```bash
npm install --save mongoose
```

### 链接数据库

```js
//index.js
const mongoose = require('mongoose');
mongoose.connect('mongodb://localhost:27017/digicity');
// 执行此行代码之前，要保证 mongodb 数据库已经运行了,有一个 database 叫做 digicity 。，而且运行在 27017 端口.
var db = mongoose.connection;
db.on('error', console.log);
db.once('open', function() {
  console.log('success!')
});
//连接数据库成功，就打印 success
```

### 创建数据模型

创建一个专门的　models/user.js ，里面代码的作用是，实现一个对象跟一个 数据集合之间映射关系。（相当于一个数据的对象格式）

```js
//models/user.js
var mongoose = require('mongoose');
var Schema = mongoose.Schema;
const UserSchema = new Schema(
  {
    username: { type: String },
    email: { type: String }
  }
);
module.exports = mongoose.model('User', UserSchema);
// `User` 会自动对应数据库中的　users 这个集合
// 如果这里是　Apple 那么就会对应　apples 集合
// 如果这里是　Person 那么就会对应　people 集合
```

- Schema 就是用来描述这个集合中每个文档 的数据结构,具体来讲就是
  - 有哪几个字段（　field ）？我们这里有两个　`username` 和　`email`
  - 每个字段的类型
这样，映射关系就建立完毕。

### 连接数据库成功后 插入一个新文档。

```js
let User = require('./models/user');
//...
db.once('open', function() {
   let user = new User({username: 'inCode', email: 'inCode@incode.com'});
   user.save(function(err){
     if(err) console.log(err);
   })
  console.log('success!')
});
```

> 强调：｀User｀ 对应　users 集合，它本身是　models/user.js 中导出的一个 model 。所以说　User 就是一个空盒子。而小写的 user 是　new User 得到的一个对象，对应一个实际的文档，所以其中会真正保存一个用户的实际　username 和 email 数据。
user.save 就是把　user 中已经有的数据（在内存中），真正保存到　MongoDB 数据库中（保存到硬盘上）。
>

###  Mongoose 来查询所有用户

- [Mongoose API 文档](http://mongoosejs.com/docs/api.html)
- [参考](http://haoqicat.com/react-express-api/5-rest-api)

```js
//index.js
db.once('open', function() {
  User.find().exec(function(err, users) {
    console.log(users);
  });
});
```
`find()` 接口是一个 **异步函数** ，所以它的返回值　`users` 只能 在回调函数中使用。`.exec` 字面意思就是执行，我们把回调函数传给它做参数。

### 如何把数据库的数据显示在前端页面？

上面数据虽然拿到，但是如果想提供给客户端使用：

- 第一步，把它要封装成一个　API
- 第二步，数据格式转换为　JSON

```js
app.get("/users",function(req,res){
  User.find().exec(function(err, users) {
    res.json({users})
    // res.send(users);
  });
});
```
 API做好之后 curl 测试 api，得到数据再去写前端的代码。

```bash
$ curl -X GET http://localhost:3000/users
```

### 名词解释

- Schema  ：  一种以文件形式存储的数据库模型骨架，不具备数据库的操作能力
- Model   ：  由Schema发布生成的模型，具有抽象属性和行为的数据库操作对
- Entity  ：  由Model创建的实体，他的操作也会影响数据库

Schema、Model、Entity的关系请牢记，Schema生成Model，Model创造Entity，Model和Entity都可对数据库操作造成影响，但Model比Entity更具操作性。
