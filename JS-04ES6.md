### es6内容
+ 1、模板字符串
```js
    const person = {
      name: '张三',
      age: 26
    };
    var s1 = `my name is ${person.name}, i am ${person.age}!`;
```
  `使用场景`
  + 字符拼接
  + 插入变量，更简洁
  + 字符串换行
  
+ 2、解构赋值
```js
    // 场景1、变量赋值
    var obj = { name: '张三', age: 19};
    var { name , age } = obj;
    console.log(name, age); 
    // 张三  19


    // 场景2、拓展
    var obj2 = { name: '李四', age: 33};
    var obj3 = { context: 'this', sex: 'man' };

    function fn({name, age}, {context, sex}) {
      console.log(name, age, context, sex);
    }
    fn(obj2, obj3);

    // 场景3、修改变量名

    var obj3 = { name: 'wangwu' };
    var { name: obj3Name} = obj3;
    console.log(obj3Name);  // wangwu
``` 
+ 3、函数rest参数  
```js
    function fn () {
      console.log(arguments.length);
    }
    fn(2, 4, 6);
    // es6 箭头函数内部不能使用arguments
    // 为了弥补这个问题，rest参数应运而生 

    // ...args就是rest参数
    // --> 产生了一个变量，这个变量是一个数组，数组立案包含了这个函数调用时传递的所有参数
    function q(...args) {
      console.log(args instanceof Array);
      console.log(args);
    }
    q(1, 3, 5);
    q(2, 3, 4, 5, 56, 6);
```

### 补充， 判断数据类型
+ typeof 
  - typeof 只能判断：数字、字符串、布尔值、undefined、函数

+ `Object.prototype.toString.call()`
  - 5     '[object Number]'
  - true    '[object Boolean]'
  - "abc"     '[objetc String]'
  - null    '[object Null]'
  - undefind    '[object Undefined]'
  - [1, 2, 3]     [object Array]'
  - function() {}     '[object Function]'
  - new Date()    '[object Date]'
  - /abc/     '[object RegExp]'

### 箭头函数
+ 场景：用于替换匿名函数
+ 基本用法：
```js
    //匿名函数
    div.onclick=function(){
        console.log("你好")
    }
    //箭头函数
    div.onclick=()=>{
        console.log("你好")
    }
```
+ 有一个参数的箭头函数
```js
    var fn=(a)=>{
        console.log("abc");
    }
    //等价于：
    var fn=a=>{
        console.log("abc");
    }
```
+ 有2个及更多参数的箭头函数
```js
    var f=(a,b,c)=>{
        console.log("abc")
    }
```
+ 箭头函数和普通函数有哪些不同？(4点)
  - 函数体内的`this`对象，就是定义时所在的对象，而不是使用时所在的对象.
  - 不可以当作构造函数，也就是说，不可以使用`new`命令，否则会抛出一个错误
  - 不可以使用`arguments`对象，该对象在函数体内不存在。如果要用可以用 rest 参数代替，即 `(...args) => { console.log(args); }`
  - 不可以使用 `yield`命令，因此箭头函数不用用作 `Genertator`函数
### 对象的Object.assign  
+ `Object.assign()` 就是进行对象的浅拷贝
```js
    var source = {
      age: 18,
      height: 170,
      className: '三年二班'
    };
    // 克隆一个新对象
    var newObj = Object.assign({}, source);

    // 使用对象扩展运算符进行浅拷贝
    var newObj2 = { ...source, age: 30 };
    console.log(newObj2); // { age: 30, height: 170, className: '三年二班'}

    // 使用扩展运算符拷贝数组
    var arr1 = [1, 2, 3, 4];
    var arr2 = [ ...arr1, 6];
    console.log(arr2); // [1, 2, 3, 4, 6]

```
## promise 
### 为什么要有Promise： 解决（地狱回调）的问题
### 地狱回调：
```js
    $.get('/api/getuser', function(res) {
      $.get('/api/getuserInfor', function(res2) {
        $.get('/api/getuserInforDetail', function(res3) {
          // ....
        })
      })
    })

    // node开发： 读取文件、开个服务器、接收一个请求。。。。
```

