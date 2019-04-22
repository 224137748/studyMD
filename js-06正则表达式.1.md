# 正则表达式 js java c# python
## 学习网站
`https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp`
## 概念
+ 正则表达式是为了解决字符串操作中一些比较麻烦的场景，比如字符串提取、字符串替换、字符串验证等
    - 常用于：表单验证：是否是一个数字、是一个电话号码、是一个邮箱。。。
    - 字符串替换：
    - 字符串查找

## 正则表达式其实也是js里面的一个对象

## 创建一个正则表达式
+ `var reg2=new RegExp("a");`
+ `var reg1=/a/;`   
    - 其中//是标记，a表示要匹配的字符

## 正则表达式匹配
```js
    var reg=/a/;        //匹配字符串中的a字符
    reg.test("abc");        
    //正则表达式的test用来检测字符串是否满足正则表达式的要求；
    //由于"abc"中存在a，所以是符合要求的，所以这段代码的返回值为：true

    const isTrue = reg.test("coke");   //由于"coke"中不存在a字符，所以不符合条件   -->isTrue:false


    var reg2=/abc/;     
    reg2.test("adbcd"); //字符串中必然存在"abc"才符合要求，所以这段代码的返回值为false

    reg2.test("1abc");  //true
    reg2.test("abc123");    //true
    reg2.test("abcabc");    //true
```

## 正则表达式特殊字符
### 字符类别
+ . 表示除了换行符(\r \n)以外的任意字符
```js
    var reg=/./;
    reg.test("a");  //true
    reg.test(".");  //true
    reg.test("\r"); //false
    reg.test("1\r");    //存在一个1，不是换行符，所以结果为：true
    reg.test("\r\n");   //false

    //注意点：如果以后要匹配.字符，不要直接写.
    //为了匹配qq邮箱，写了 /qq.com/ 这是一个错误的写法：
    //=>/qq.com/不仅匹配："qq.com"也匹配"qq3com" "qq@com"

    //解决方案：/qq\.com/  或者 /qq[.]com/
```

+ \d 表示数字
```js
    var reg=/\d/;
    reg.test("123abc"); //由于存在一个数字，所以结果为true
    reg.test("abcde");  //由于不存在任何数字，所以结果为false

    //扩展：
    var _reg=/\d\d/;
    _reg.test("1b");    //不存在2个连续数字，所以结果为：false
    _reg.test("a12c");  //存在2个连续数字，结果为：true

    //匹配手机号码：出现了连续11个数字
    var isCellphoneNumber=/\d\d\d\d\d\d\d\d\d\d\d/;
```

+ 案例：匹配手机号码
```js
    var reg = /\d\d\d\d\d\d\d\d\d\d\d/;
    reg.test("13532159091");    //true
    reg.test("17380889395");    //true
    reg.test("a7380889395");        //false
```

+ \D 是\d的取反结果，表示不是数字
```js
    var reg=/\D/;
    _reg.test("123");   //由于全是数字，所以结果为：false
    _reg.test("123a");  //存在一个字母，所以结果为：true

    var reg2=/123\D5/;
    _reg.test("12345"); //false
    _reg.test("123b5"); //true
```

+ \w 匹配字母、数字、下划线 ，三者满足其一即可
```js
    var reg=/\w/;   
    reg.test("123");    //存在数字，结果为：true
    reg.test("a");  //true
    reg.test("1");  //true
    reg.test("_");  //true
    reg.test("-");  //false
    reg.test(".");  //false

    reg.test("1.");   //true
    reg.test("_\r\n");  //true
    reg.test("\r\n");   //false
```

+ \W 是\w的取反

+ \s 表示一个空白字符（例如：\r \n \t 空格 。。。）
```js
    var reg=/\s/;
    reg.test("abc def");   //存在一个空白，结果为：true
    reg.test("abc\r123");   //存在一个\r，结果为：true
    reg.test("abc");    //false
```

