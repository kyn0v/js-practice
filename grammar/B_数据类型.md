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
  


