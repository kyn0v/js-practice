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

# 元属性
属性描述对象的各个属性称为“元属性”，因为它们可以看作是控制属性的属性。
- `value`属性是对象属性的值
- `writable`属性决定了`value`是否可以被改变
    ```js
    var proto = Object.defineProperty({}, 'foo', {
        value: 'a',
        writable: false
    });
    
    // obj继承原型对象proto
    var obj = Object.create(proto);

    obj.foo = 'b';
    obj.foo // 'a'

    // 可以通过覆盖属性描述对象来规避这一限制。原因是这种情况下，原型链会被完全忽视。
    Object.defineProperty(obj, 'foo', {
        value: 'b'
    });
    obj.foo // 'b'
    ```
- `enumerable` 表示目标属性是否可遍历  
    `for...in` 循环是基于 `in` 运算符的，但是 `in` 运算符不管某个属性是对象自身的还是继承的，都会返回 `true`。这显然不太合理，因此后来引入 `可遍历性` 的概念，只有可遍历的属性才会被遍历，同时还规定继承的原生属性都是不可遍历的，以保证 `for...in` 循环的可用性。  
    具体说，`for..in循环`、`Object.keys方法`、`JSON.stringify方法`不会取到 `enumerable` 为 `false` 的属性。因此可以用来设置“秘密”属性，如果对象的JSON格式输出要排除某些属性，就可以把这些属性的`enumerable`设置为`false`。
- `configurable`决定了是否可以修改属性描述对象，也决定了目标属性是否可以被删除。  

# 存取器
写法一：属性`p`的`configurable`和`enumerable`都为`false`，从而导致属性`p`是不可遍历的。
```js
var obj = Object.defineProperty({}, 'p', {
    get: function(){
        return 'getter';
    },
    set: function(value){
        console.log('setter: '+ value);
    }
});

obj.p // "getter"
obj.p = 123 // "setter: 123"
```

写法二（更常用）：属性`p`的`configurable`和`enumerable`都为true，因此属性p是可遍历的
```js
var obj = {
    get p() {
        return 'getter';
    },
    set p(value) {
        console.log('setter: ' + value);
    }
}
```

取值函数`get`不能接受参数，存值函数`set`只能接受一个参数（即属性的值）。存储器通常用于属性的值依赖对象内部数据的场合。例如：
```js
var obj = {
    $n : 5,
    get next(){return this.$n++},
    set next(n){
        if(n>this.$n) this.$n = n;
        else throw new Error('新的值必须大于当前值')
    }
}

obj.next // 5

obj.next = 10;
obj.next // 10;

obj.next = 5;
```

# 对象的拷贝
```js
var extend = function(to, from){
    for(var property in from){
        // 用来过滤掉继承的属性，因为getOwnPropertyDescriptor读不到继承属性的属性描述对象
        if(!from.hasOwnPropertyproperty(property)) continue;
        Object.defineProperty(
            to,
            property,
            Object.getOwnPropertyDescriptor(from, property)
        );
    }
    return to;
}

extend({}, {get a(){return 1}}).a // 1
```

# 对象控制状态
- `Object.preventExtensions()`使对象无法添加新的属性
- `Object.seal()`使对象无法添加也无法删除属性
- `Object.freeze()`使对象无法添加、删除和改变属性，使对象实际变成常量  

上述方法锁定对象可写性有两个局限：
- 可以通过改变原型对象，来为对象增加属性
- 只能冻结属性指向的对象，而不能冻结对象本身的内容

# 自定义排序
如果自定义排序函数的返回值大于0，表示第一个成员排在第二个成员后面；其他情况下，都是第一个元素排在第二个元素前面。
```js
[
  { name: "张三", age: 30 },
  { name: "李四", age: 24 },
  { name: "王五", age: 28  }
].sort(function (o1, o2) {
  return o1.age - o2.age;
})
```

# `map()` 函数和`forEach()`函数
`map()`将数组的所有成员依次传入参数函数，然后把每一次的执行结果组成一个新数组返回。`map` 方法可以接收两个参数：
- 第一个参数为自定义函数，该函数调用时，map方法向它传入三个参数：当前成员、当前位置和数组本身
- 第二个参数用来用来绑定回调函数内部的`this`变量  
  ```js
  var arr = ['a', 'b', 'c'];
    [1, 2].map(function (e) {
    return this[e];
    }, arr)
    // ['b', 'c']
  ```
`forEach()`和`map()`的区别在于前者不返回值，只用来操作数据。  

# Array其他实例方法
- `filter()`
- `some()`, `every()`
- `reduce()`, `reduceRight()`
- `indexOf()`, `lastIndexOf()`

# 数组方法的链式使用
```js
var users = [
  {name: 'tom', email: 'tom@example.com'},
  {name: 'peter', email: 'peter@example.com'}
];

users
.map(function (user) {
  return user.email;
})
.filter(function (email) {
  return /^t/.test(email);
})
.forEach(function (email) {
  console.log(email);
});
// "tom@example.com"
```  

# Number 添加自定义方法
```js
Number.prototype.iterate = function () {
  var result = [];
  for (var i = 0; i <= this; i++) {
    result.push(i);
  }
  return result;
};

(8).iterate()
// [0, 1, 2, 3, 4, 5, 6, 7, 8]
```

# RegExp 对象
- `exec()`+`g`：实现多次匹配
  ```js
  var reg = /a/g;
  var str = 'abc_abc_abc'

  while(true){
    var match = reg.exec(str);
    if(!match) break;
    console.log('#' + match.index + ':'  + match[0]);
  }
  ```

- `String.prototype.replace() `
  ```js
  // 例1：消除首位空格
  var str = '  #id div.class  '
  str.replace(/^\s+|\s+$/g, '')
  // "#id div.class"

  // 例2：$指代所替换的内容
  'hello world'.replace(/(\w+)\s(\w+)/, '$2 $1')
  // "world hello"(将匹配的组互换位置)
  
  'abc'.replace('b', '[$`-$&-$\']')
  // "a[a-b-c]c"（改写匹配的值）

  // 例3：函数作为参数，将匹配内容替换为函数返回值
  '3 and 5'.replace(/[0-9]+/g, function (match){
    return match * 2;
  })
  // "6 and 10"

  var a = 'The quick brown fox jumped over the lazy dog.';
  var pattern = /quick|brown|lazy/ig;
  a.replace(pattern, function replacer(match) {
    return match.toUpperCase();
  });
  // "The QUICK BROWN fox jumped over the LAZY dog."

  // 例4：网页模板替换实例
  var prices = {
    'p1': '$1.99',
    'p2': '$9.99',
    'p3': '$5.00'
  };
  var template = '<span id="p1"></span>'
    + '<span id="p2"></span>'
    + '<span id="p3"></span>';
  template.replace(
    /(<span id=")(.*?)(">)(<\/span>)/g,
    function(match, $1, $2, $3, $4){
      return $1 + $2 + $3 + prices[$2] + $4;
    }
  );
  // "<span id="p1">$1.99</span><span id="p2">$9.99</span><span id="p3">$5.00</span>"

  // 注：正则默认是贪婪匹配
  ```
  
