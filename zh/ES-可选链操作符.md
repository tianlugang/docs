# 可选链操作符

`?.` 可选链操作符 ,链式读取对象的属性或者使整个对象链返回 undefined。?. 运算符的作用与 . 运算符类似，不同之处在于，如果对象链上的引用是 nullish (null 或者 undefined)，. 操作符会抛出一个错误，而 ?. 操作符则会按照短路计算的方式进行处理，返回 undefined。可选链操作符也可用于函数调用，如果操作符前的函数不存在，也将会返回 undefined。

* 可选链不能用于赋值
* 短路原则

```js
    let potentiallyNullObj = null;
    let x = 0;
    let prop = potentiallyNullObj?.[x++];

    console.log(x); // x 将不会被递增，依旧输出 0
```
