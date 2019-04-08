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

## 补充， 判断数据类型

+ 3、箭头函数  
    - 箭头函数和普通函数有哪些不同？(4点)
+ 4、对象的Object.assign  
+ 5、promise 
+ 6、generator 
+ 7、async 
+ 8、class 
+ 9、module

### 原型
+ 原型很多人开发用不到？
    - 很多人都用es6/7/8开发，确实用的比较少
    - 如果你用es5之前的版本开发代码(IE8、IE7。。。)，可能天天都要写原型
    - 理解了原型，才是理解了JS面向对象的核心
+ 类继承其实本质上还是用原型继承来(包装)的