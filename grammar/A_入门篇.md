# 标签（label）  
JS允许语句前面有标签（label），相当于定位符，用于跳转到程序的任意位置，例如：
```js
top:
    for (var i = 0; i < 3; i++){
    for (var j = 0; j < 3; j++){
      if (i === 1 && j === 1) break top;
      console.log('i=' + i + ', j=' + j);
    }
  }
// output:
// i=0, j=0
// i=0, j=1
// i=0, j=2
// i=1, j=0
```  

```js
top:
  for (var i = 0; i < 3; i++){
    for (var j = 0; j < 3; j++){
      if (i === 1 && j === 1) continue top;
      console.log('i=' + i + ', j=' + j);
    }
  }
// output:
// i=0, j=0
// i=0, j=1
// i=0, j=2
// i=1, j=0
// i=2, j=0
// i=2, j=1
// i=2, j=2
```