# `Object.prototype.toString()` 应用
通常我们可以使用 `toString` 方法得到类型字符串，但是由于实例对象可能会自定义 `toString()` 方法，覆盖掉原方法的实现，所以最好直接使用 `Object.prototype.toString` 方法，通过该函数的 `call` 方法获得类型字符串。另外，`typeof` 函数尽管可以判断简单类型，但对复杂数据类型判断比较模糊，如：数组对象、时间对象、正则对象、Math对象都是返回Object。  
因此，可以基于`toString()`函数实现类型获取，如下：
```js
var type = function(o) {
    var s = Object.prototype.toString.call(o);
    return s.match(/\[object (.*?)\]/)[1].toLowerCase();
};
```

在此基础上，可以拓展为专门判断某种类型数据的方法，如下：
```js
var type = function(o) {
    var s = Object.prototype.toString.call(o);
    return s.match(/\[object (.*?)\]/)[1].toLowerCase();
};

['Null', 'Undefined', 'Object', 'Array', 'String', 'Number', 'Boolean', 'Function', 'Regexp'].forEach(function(t) {
    type['is'+t] = function(o) {
        return type(o) === t.toLowerCase();
    };
});

type.isObject({}) // true
type.isNumber(NaN) // true
type.isRegExp(/abc/) // true
```
