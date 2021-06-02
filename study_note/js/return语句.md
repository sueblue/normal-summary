**return**语句**终止函数的执行**，并返回一个指定的值给函数调用者。


### 1. 中断一个函数的执行
``` javascript
function counter() {
  for (var count = 1; ; count++) {  // 无限循环
    console.log(count + "A"); // 执行5次
      if (count === 5) {
        return;
      }
      console.log(count + "B");  // 执行4次
    }
  console.log(count + "C");  // 永远不会执行
}

counter();

// Output:
// 1A
// 1B
// 2A
// 2B
// 3A
// 3B
// 4A
// 4B
// 5A
```
### 2.返回一个函数 
```javascript
function magic(x) {
  return function calc(x) { return x * 42};
}

var answer = magic();
answer(1337); // 56154
```
这里使用return函数涉及到一个闭包的概念
