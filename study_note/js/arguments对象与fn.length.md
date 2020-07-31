**arguments** 是一个对应于传递给函数的参数的类数组对象.

arguments对象是所有（非箭头）函数中都可用的局部变量。

你可以使用arguments对象在函数中引用函数的参数

arguments对象不是一个 Array 。

它类似于Array，但除了length属性和索引元素之外没有任何Array属性。

例如，它没有 pop 方法。但是它可以被**转换为一个真正的Array**：

```
var args = Array.prototype.slice.call(arguments); //方式1

var args = [].slice.call(arguments); //方式2

var args = (arguments.length === 1 ? [arguments[0]] : Array.apply(null, arguments)); //方式5

// ES2015
const args = Array.from(arguments); //方式3

const args = [...arguments];    //方式4
```