### Promise函数的基本用法
```js
    var promise1 = new Promise(function(resolve, reject) {
    setTimeout(function() {
        resolve('foo');
      }, 300);
    });

    promise1.then(function(value) {
      console.log(value);
      // expected output: "foo"
    });

    console.log(promise1);
```
+ 解决地狱回调
```js
    function fn1() {
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          console.log('第一步');
          resolve();
        }, 1000)
      })
    }

    function fn2() {
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          console.log('第二步');
          resolve();
        }, 1000)
      })
    }

    fn1().then(res => {
      return fn2();
    }).then(res2 => {
      console.log('完成');
    });
```

### Promise方法
+ `Promise.all(iterable)` 这个方法返回一个新的promise对象，该promise对象在iterable参数对象里所有的promise对象都成功的时候才会触发成功，一旦有任何一个iterable里面的promise对象失败则立即触发该promise对象的失败。
+ `Promise.reject(reason)` 返回一个状态为失败的Promise对象，并将给定的失败信息传递给对应的处理方法
+ `Promise.resolve(value)` Promise.resolve(value) 来返回一个Promise对象,这样就能将该value以Promise对象形式使用。
+ `Promise.try()` ES6中增加

### Promise回调处理
+ 第一种方式
```js
    function fn() {
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          console.log('获取数据');
          resolve('ok');
        }, 1000);
      })
    }

    fn().then(function(res) {
      // resolve 执行第一个函数
      console.log(res);
    }， function(err) {
      // reject 执行第二个函数
      console.log(err);
    })

```
+ 第二种方式
```js
    fn().then(res => {
      // promise 返回状态为resolve执行
      console.log(res);
    }).catch(err => {
      // promise 返回状态为reject执行
      console.log(err);
    })

```

+ 总结
  - 上面两种错误处理方式，第二种更加推荐
  - 第二种方式不仅可以捕捉到reject传递的参数，还能捕获到成功的回调中发生的错误；
+ 6、generator 
## async 
+ async 函数是什么？一句话，它就是 Generator 函数的语法糖。
```js
  async function get() {
    console.log('开始执行');
    var res = await timer();
    console.log('执行结果', res);
  }

  function timer() {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve('你好');
      }, 1000);
    })
  }

  get();
```

+ `await` 可以执行异步操作，但是`await`必须在`async`函数内执行
+ `await`操作可以有返回值，这个返回值表示`promise`操作成功的返回值
+ 如果`await`里面执行的异步操作发生了`reject`，活着发生了错误，那么只能用`try...catch`语法来进行错误处理。
## class 类
+ 类的创建
```js
    // 构造函数方法创建
    function Person(name, age) {
      this.name = name;
      this.age = age;
    }

    var p1 = new Person('王二麻子', 8);


    // 使用类方法创建
    class Student{
      constructor(name, age) {
        this.name = name;
        this.age = age;
      }
    }
    var s1 = new Student('爱因斯坦', 99);

```

+ 类的方法
```js 
    // 构造函数
    function Person(name, age) {
      this.name = name;
      this.age = age;
    }
    Person.prototype.run = function() {
      console.log(this.name, '跑了');
    }

    // 类
    class Student {
      constructor(name, age) {
        this.name = name;
        this.age = age;
      }
      run() {
        console.log(this.name, '跑了')
      }
    }
    var s1 = new Student('张三'， 26)；

```

### 类的静态方法
+ 静态成员： 静态属性、静态方法
+ 静态属性： 通过类本身来访问，`Person.maxAge`;
+ 静态方法： 通过类本身来访问的一个方法： `Person.born()`;
```js
  class Animal {
    constructor() {

    }
    // 这就是一个静态方法
    static born() {
      console.log('一个monster对象诞生了');
    }
  }
  // 访问静态方法
  Animal.born();
```

### 类的继承
```js
    // 父类
    class Person {
      constructor(name) {
        this.name = name;
      }
    }

    // Student类继承自Person类
    class Student extends Person {
      // 构造方法
      constructor(name, grade) {
        // 规定： 必须调用父类的构造方法，如果不调用会报错
        super(name);  
        // 调用父类的构造方法，从而给子类的实例添加了name属性
        this.grade = grade;
      }
    }
```
+ 9、module

