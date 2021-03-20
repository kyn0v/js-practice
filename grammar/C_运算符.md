# 位运算符的开关作用
```js
var flags = 5; // 二进制的0101
var FLAG_A = 1; // 0001
var FLAG_B = 2; // 0010
var FLAG_C = 4; // 0100
var FLAG_D = 8; // 1000

if (flags & FLAG_C) {
  // ...
}
// 0101 & 0100 => 0100 => true
// 检查开关C是否打开

var mask = FLAG_A | FLAG_B | FLAG_D;
flags = flags | mask;
// 0001 | 0010 | 1000 => 1011
// 确保打开ABD

flags = flags & mask;
// 确保只打开ABD
```