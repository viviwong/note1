---
title: bind super()理解
---

### bind
this 在不同的执行上下文中指向不同的对象，这样往往会造成 undefined 错误，bind 要解决的就是明确函数在执行的时候 this 的指向。
不使用bind,下面的函数执行会报错

```js
var Person = {
  name: 'yewenxiang',
  talk: function() {
    console.log(`My name is ${this.name}`);
  }
}

Person.talk();
let plzTalk = Person.talk;
plzTalk();

//------------------------等价于

let plzTalk = function() {
  console.log(`My name is ${this.name}`);
}
plzTalk();
```

使用bind之后:

```js
var Person = {
  name: 'yewenxiang'
}
let plzTalk = function() {
    console.log(`My name is ${this.name}`);
  };
plzTalk.bind(Person)();
```
总结一下： bind 的作用就是把一个对象（作为 bind 的参数传入），绑定到这个函数中的 this 之上。

再举个例子加深理解：

```js
function talk() {
  console.log(this.sound);
}
let Person = {
  sound: 'Hi there!',
  speak: talk
}
Person.speak(); // 可以正确输出 sound 的值

//----------------------------等价于

let Person = {
  sound: 'Hi there!',
  speak: function () {
            console.log(this.sound);
        }
}
Person.speak();
```

### super调用父类方法

super 代表父类。主要有两个用途：
- 使用 super() 呼叫父类的 constructor()
- 使用 super.functionName() 调用父类中的 static 方法

##### super()的作用
子类必须在 constructor 方法中调用 super 方法，否则新建实例时会报错。这是因为子类没有自己的 this 对象，而是继承父类的 this 对象，然后对其进行加工。如果不调用 super 方法，子类就得不到 this 对象。
ES6的继承机制，实质是先创造父类的实例对象 this（所以必须先调用 super 方法），然后再用子类的构造函数修改 this。

```js
class Father {
  constructor(familyName){
    this.familyName = familyName;
  }
  static sayHello() {
    console.log('hello');
  }
}
class Son extends Father {
  constructor() {
    super();
    this.height = 170; // 没有上一行的 super() ，这里的 this 就不让用
  }

  static hello() {
    super.sayHello(); // 调用父类的静态方法
  }
}
let tom = new Son('Wang', 160);
console.log(Son.hello());
```
