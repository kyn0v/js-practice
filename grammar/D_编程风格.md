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
1. 每个`case`的最后一行需要`break`，既容易遗忘，又容易造成代码冗长。  
2. `switch...case`不使用大括号，不利于代码形式统一，使代码较为混乱，不符合面向对象的原则。  