# JavaScript语言精粹


JavaScript语言精粹

<!--more-->

# JavaScript
## Ch01_语句
### 注释
在js中，那些字符对也有可能出现在正则表达式字面量中，所有块注释对于被注释的代码块来说是不安全的。例如
```js
/*
    var rm_a = /a*/.match(s);
*/
```
所以避免使用`/* */`，而用`//`注释代替它。
### 数字
- js只有一个数字类型，它在内部被表示为64位的浮点数。
- 它没有分离出整数类型，所以1和1.0的值相同
- 100和1e2是相同的数字
- NaN是一个数值，它表示一个不能产生正常结果的运算结果。NaN不等于任何值，包括它自己。可以用函数`isNaN(number)`检测NaN。
### 字符串
- 一般建议使用双引号来声明字符串
- 字符串是不可变的
- 两个包含完全相同的字符且字符顺序也相同的字符串被认为是相同的字符串

```js
"c" + "a" + "t" === "cat" //true
```

### 语句
- js中的代码块（狭义）不会创建新的作用域
- if判断，以下值为假 false,null,undefined,'',0,NaN

## Ch02_对象
Javascript中的对象是可变的键控集合。

在JavaScript中，数组是对象，函数是对象，正则表达式是对象，对象自然也是对象。

### 对象字面量
一个对象字面量就是包围在一对花括号中的零或多个“名/值”对。
- 优先使用dot获取属性值

### 引用
对象通过引用来传递
```js
var a = {}, b = {}, c = {}; //abc每个都引用一个不同的空对象
a = b = c = {} //abc都引用同一个空对象
```

### 原型
如果一个对象没有此属性名，就会从原型中找直到Object.prototype。

### 反射
检查对象并确定对象有什么属性
```js
typeof person.name   //'string'

typeof person.toString //'function'
typeof person.constructor //'function'
```
typeof会沿着原型链找

hasOwnProperty 方法不会检查原型链
```js
person.hasOwnProperty('number') //true
person.hasOwnProperty('constructor')//false
```

### 枚举
排除函数

```js
var name;
for (name in another_stooge){
    if (typeof another_stooge[name] !== 'function'){
        document.writeln(name + ': ' + another_stooge[name]);
    }
}
```

选取想要的属性

```js
var i;
var properties = [
    'first-name',
    'middle-name',
    'last-name',
    'profession'
];

for (i = 0; i < properties.length; i++){
    document.writeln(properties[i] + ': ' + another_stooge[properties[i]]);
}
```

### 删除
不会触及原型链中的任何对象。
删除对象的属性可能会让来自原型链中的属性透现出来
`delete another_stooge.nickname;`

### 减少全局变量污染
最小化使用全局变量的方法之一是为你的应用只创建一个唯一的全局变量：`var MYAPP = {}`

```js
MYAPP.stooge = {};
MYAPP.flight = {};
```

## Ch03_函数
- 对象字面量产生的对象  连接到`Object.prototype`。
- 函数对象  连接到`Function.prototype`
(该原型对象本身连接到Object.prototype)。
- 每个函数在创建时会附加两个隐藏属性：函数的上下文和实现函数行为的代码。
### 函数字面量
```js
var add = function(a, b){
    return a + b;
};
```
通过函数字面量创建的函数对象包含一个连到外部上下文的连接。这杯称为闭包。
### 调用
每个函数还接受两个附加的参数：this和arguments。
this的值取决于调用的模式
在JS中一共有4种调用模式：
- 方法调用模式
- 函数调用模式
- 构造器调用模式
- apply调用模式
#### 方法调用模式

当一个函数被保存为对象的一个属性时，我们称它为一个方法。

当一个方法被调用时，this被绑定到该对象。

```js
var myObject = {
    value: 0,
    increment: function(inc){
        this.value += typeof inc === 'number' ? inc : 1;
    }
};

myObject.increment(2);
document.writeln(myObject.value); //3
```

方法可以使用this访问自己所属的对象，所以它能从对象中取值或对对象进行修改

this到对象的绑定发生在调用的时候。

这个vert late binding 使得函数可以对this高度复用。

通过this可以取得所属对象上下文的方法称为public method

#### 函数调用模式
当一个函数并非一个对象的属性时，那么它就是被当做一个函数来调用。

以此模式调用函数时，this被绑定到全局对象。（这是一个语言设计的错误，我们不能通过用内部函数来帮助外部函数为对象工作，this应该仍然绑定到外部函数的this变量）

```js
var myObject = {
    value: 0,
    increment: function(inc){
        this.value += typeof inc === 'number' ? inc : 1;
    }
};

myObject.double = function(){
    var that = this;

    var helper = function (){
        that.value = add(that.value, that.value);
    }

    helper();
}
myObject.double();
document.writeln(myObject.value); //6
```

#### 构造器调用模式
当在一个函数前面带上new来调用，那么背地里将会创建一个连接到该函数的prototype成员的新对象，同时this会被绑定到那个新对象上。

```js
// 创建一个名为Quo的构造器函数
var Quo = function(string){
    this.status = string;
};
// 给Quo的所有实例提供一个名为get_status的公共方法。
Quo.prototype.get_status = function(){
    return this.status;
};
//构造一个Quo实例
var myQuo = new Quo("confused");
```

#### Apply调用模式
apply方法让我们构建一个参数数组传递给调用函数。
它也允许我们选择this的值。

apply方法接受两个参数，第1个是要绑定给this 的值，第2个就是一个参数数组。

```js
var array = [3, 4];
var sum = add.apply(null, array);
//-------------
var statusObject = {
    status: 'A-OK'
};
var status = Quo.prototype.get_status.apply(statusObject);
```