+ \S 是\s的取反
```js
    var reg=/\S/;
    reg.test(" \r \n ");   //全是空白字符，结果为false
    reg.test("123 abc");    //存在一个空白，结果为true


    var reg3=/\s\S/;
    reg3.test(" a");    //true
    reg3.test("a ");    //false
    reg3.test("a b");   //true
```

+ \ 转义字符
```js
    var reg=/./;    //匹配除了换行符以外的任意字符
    var reg=/\./;   //匹配.字符

    var reg=/\//;   //匹配一个正斜杠：/
    var reg=/[/]/;  //。。。。。。。。。
```

+ 案例：匹配邮箱
```js
    //asfas.com
    //123ad.com
    //ABC12.com
    //abc_1.com
    var reg =/\w\w\w\w\w\.com/;
```


### 字符集合
+ []
```js
    var reg=/[1a2b3]/; //匹配这5个字符中的其中一个，只要满足其中，就是符合要求的
    reg.test("a");      //结果为：true
    reg.test("3");      //结果为：true
    reg.test("fg5678"); //一个符合要求的字符都不存在，结果为：false
    reg.test("a999999");    //结果为：true

    var reg2=/[123][abc]/;
    reg2.test("2a");    //true
    reg2.test("3c");    //true
    reg2.test("asdfasdf1c");    //存在符合条件的1c，结果为：true
    reg2.test("39");    //不满足

    var reg2=/[0-9]/;   //匹配数字0-9之间，任何一个数字 等价于：/\d/
    var reg3=/[a-z]/;   //匹配小写字母
    var reg4=/[A-Z]/;   //匹配大写字母
    var reg5=/[a-zA-Z]/;//匹配所有字母
    var reg6=/[A-Za-z]/;//..........

    var reg10=/[0-9abc]/;
    reg10.test("3");//true
    reg10.test("five");//false
    reg10.test("banana");//true
```
+ [^]
```js
    var reg=/[^123]/;   //不是1 并且 不是2 并且 不是3 
    reg.test("a");      //true
    reg.test("3");      //false
    reg.test("123");    //没有1，2，3以外的字符，结果为：false
```

### 边界
+ ^ 表示字符串最左边
+ $ 表示字符串最右边
```js
    var reg=/^abc/;     //表示以abc开头
    reg.test("123abc123");  //并不是以abc开头，结果为：false
    reg.test("abc123");     //以abc开头，结果为：true
```
```js
    var reg=/abc$/;     //表示以abc结尾
    reg.test("123abc"); //结果为：true
    reg.test("abc123"); //结果为：false
```

```js
    var reg=/^abc$/;    //表示字符串abc
    reg.test("abc");    //结果为：true
    reg.test("123abc"); //结果为：false
```

### 量词
+ `*` 出现0次或多次
```js
    var reg=/\d*/;
    reg.test("123");    //true
    reg.test("234");    

    var reg3=/1\d*/;
    reg3.test("123");   //true
    reg3.test("a123")   //true
    reg3.test("a1b");   //false
```
+ `+` 出现1次或多次
+ `?` 出现0次或一次
+ `{3}` 出现3次
+ `{3,}` 至少出现3次
+ `{3,5}` 出现3-5次
```js
    var reg=/abc{3}/;           //表示匹配abccc
    reg.test("123abccc123");    //结果为：true
```
+ `|` 或者
```js
    var reg=/abc|123/;  //表示匹配abc或者123
```
+ `()` 分组
```js
    var reg=/(\d{3-4})-(\d{7,8})/;
    var arr="021-33335555".match(/(\d{3,4})-(\d{7,8})/)
    arr[0]  //全部
    arr[1]  //区号：021
    arr[2]  //号码：333355555
```
```js
    var reg=/(\d{3-4})-(\d{7,8})/;
    "021-33335555".replace(/(\d{3,4})-(\d{7,8})/,'区号是：$1')
    //区号是：021
```

### 案例：匹配邮箱
### 案例：匹配电话座机