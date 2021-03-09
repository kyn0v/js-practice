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