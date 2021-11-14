# typeof 运算符
在 JavaScript 内部，数组本质上只是一种特殊的对象。  
```js
typeof {}   // "object"
typeof []   // "object"
```  

使用instanceof运算符可以区分数组和对象。
```js
var o = {}
var a = []

o instanceof Array  // false
a instanceof Array  // true
```

# null 和 undefined
`null`是一个表示“空”的对象，转为数值时为0；`undefined`是一个表示"此处无定义"的原始值，转为数值时为NaN。  
```js
Number(null) // 0
5 + null // 5
Number(undefined) // NaN (Not a Number)
5 + undefined // NaN
```

# 与数值相关的全局方法
- `parseInt()`  
    解析数值：
    ```js
        parseInt('  +1.2e2') // 1
        parseInt(011)   // 9
        parseInt('011') // 11
        parseInt('0x10') // 16
        parseInt('') // NaN
    ```  
    转换进制：
    ```js
        parseInt(1000, 2) // 8
        // 等同于
        parseInt(string(1000), 2) // 8
        // 等同于
        parseInt('1000', 2) // 8
    ```
- `parseFloat()`
- `isNaN()`
- `isFinite()`

# Base64 转码
```js
var string = 'Hello World!';
btoa(string) // "SGVsbG8gV29ybGQh"
atob('SGVsbG8gV29ybGQh') // "Hello World!"
```  

要将非 ASCII 码字符转为 Base64 编码，必须中间插入一个转码环节，再使用这两个方法。
```js
function b64Encode(str) {
    return btoa(encodeURIComponent(str));
}

function b64Decode(str) {
    return decodeURIComponent(atob(str));
}

b64Encode('你好') // "JUU0JUJEJUEwJUU1JUE1JUJE"
b64Decode('JUU0JUJEJUEwJUU1JUE1JUJE') // "你好"
```

# 闭包 (closure)
在JS中，只有函数内部的子函数才能读取内部变量，因此可以把闭包简单理解成“定义在一个函数内部的函数”。闭包的最大特点就是可以“记住”诞生的环境。在本质上，闭包就是将函数内部和函数外部连接起来的一座桥梁。

**功能1**：读取外层函数内部的变量

JavaScript 语言具有"链式作用域"结构（chain scope），子对象会一级一级地向上寻找所有父对象的变量。样例实现了在`f1`外部读取其内部变量`n`。  
```js
function f1() {
  var n = 999;
  function f2() {
    console.log(n);
  }
  return f2;
}

var result = f1();
result(); // 999
```  

**功能2**：将外层函数内部的变量一直保存在内存中  
```js
funciton createIncrementor(start) {
    return function() {
        return start++;
    };
}

var inc = createIncrementor(5)

inc()   // 5
inc()   // 6
inc()   // 7
```  
闭包（`inc`）用到了外层变量（`start`），导致外层函数（`createIncrementor`）不能从内存释放，使得函数`createIncrementor`的内部环境一直存在。所以，闭包可以看作是函数内部作用域的一个接口。

```js
function makeAdder(a){
  return funciton(b){
    return a + b;
  }
}
var add5 = makeAdder(5);
var add10 = makeAdder(20);
add5(6); //11
add(7); //27

**功能3**：封装对象的私有属性和私有方法
```js
function Person(name) {
  var _age;
  function setAge(n) {
    _age = n;
  }
  function getAge() {
    return _age;
  }

  return {
    name: name,
    getAge: getAge,
    setAge: setAge
  };
}

var p1 = Person('张三');
p1.setAge(25);
p1.getAge() // 25
```

函数`Person`的内部变量`_age`，通过闭包`getAge`和`setAge`，变成了返回对象`p1`的私有变量。

# 立即调用函数表达式（IIFE）
JS 规定，如果function关键字出现在行首，一律解释成语句（statement）而非表达式（expression）。  
通常情况下，只对匿名函数使用“立即执行的函数表达式”。目的有两个：
1. 不必为函数命名，避免全局污染
2. IIFE内部形成了一个单独的作用域，可以封装一些外部无法读取的私有变量  

```js
// 写法一
var tmp = newData;
processData(tmp);
storeData(tmp);

// 写法二：完全避免了污染全局变量
(function(){
  var tmp = newData;
  processData(tmp);
  storeData(tmp);
})()
```

# `for...in`循环和数组的遍历
由于`for...in`不仅会遍历数组所有的数字键，还会遍历非数字键。因此推荐使用`for`循环或`while`循环遍历。
```js
var a = [1,2,3];
a.foo = true;

for(var key in a){
  console.log(key);
}
// 1
// 2
// 3
// foo

for(var i = 0; i < a.length; i++) {
  console.log(a[i]);
}
// 1
// 2
// 3

var i = 0;
while (i < a.length) {
  console.log(a[i]);
  i++;
}
```

# 类似数组的对象
如果一个对象所有键名都是自然数，并且有`length`属性，就称为`array-like object`。  
常见的有：函数的arguments对象、大多数 DOM 元素集、字符串。  
通过数组的slice方法，可以将它们转换为真正的数组:
```js
var arr = Array.prototype.slice.call(arrayLike);
```

另外，还可以调用call()，把数组的forEach()方法嫁接到arrayLike上面调用，但是这比使用数组原生的forEach要慢：
```js
function print(value, index) {
  console.log(index + ' : ' + value);
}

Array.prototype.forEach.call(arrayLike, print);
```