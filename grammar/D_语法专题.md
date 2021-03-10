# switch...case 结构  
```js
// switch...case 结构
function doAction(action) {
  switch (action) {
    case 'hack':
      return 'hack';
    case 'slash':
      return 'slash';
    case 'run':
      return 'run';
    default:
      throw new Error('Invalid action.');
  }
}
```
建议使用  
--->
```js
// 对象结构
function doAction(action) {
    var action = {
        'hack': function () {
            return 'hack';
        },
        'slash': function () {
            return 'slash';
        },
        'run': function () {
            return 'run';
        }
    }
}
```  
建议使用对象结构，主要有以下两点原因：  
1. 每个`case`的最后一行需要`break`（除return外），既容易遗忘，又容易造成代码冗长。  
2. `switch...case`不使用大括号，不利于代码形式统一，使代码较为混乱，不符合面向对象的原则。  

# console 对象的静态方法
`console`对象的所有方法都可以被覆盖，因此可以根据需求自定义，例如：
```js
['log', 'info', 'warn', 'error'].forEach(function(method) {
  console[method] = console[method].bind(
    console,
    new Date().toISOString()
  );
});

console.log("出错了！");
// 2014-05-18T09:00.000Z 出错了！
```

# 计时操作
`time`方法表示计时开始，`timeEnd`方法表示计时结束。它们的参数是计时器的名称。调用`timeEnd`方法之后，控制台的显示格式为“$计时器名称: $所耗费的时间”。
```js
console.time('Array initialize');

var array= new Array(1000000);
for (var i = array.length - 1; i >= 0; i--) {
  array[i] = new Object();
};

console.timeEnd('Array initialize');
// Array initialize: 16767.736083984375 ms
```
