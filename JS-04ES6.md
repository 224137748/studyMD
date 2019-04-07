### es6内容
+ 1、模板字符串
```js
    const person = {
      name: '张三',
      age: 26
    };
    var s1 = `my name is ${person.name}, i am ${person.age}!`;
```
  + 字符拼接
  + 插入变量，更简洁
  + 字符串换行
+ 1、解构赋值   
+ 2、函数rest参数  
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